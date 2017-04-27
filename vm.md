# VMware workstation setting 

install ...


```
edit -> vitural network editor -> 
找到 subnet address
设置ip为上面的.1
设置桥接

```

> 配置linux vim /etc/sysconfig/network-scripts/ifcfg-eth0 

> static

```
DEVICE=eth0
HWADDR=00:0C:29:26:D7:7F
TYPE=Ethernet
UUID=bbcf3d5a-ef85-43bd-93ab-33e9465e1e42
ONBOOT=yes
NM_CONTROLLED=yes
BOOTPROTO=static
IPADDR=192.168.1.211

DNS1=192.168.1.1
NETMASK=255.255.255.0
GATEWAY=192.168.1.1
```
> service network restart

> dhcp

```
DEVICE=eth0
HWADDR=08:00:27:E1:7C:BB
TYPE=Ethernet
UUID=d791c482-e677-47eb-aba4-ab1f5f378bfc
ONBOOT=yes
NM_CONTROLLED=no
BOOTPROTO=dhcp


```

```
$ parted
 mklabel gpt
 mkpart ...
 quit
$ mkfs.ext4 /dev/sdb1
$ mount /dev/sdb1 /home/data1

```

