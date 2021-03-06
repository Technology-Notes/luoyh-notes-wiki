### kubeadm

```
$> # 设置aliyun的docker yum源
$> curl -lO https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
$> mv docker-ce.repo /etc/yum.repos.d/
$> # 查找docker, 显示所有版本
$> yum list docker-ce --showduplicates 
$> yum -y install docker-ce-18.06.3.ce-3.el7
$> systemctl start docker # systemctl enable docker
$> # kubernetes yum源, 可禁用gpgcheck和repo_gpgcheck, 不然会出现签名错误
$> cat <<EOF > /etc/yum.repos.d/kubernetes.repo
   [kubernetes]
   name=Kubernetes
   baseurl=https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64/
   enabled=1
   gpgcheck=1
   repo_gpgcheck=1
   gpgkey=https://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg ttps://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg
   EOF
$> # 可指定版本, 如kubelet-1.14.1
$> yum install -y kubelet kubeadm kubectl
$> kubeadm init \
   --apiserver-advertise-address=192.168.11.252 \
   --image-repository registry.aliyuncs.com/google_containers \
   --kubernetes-version v1.14.1 \
   --service-cidr=10.1.0.0/16 \
   --pod-network-cidr=10.244.0.0/16
$> # 创建nginx pods   
$> kubectl create deployment nginx --image=nginx   
$> kubectl expose deployment nginx --port=80 --type=NodePort
$> # 删除pods
$> kubectl delete pods nginx-65f88748fd-zsb6c --force
$> # 查看pods, [-n namespace] 指定命令空间, svc查看随机端口
$> kubectl get pods[,svc]
$> # dashboard
$> kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/master/aio/deploy/recommended/kubernetes-dashboard.yaml
$> # flannel
$> kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/a70459be0084506e4ec919aa1c114638878db11b/Documentation/kube-flannel.yml
$> # dashboard 帐号, token
$> kubectl create serviceaccount dashboard-admin -n kube-system
$> kubectl create clusterrolebinding dashboard-admin --clusterrole=cluster-admin --serviceaccount=kube-system:dashboard-admin
$> kubectl get secret -n kube-system
$> kubectl describe secret dashboard-admin-token-7pc9h -n kube-system
$> # 运行应用安装到master
$> kubectl taint nodes --all node-role.kubernetes.io/master-

```

### minikube

```

Kubernetes : Minikube : Install2015/12/13
 	
Configure Kubernetes which is Docker Container Orchestration System.
On this exmaple, Install Minikube to configure Single Node Cluster within a Virtual machine.
[1]	Because using VM, Install a Hypervisor which is supported by Minikube.
On this example, Install KVM.
For other Hypervisors, it's possible to use VirtualBox, VMware Fusion, HyperKit.
[root@dlp ~]# yum -y install qemu-kvm libvirt libvirt-daemon-kvm
[root@dlp ~]# systemctl start libvirtd 
[root@dlp ~]# systemctl enable libvirtd 
[2]	Configure Kubernetes repository and Install Minikube.
[root@dlp ~]# cat <<'EOF' > /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-$basearch
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
EOF

[root@dlp ~]# yum -y install kubectl
[root@dlp ~]# wget https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64 -O minikube 
[root@dlp ~]# wget https://storage.googleapis.com/minikube/releases/latest/docker-machine-driver-kvm2 
[root@dlp ~]# chmod 755 minikube docker-machine-driver-kvm2 
[root@dlp ~]# mv minikube docker-machine-driver-kvm2 /usr/local/bin/
[root@dlp ~]# minikube version 
minikube version: v0.25.2
[root@dlp ~]# kubectl version -o json 
{
  "clientVersion": {
    "major": "1",
    "minor": "10",
    "gitVersion": "v1.10.0",
    "gitCommit": "fc32d2f3698e36b93322a3465f63a14e9f0eaead",
    "gitTreeState": "clean",
    "buildDate": "2018-03-26T16:55:54Z",
    "goVersion": "go1.9.3",
    "compiler": "gc",
    "platform": "linux/amd64"
  }
}

# start minikube
[root@dlp ~]# minikube start --vm-driver kvm2 
Starting local Kubernetes v1.9.4 cluster...
Starting VM...
Downloading Minikube ISO
 142.22 MB / 142.22 MB  100.00% 0s
Getting VM IP address...
Moving files into cluster...
Downloading localkube binary
 163.02 MB / 163.02 MB  100.00% 0s
 65 B / 65 B  100.00% 0s
Setting up certs...
Connecting to cluster...
Setting up kubeconfig...
Starting cluster components...
Kubectl is now configured to use the cluster.
Loading cached images from config file.

# show status
[root@dlp ~]# minikube status 
minikube: Running
cluster: Running
kubectl: Correctly Configured: pointing to minikube-vm at 192.168.39.110
[root@dlp ~]# minikube service list 
|-------------|----------------------|-----------------------------|
|  NAMESPACE  |         NAME         |             URL             |
|-------------|----------------------|-----------------------------|
| default     | kubernetes           | No node port                |
| kube-system | kube-dns             | No node port                |
| kube-system | kubernetes-dashboard | http://192.168.39.110:30000 |
|-------------|----------------------|-----------------------------|

[root@dlp ~]# minikube docker-env 
export DOCKER_TLS_VERIFY="1"
export DOCKER_HOST="tcp://192.168.39.110:2376"
export DOCKER_CERT_PATH="/root/.minikube/certs"
export DOCKER_API_VERSION="1.23"
# Run this command to configure your shell:
# eval $(minikube docker-env)

[root@dlp ~]# kubectl cluster-info 
Kubernetes master is running at https://192.168.39.110:8443

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.

[root@dlp ~]# kubectl get nodes 
NAME       STATUS    ROLES     AGE       VERSION
minikube   Ready     <none>    1h        v1.9.4

# a VM [minikube] is just running
[root@dlp ~]# virsh list 
 Id    Name                           State
----------------------------------------------------
 1     minikube                       running
 
# possible to access with SSH to the VM
[root@dlp ~]# minikube ssh 
                         _             _
            _         _ ( )           ( )
  ___ ___  (_)  ___  (_)| |/')  _   _ | |_      __
/' _ ` _ `\| |/' _ `\| || , <  ( ) ( )| '_`\  /'__`\
| ( ) ( ) || || ( ) || || |\`\ | (_) || |_) )(  ___/
(_) (_) (_)(_)(_) (_)(_)(_) (_)`\___/'(_,__/'`\____)

$ hostname 
minikube
$ docker ps 
CONTAINER ID IMAGE                             COMMAND                CREATED     STATUS  PORTS NAMES
7d769e956904 fed89e8b4248                      "/sidecar --v=2 --..." 3 hours ago Up 3 ho       k8s_si..
39680ff90ecf 459944ce8cc4                      "/dnsmasq-nanny -v..." 3 hours ago Up 3 ho       k8s_dn..
e568a2ac6088 512cd7425a73                      "/kube-dns --domai..." 3 hours ago Up 3 ho       k8s_ku..
fd9bd248d82f e94d2f21bc0c                      "/dashboard --inse..." 3 hours ago Up 3 ho       k8s_ku..
f2c59bf5dd0a gcr.io/k8s-minikube/storage-prer  "/storage-provisioner" 3 hours ago Up 3 ho       k8s_st..
203ac12b2138 gcr.io/google_containers/pause3.0 "/pause"               3 hours ago Up 3 ho       k8s_PO..
6d88074f01dc gcr.io/google_containers/pause3.0 "/pause"               3 hours ago Up 3 ho       k8s_PO..
d44ea81b29f1 gcr.io/google_containers/pause3.0 "/pause"               3 hours ago Up 3 ho       k8s_PO..
3b26e522321b d166ffa9201a                      "/opt/kube-addons.sh"  3 hours ago Up 3 ho       k8s_ku..
5a0a0833416d gcr.io/google_containers/pause3.0 "/pause"               3 hours ago Up 3 ho       k8s_PO..

$ exit
# to stop minikube, do like follows
[root@dlp ~]# minikube stop 
Stopping local Kubernetes cluster...
Machine stopped.
# to remove minikube, do like follows
[root@dlp ~]# minikube delete 
Deleting local Kubernetes cluster...
Machine deleted.
[root@dlp ~]# virsh list --all 
 Id    Name                           State
----------------------------------------------------


```