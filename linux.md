# linux

### mount

```
$> fdisk -l
$> # /dev/sdb is 
$> fdisk /dev/sdb
#> n
#> p
#> 1
#> # enter
#> # enter
#> p
#> w
$> partprobe
$> mkfs -t ext4 /dev/sdb
$> mount /dev/sdb /mnt/docker
$> df -h
$> blkid /dev/sdb
$> vim /etc/fstab
UUID=c327d2f6-32a8-47be-91a5-f49d4f1fca75 /mnt/docker ext4 defaults 0 2
:wq
$> init 6
```

```
du -ah --max-depth=1     这个是我想要的结果  a表示显示目录下所有的文件和文件夹（不含子目录），h表示以人类能看懂的方式，max-depth表示目录的深度。

du命令用来查看目录或文件所占用磁盘空间的大小。常用选项组合为：du -sh

　　一、du的功能：`du` reports the amount of disk space used by the specified files and for each subdirectory (of directory arguments). with no arguments,`du` reports the disk space for the current directory。
　　很明显，与df不同，它用来查看文件或目录所占用的磁盘空间的大小。
 　二、du常用的选项：
　　-h：以人类可读的方式显示
　　-a：显示目录占用的磁盘空间大小，还要显示其下目录和文件占用磁盘空间的大小
　　-s：显示目录占用的磁盘空间大小，不要显示其下子目录和文件占用的磁盘空间大小
　　-c：显示几个目录或文件占用的磁盘空间大小，还要统计它们的总和
　　--apparent-size：显示目录或文件自身的大小
　　-l ：统计硬链接占用磁盘空间的大小
　　-L：统计符号链接所指向的文件占用的磁盘空间大小
　　一、du -h：这个就不多说了。
　　二、du -a：使用此选项时，显示目录和目录下子目录和文件占用磁盘空间的大小。


ls -lh

```
查询指定目录文件内容包含字符串:
find ./ -type f | xargs grep -i "getLoadcurve"
```

可以尝试使用文件描述符(fd)恢复rm误删的MySQL数据文件(要求数据库没有重启)
查看哪个进程正在操作目录/var/lib/mysql
sudo lsof /var/lib/mysql
查找被删除了的文件,假设要恢复的这个文件的fd号为24
sudo ls -lh /proc/`pidof mysqld`/fd|grep deleted 
sudo cp -Lv /proc/`pidof mysqld`/fd/24 /restore/ibdata1

# uname -a # 查看内核/操作系统/CPU信息 
# head -n 1 /etc/issue # 查看操作系统版本 
# cat /proc/cpuinfo # 查看CPU信息 
# hostname # 查看计算机名 
# lspci -tv # 列出所有PCI设备 
# lsusb -tv # 列出所有USB设备 
# lsmod # 列出加载的内核模块 
# env # 查看环境变量资源 
# free -m # 查看内存使用量和交换区使用量 
# df -h # 查看各分区使用情况 
# du -sh <目录名> # 查看指定目录的大小 
# grep MemTotal /proc/meminfo # 查看内存总量 
# grep MemFree /proc/meminfo # 查看空闲内存量 
# uptime # 查看系统运行时间、用户数、负载 
# cat /proc/loadavg # 查看系统负载磁盘和分区 
# mount | column -t # 查看挂接的分区状态 
# fdisk -l # 查看所有分区 
# swapon -s # 查看所有交换分区 
# hdparm -i /dev/hda # 查看磁盘参数(仅适用于IDE设备) 
# dmesg | grep IDE # 查看启动时IDE设备检测状况网络 
# ifconfig # 查看所有网络接口的属性 
# iptables -L # 查看防火墙设置 
# route -n # 查看路由表 
# netstat -lntp # 查看所有监听端口 
# netstat -antp # 查看所有已经建立的连接 
# netstat -s # 查看网络统计信息进程 
# ps -ef # 查看所有进程 
# top # 实时显示进程状态用户 
# w # 查看活动用户 
# id <用户名> # 查看指定用户信息 
# last # 查看用户登录日志 
# cut -d: -f1 /etc/passwd # 查看系统所有用户 
# cut -d: -f1 /etc/group # 查看系统所有组 
# crontab -l # 查看当前用户的计划任务服务 
# chkconfig –list # 列出所有系统服务 
# chkconfig –list | grep on # 列出所有启动的系统服务程序 
# rpm -qa # 查看所有安装的软件包
# lsof -i:[port]8080 # find use the port pid

# find port
netstat -apn | grep 7020 

linux 
# rm -rf path
# tail -f path
# netstat -tulpen

find file by PID:
# lsof -p PID

copy and replace in VI:
# :s/src/dest/g         # replace in current line
# :%s/src/dest/g        # replace in file

查看挂载点:

# df /var/lib/
# df /var/lib/ -kh
# df -kh


```

> install by source
```
tar zxvf xx.gz
cd xx
./configure --help
// must set --prefix
make distclean -- clean configure create file

make & make install
make clean
```


```
阿里云Linux安装软件镜像源
阿里云是最近新出的一个镜像源。得益与阿里云的高速发展，这么大的需求，肯定会推出自己的镜像源。
阿里云Linux安装镜像源地址：http://mirrors.aliyun.com/
CentOS系统更换软件安装源
第一步：备份你的原镜像文件，以免出错后可以恢复。
mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup
第二步：下载新的CentOS-Base.repo 到/etc/yum.repos.d/
CentOS 5
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-5.repo
CentOS 6
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-6.repo
第三步：运行yum makecache生成缓存
yum clean all
yum makecache

```

> firewall

```
1） 临时生效，重启后复原
开启： service iptables start
关闭： service iptables stop
 
2） 永久性生效，重启后不会复原
开启： chkconfig iptables on
关闭： chkconfig iptables off
```

```
1、firewalld的基本使用
启动： systemctl start firewalld
查看状态： systemctl status firewalld 
停止： systemctl disable firewalld
禁用： systemctl stop firewalld
 
2.systemctl是CentOS7的服务管理工具中主要的工具，它融合之前service和chkconfig的功能于一体。
启动一个服务：systemctl start firewalld.service
关闭一个服务：systemctl stop firewalld.service
重启一个服务：systemctl restart firewalld.service
显示一个服务的状态：systemctl status firewalld.service
在开机时启用一个服务：systemctl enable firewalld.service
在开机时禁用一个服务：systemctl disable firewalld.service
查看服务是否开机启动：systemctl is-enabled firewalld.service
查看已启动的服务列表：systemctl list-unit-files|grep enabled
查看启动失败的服务列表：systemctl --failed

3.配置firewalld-cmd

查看版本： firewall-cmd --version
查看帮助： firewall-cmd --help
显示状态： firewall-cmd --state
查看所有打开的端口： firewall-cmd --zone=public --list-ports
更新防火墙规则： firewall-cmd --reload
查看区域信息:  firewall-cmd --get-active-zones
查看指定接口所属区域： firewall-cmd --get-zone-of-interface=eth0
拒绝所有包：firewall-cmd --panic-on
取消拒绝状态： firewall-cmd --panic-off
查看是否拒绝： firewall-cmd --query-panic
 
那怎么开启一个端口呢
添加
firewall-cmd --zone=public --add-port=80/tcp --permanent    （--permanent永久生效，没有此参数重启后失效）
重新载入
firewall-cmd --reload
查看
firewall-cmd --zone= public --query-port=80/tcp
删除
firewall-cmd --zone= public --remove-port=80/tcp --permanent

```

### addition user and group

```

创建/删除组
# groupadd/groupdel user0group


创建用户:
# useradd -d /home/user0 -m user0 -g user0group
修改密码:
# passwd user0
New password:user0password
Re-enter new password:user0password
添加目录权限
# chown user0 /home/user0

```

### 设置时区与同步时间

```

#> yum -y install ntpdate
#> ntpdate time.windows.com
#> timedatectl -- help
#> timedatectl set-timezone "Asia/Shanghai"
#> hwclock
#> hwclock -w

```

### shell **

```
# get PID, can use & and $!, eg:
java -jar xxx.jar --spring.config.location=/opt/xxx.yml &
echo $! > xxx.pid

# or
kill -9 `cat xxx.pid`
kill -9 `jps -lm | grep xxx | awk '{print $1}'` # can use ps aux
su - javaer -lc "kill -9 `jps -lm | grep xxx | awk '{print $1}'`"
echo `jps -lm | grep xxx | awk '{print $1}'` > xxx.pid

# get file name
file=/opt/xxx/xxx-1.0.jar
echo  ${file##*/} # xxx-1.0.jar

```

