```

（1）安装环境支持
netsh interface ipv6 install

（2）查看已配置的“端口映射”清单
netsh interface portproxy show v4tov4

（3）添加“端口映射”
netsh interface portproxy add v4tov4 listenaddress=192.168.222.145 listenport=15001 connectaddress=192.168.222.63 connectport=81
将本机（192.168.222.145）的15001端口映射到192.168.222.63的81端口

（4）删除“端口映射”
netsh interface portproxy delete v4tov4 listenaddress=192.168.222.145 listenport=1500
--------------------- 

```