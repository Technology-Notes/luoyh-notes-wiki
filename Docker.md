some command:

```
# pull
docker pull xxx

# run with background
docker run -dti -p 80:80 -p 81:81 -v host_path:image_path [--rm] image_id

# in container bash
docker exec -it container_id bash

# copy resource in container
docker cp host_path container_id:container_path

# inspect
docker inspect container_id

docker stop/start

docker ps [-a]

docker rm container_id

```

> build tomcat images

> dockerfile
```

FROM hub.c.163.com/library/centos
MAINTAINER luoyh
ADD jre-8u131.tar.gz /usr/local/
ADD tomcat.tar.gz /usr/local/

ENV JAVA_HOME /usr/local/jre1.8.0_131
ENV CLASSPATH $JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
ENV CATALINA_HOME /usr/local/apache-tomcat-9.0.0.M21
ENV CATALINA_BASE /usr/local/apache-tomcat-9.0.0.M21
ENV PATH $PATH:$JAVA_HOME/bin:$CATALINA_HOME/lib:$CATALINA_HOME/bin

VOLUME ["/root/datas/luoyhtomcat"]

ENTRYPOINT /usr/local/apache-tomcat-9.0.0.M21/bin/startup.sh && tail -f /usr/local/apache-tomcat-9.0.0.M21/logs/catalina.out

EXPOSE 8080

```
> docker build -t luoyh/tomcat --rm=true .

> run command
```
docker run -dti \
-p 8080:8080 \
-v /root/datas/tomcat/webapps:/usr/local/apache-tomcat-9.0.0.M21/webapps \
-v /root/datas/tomcat/logs:/usr/local/apache-tomcat-9.0.0.M21/logs \
luoyh/tomcat
```

### Redis On Docker

```
docker pull redis
docker run -dti --name redis -p 6379:6379 -v /var/dd/redis:/data redis 
```
> Operator Redis With Docker redis-cli

```
docker run --rm -it --link redis:redis redis /bin/bash
xxx@abccd:/data# redis-cli -h redis -p 6379
redis:6379> ...
```




### CentOS7安装docker, 并且安装CentOS7镜像


```
# 配置阿里云yum源
# backup old
> mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.bak
> wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
> yum clean all
> yum makecache 
> yum update

# 阿里云docker的yum源
> yum install -y epel-release
> yum clean all
> yum makecache 
> yum update

# 安装docker & 启动docker
> yum install -y docker
> systemctl start docker
> docker info

# 安装CentOS7镜像, 使用网易镜像, 默认安装了openssh-server,提供了ssh登录
> docker pull hub.c.163.com/public/centos:7.2-tools

# 启动镜像
> docker images 
REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
centos7/0.1                   latest              ce0d469d287b        35 minutes ago      515 MB
hub.c.163.com/public/centos   7.2-tools           4a4618db62b9        13 months ago       515 MB

> docker run -dti -p 50001:22 4a4618db62b9 /bin/bash

# 进入容器
> docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS                   NAMES
956543610f65        ce0d469d287b        "/bin/bash"         36 minutes ago      Up 36 minutes       0.0.0.0:50001->22/tcp   cocky_leakey
> docker attach 956543610f65
# 镜像CentOS设置密码, 开启ssh
#> passwd root
#> /usr/sbin/sshd -D
# Ctrl + p + q

# Could not load host key: /etc/ssh/ssh_host_ecdsa_key
# Could not load host key: /etc/ssh/ssh_host_ed25519_key
#> ssh-keygen -t rsa -f /etc/ssh/ssh_host_rsa_key
#> ssh-keygen -t ecdsa -f /etc/ssh/ssh_host_ecdsa_key
#> ssh-keygen -t ed25519 -f /etc/ssh/ssh_host_ed25519_key


# 配置端口
> firewall-cmd --zone=public --add-port=50001/tcp --permanent
> firewall-cmd --reload

# 完成, 现在就可以使用ssh,端口为50001,密码是root进行登录了.

```

### change default image path on CentOS7

```
$> mkdir /etc/systemd/system/docker.service.d
$> vim /etc/systemd/system/docker.service.d/docker.conf
[Service]
ExecStart=
ExecStart=/usr/bin/dockerd --graph=/mnt/docker/image --storage-driver=overlay2
# the "overlay2" is docker info and "storage-driver" line
# the /mnt/docker/image is new path
# :wq
$> systemctl daemon-reload
$> systemctl start docker

```


