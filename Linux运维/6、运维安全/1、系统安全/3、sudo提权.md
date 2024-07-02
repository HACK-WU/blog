# **一、sudo 授权     给普通用户赋予管理员权限**

## **详情解释**

**/sbin/		在此目录下只有超级用户才能使用**

**/usr/bin/**

```javascript
visudo 赋予普通用户权限命令，命令执行后和 vi 一样使用
root ALL=(ALL) ALL
#用户名 被管理主机的地址=（可使用的身份） 授权命令（绝对路径）
# %wheel ALL=(ALL) ALL
#%组名 被管理主机的地址=（可使用的身份） 授权命令（绝对路径）
```

 用户名/组名：代表 root 给哪个

 用户可以用指定的命令管理指定 IP 地址的服务器。如果写 ALL，代表可以管理任何主机，如果写固定 IP，代表用户可以管理指定的服务器。那么如果你是一台独立的服务器，这里写 ALL 和你服务器的 IP 地址，作用是一样的。而写入网段，只有对 NIS 服务这样用户和密码集中管理的服务器才有意义）。如果我们这里写本机的 IP 地址，不代表只允许本机的用户使用指定命令，而代表指定的用户可以从任何 IP 地址来管理当前服务器。


 可使用身份：就是把来源用户切换成什么身份使用，（ALL）代表可以切换成任意身份。这个字段可以省略


 授权命令：代表 root 把什么命令授权给普通用户。默认是 ALL，代表任何命令，这个当然不行。如果需要给那个命令授权，写入命令名即可，不过需要注意一定要命令写成绝对路径


**2、举例**

1）


```javascript
举个例子，比如授权用户 user1 可以重启服务器，则由 root 用户添加如下行：
[root@localhost ~]# visudo
user1 ALL= /sbin/shutdown –r now 
[user1@localhost ~]$ sudo -l		#l,look，看
#查看可用的授权
```

2)


再举个例子，授权一个用户管理你的 Web 服务器，不用自己插手是不是很爽，以后修改设置更新


网页什么都不用管，一定 Happy 死了，Look：


首先要分析授权用户管理 Apache 至少要实现哪些基本授权：


1、可以使用 Apache 管理脚本


2、可以修改 Apache 配置文件


3、可以更新网页内容


假设 Aapche 管理脚本程序为/etc/rc.d/init.d/httpd 。


为满足条件一，用 visudo 进行授权：


```javascript
[root@localhost ~]# visudo
user1 192.168.0.156=/etc/rc.d/init.d/httpd reload,\
/etc/rc.d/init.d/httpd configtest
```




授权用户 user1 可以连接 192.168.0.156 上的 Apache 服务器，通过 Apache 管理脚本重新读取配置文件让更改的设置生效（reload）和可以检测 Apache 配置文件语法错误（configtest），但不允许其执行关闭（stop）、重启（restart）等操作。（“\”的意思是一行没有完成，下面的内容和上一行是同一行。）


为满足条件二，同样使用 visudo 授权：


```javascript
[root@localhost ~]# visudo
user1 192.168.0.156=/binvi /etc/httpd/conf/httpd.conf
```

授权用户 user1 可以用 root 身份使用 vi 编辑 Apache 配置文件。


以上两种 sudo 的设置，要特别注意，很多朋友使用 sudo 会犯两个错误：第一，授权命令没有细化到选项和参数；第二，认为只能授权管理员执行的命令。


条件三则比较简单，假设网页存放目录为/var/www/html ，则只需要授权 user1 对此目录具有写权限或者索性更改目录所有者为 user1 即可。如果需要，还可以设置 user1 可以通过 FTP 等文件共享服务


更新网页。


3）授权 aa 用户可以添加其他普通用户


```javascript
aa ALL=/usr/sbin/useradd 赋予 aa 添加用户权限.命令必须写入绝对路径
aa ALL=/usr/bin/passwd 赋予改密码权限，取消对 root 的密码修改
aa ALL=/usr/bin/passwd [A-Za-z]*, !/usr/bin/passwd "", !/usr/bin/passwd root
aa 身份
sudo /usr/sbin/useradd ee 普通用户使用 sudo 命令执行超级用户命令
```

## **使用visudo授权：**

```javascript
[root@localhost ~]# visudo
## Allow root to run any commands anywhere
root    ALL=(ALL)       ALL
user1 ALL=/usr/sbin/useradd                //对user1用户进行授权；
user1 ALL=/usr/bin/passwd [A-Za-z]*, !/usr/bin/passwd "", !/usr/bin/passwd root
## Allows members of the 'sys' group to run networking, software,
## service management apps and more.
```

## **授权用户执行操作**

```javascript
[user1@localhost ~]$ 
[user1@localhost ~]$ sudo /usr/sbin/useradd  user3
[sudo] user1 的密码：
[user1@localhost ~]$ sudo /usr/bin/passwd user3
更改用户 user3 的密码 。
新的 密码：
无效的密码： 密码少于 8 个字符
重新输入新的 密码：
passwd：所有的身份验证令牌已经成功更新。
[user1@localhost ~]$ 
```

# 二、visudo

```
[root@localhost ~]# groupadd smartgo
[root@localhost ~]# useradd ito1 -G smartgo
[root@localhost ~]# useradd ito2 -G smartgo
[root@localhost ~]# id it01
uid=1003(it01）gid=1004(it01) 组=1004(it01),1003(smartgo)
[root@localhost ~]# id it02
uid=1004(it02）gid=1005(it02）组=1005(it02),1003(smartgo)
[root@localhost ~]# visudo
%smartgo    ALL=NOPASSWD: /usr/sbin/ip
%smartgo    ALL=NOPASSWD: /usr/sbin/useradd, /usr/sbin/userdel, /usr/bin/passwd
%smartgo    ALL=NOPASSWD: !/usr/bin/passwd root, !/usr/bin/passwd root --stdin ,!/usr/bin/passwd --stdin root,!/usr/bin/passwd ""
%smartgo    ALL=NOPASSWD: !/usr/bin/rm
[root@localhost ~]# su - it01
[it01@localhost ~]$ sudo passwd root
对不起 it01 无权以root 的身份在 localhost.localdomain 上执行/bjn/passwd root.
```

# 三、sudo日志