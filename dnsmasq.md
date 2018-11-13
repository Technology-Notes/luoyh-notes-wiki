```
#> vi /etc/dnsmasq.conf
server=/roy.xxx.com/127.0.0.1

address=/dev.xxx.com/192.168.1.11
address=/test.xxx.com/192.168.1.12
address=/ecs1.xxx.com/192.168.1.101
address=/ecs2.xxx.com/192.168.1.102
address=/ecs3.xxx.com/192.168.1.103
address=/ecs4.xxx.com/192.168.1.104

#domain=xxx.com

# or vi /etc/dnsmasq.d/address.conf
address=/dev.xxx.com/192.168.1.11
address=/test.xxx.com/192.168.1.12
address=/ecs1.xxx.com/192.168.1.101
address=/ecs2.xxx.com/192.168.1.102
address=/ecs3.xxx.com/192.168.1.103
address=/ecs4.xxx.com/192.168.1.104

#> systemctl restart dnsmasq
```


