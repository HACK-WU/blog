**MySQL安装部署**

# 1、部署mariadb

## 1）安装mariadb-server

```
[root@node1 ~]# yum install mariadb mariadb-server -y 
```

## 2）初始化

```
[root@node1 ~]# systemctl start mariadb.service
[root@node1 ~]# mysql_secure_installation
Enter current password for root (enter for none): 当前root用户密码为空，所以直接
敲回车
OK, successfully used password, moving on...
Set root password? [Y/n] y 设置root密码
New password:
Re-enter new password:
Password updated successfully!
Remove anonymous users? [Y/n] y 删除匿名用户
... Success!
Disallow root login remotely? [Y/n] y 禁止root远程登录
... Success!
Remove test database and access to it? [Y/n] y 删除test数据库
- Dropping test database...
... Success!
- Removing privileges on test database...
... Success!
Reload privilege tables now? [Y/n] y 刷新授权表，让初始化生效
... Success!
```

## 3）启动并查看数据库状态

```
[root@node1 ~]# systemctl start mariadb.service
[root@node1 ~]# systemctl status mariadb.service
● mariadb.service - MariaDB database server
 Loaded: loaded (/usr/lib/systemd/system/mariadb.service; disabled; vendor
preset: disabled)
 Active: active (running) since 一 2020-09-14 22:38:17 CST; 10s ago
Process: 1634 ExecStartPost=/usr/libexec/mariadb-wait-ready $MAINPID
(code=exited, status=0/SUCCESS)
Process: 1550 ExecStartPre=/usr/libexec/mariadb-prepare-db-dir %n
(code=exited, status=0/SUCCESS)
Main PID: 1633 (mysqld_safe)
......
```

## 4)登录数据库

```
[root@node1 ~]# mysql -uroot -p
Enter password:
Welcome to the MariaDB monitor. Commands end with ; or \g.
Your MariaDB connection id is 2
MariaDB [(none)]>
```

## 5)主配置文件（主要关注每一个参数的意义，参数的值可以根据实际场景修改）

```
[root@node1 ~]# vim /etc/my.cnf
[client]  # 客户端基本配置
port     = 3306 #客户端默认连接端口
socket    = /tmp/mysql.sock  #用于本地连接的socket套接字
[mysqld]  # 服务端基本配置
port     = 3306 # mysql监听端口
socket    = /tmp/mysql.sock #为MySQL客户端程序和服务器之间的本地通讯指定一个套接
字文件
user   = mariadb # mysql启动用户
basedir = /usr/local/mariadb # 安装目录
datadir = /data/mysql # 数据库数据文件存放目录
log_error = /data/mysql/mariadb.err #记录错误日志文件
pid-file = /data/mysql/mariadb.pid  #pid所在的目录
skip-external-locking  #不使用系统锁定，要使用myisamchk,必须关闭服务器
```