# mysql install on linux with generic binaric

```
groupadd mysql
useradd -r -g mysql -s /bin/false mysql
tar zxvf mysql-5.6.33-linux-glibc2.5-x86_64.tar.gz	# 解压mysql二进制包
cd /usr/local						# 进入用户目录
mv /mysql-5.6.33-linux-glibc2.5-x86_64 mysql		# 移动解压的二进制文件夹到/usr/local/mysql
cd mysql						# 进入mysql目录
cp support-files/my-default.cnf /etc/my.cnf		# 复制mysql默认配置文件到/etc/my.cnf
vim /etc/my.cnf						# 修改/etc/my.cnf, 主要修改basedir,datadir,port
	-------------------------------- my.cnf 的内容修改 ---------------------------------------
	basedir = /usr/local/mysql
	datadir = /var/lib/mysqldata
	port = 3306
	server_id = 1
	----------------------------- :wq 保存退出 -------------------------------------------
mkdir /var/lib/mysqldata				# 新建mysql的数据目录, 也就是上面的datadir的目录
scripts/mysql_install_db --datadir=/var/lib/mysqldata --basedir=/usr/local/mysql	# 初始化mysql

shell> bin/mysql_install_db --user=mysql    # MySQL 5.7.5
shell> bin/mysqld --initialize --user=mysql # MySQL 5.7.6 and up
shell> bin/mysql_ssl_rsa_setup              # MySQL 5.7.6 and up

cp support-files/mysql.server /etc/init.d/mysqld	# 复制mysql的服务到init.d, 可以通过service命令启动
chown -R mysql.mysql /var/lib/mysqldata			# 给mysql的数据目录赋予mysql用户权限
service mysqld start					# 启动mysql服务
cp bin/mysql /usr/bin/mysql				# 复制mysql命令到环境变量下
mysql -uroot						# 第一次不需要密码
	set password=password('root');			# 设置密码
	\q						# 退出
mysql -proot						# 通过密码进入
service mysqld stop					# 停止mysql服务

```

# install mysql on windowx 

```
copy my-default.ini to my.ini
edit:
[mysqld]
basedir = /mysql
datadir = /datas/mysql
port = 3307
server_id = 330701
default_time_zone = +08:00
character-set-server=utf8

[client]
default-character-set=utf8


run cmd --> in mysq/bin
$> mysqld --install mysql3307
$> mysqld --initialize

if you have more mysql service
run regedit
HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Services\mysql3307
modify ImagePath :
mysql\bin\mysqld --defaults-file=mysql\my.ini mysql3307

$> net start mysql3307
$> mysql -uroot -p -P3307
mysql> set password = password('root');
mysql> \q

$> mysqld --remove mysql3307

```


### Backup with Xtrabackup

```
> # backup
> # if error by 'Too open many files' do this: ulimit -n 65535
> xtrabackup --backup --target-dir=/data/backups -S /data/dev/mysql.sock -p123456 [--defaults-file=/data/dev/my.cnf]
> # restore
> xtrabackup --copy-back --target-dir=/data/backups --datadir=/data/xxx
> chown -R mysql:mysql /data/xxx
> # cp /etc/init.d/mysql.server && then start

```




