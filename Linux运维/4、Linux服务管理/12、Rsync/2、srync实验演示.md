

1、 ssh 协议 数据同步 ： 将 NFS 服务器数据同步 备份到 rsync 服务器


2、rsync 协议 数据同步 ： 将 NFS 服务器数据同步 备份到rsync 服务器


1.NFS服务器创建目录：/filesrc ;   rsync服务器上创建目录：/filedst;


2.搭建 rsync 服务（ 仅需要在 NFS 服务器上搭建即可）


a.创建主配置文件（ /etc/rsyncd.conf）


b.创建认证所需账户密码文件


c.启动服务


d.设置映射用户对共享目录有权限（r）


下行同步 （ 下载 ）


上行同步 （ 上传 ）


3. 配置 rsync+inotify 实时同步


 inotify 简介


inotify 部署


inotifywait 命令 格式


利用 rsynct +inotifywait 结合脚本实现单 向实时同步


实验结果验证


4、配置 unison+inotify 实现 双向实时同步


1. 环境要求


2. 安装 步骤


1）先安装 inotify


2）再安装 ocaml


3)安装 unison


3.配置脚本


filesrc 端 ：


filedst 端 ：


4.测试


rsync 实验演示


我们一般使用 rsync 来进行单向数据同步，因此我们需要确定一个基准，比如：两台服务器，一


台 NFS 作为网站数据服务器（基准服务器），另外一台专门做 rsync 数据备份服务器，我们以此为基


础开始我们的实验。


# 1、 ssh 协议 数据同步 ： 将 NFS 服务器数据同步 备份到 rsync 服务器


实验环境：一台 NFS 服务器，一台 rsync 服务器


NFS服务器创建目录：/filesrc ;   rsync服务器上创建目录：/filedst;


下行同 步（下 载）


格式：rsync  -avz 服务器地址:/服务器目录/* /本地目录


```javascript
示例：rsync -avz root@192.168.88.10:/filesrc/* /filedst
-a：归档模式，递归并保留对象属性
-v：显示同步过程
-z：在传输文件时进行压缩
```

例子：

```javascript
[root@localhost filedst]# rsync -avz root@192.168.1.106:/filesrc/* ./
root@192.168.1.106's password: 		#输入密码
receiving incremental file list
1.txt
2.txt
3.txt
4.txt

sent 87 bytes  received 209 bytes  65.78 bytes/sec
total size is 0  speedup is 0.00
[root@localhost filedst]# ls
1.txt  2.txt  3.txt  4.txt

```

上行同步（上传）


格式：rsync  -avz /本地目录/* 服务器地址:/服务器目录


```javascript
示例：rsync -avz /filedst/*  root@192.168.88.10:/filesrc
```

注意：使用 root 用户进行实验可以，但生产环境中尽量使用单独创建的普通用户，减少权限溢出


创建用来做数据同步的用户，并给予用户对目录的相应权限，一般使用 ACL 设置权限


```javascript
# useradd zhangsan
# passwd zhangsan
# setfacl -m u:zhangsan:rwx /filesrc
```

拓展：若要实现免密码数据同步，只需要做好 ssh 密钥对登录即可




---

# 2、rsync 协议 数据同步 ： 将 NFS 服务器数据同步 备份到rsync 服务器


实验环境：一台服务器，一台客户端


## 1.NFS服务器创建目录：/filesrc ;   rsync服务器上创建目录：/filedst;


## 2.搭建 rsync 服务（ 仅需要在 NFS 服务器上搭建即可）


### a.创建主配置文件（ /etc/rsyncd.conf）


```javascript
address = 192.168.88.10 		#rsync 服务绑定 IP
port 873 				#默认服务端口 873
log file = /var/log/rsyncd.log	#日志文件位置
pid file = /var/run/rsyncd.pid 	#进程号文件位置
[web] 				#共享名：用来连接是写在 url 上的，切记
    comment = web directory backup #共享描述话语
    path = /filesrc 		#实际共享目录
    read only = no			#是否仅允许读取
    dont compress = *.gz *.bz2 	#哪些文件类型不进行压缩

    auth users = user1 		#登录用户名（非系统用户，需要自行创建）
    secrets file = /etc/rsyncd_users.db #认证所需账户密码文件（需自行创建-同上）
```

### b.创建认证所需账户密码文件


```javascript
# vim /etc/rsyncd_users.db
user1:123456
# chmod 600 /etc/rsyncd_users.db 必须修改权限，否则登录报错
```

### c.启动服务


```javascript
# rsync --daemon
# netstat -antp | grep :873
```

### d.设置映射用户对共享目录有权限（r）


```javascript
# setfacl -m u:nobody:rwx /filesrc
```

注意：关闭服务可使用 kill 命令，但偶尔会造成服务被结束，但进程号配置文件不被删除的问题，


若遇到此类问题可自己手动删除，再启动则正常（建议自己写一个 rsync 的服务管理脚本）




## 下行同步 （ 下载 ）


格式：rsync -avz rsync://用户名@服务器地址/共享模块名 /本地目录


```javascript
示例：rsync -avz rsync://user1@192.168.88.10/web /filedst
```

拓展：--delete：删除本地比服务器多出来的文件（源地址没有，目标地址有的删掉）


rsync -avz --delete rsync://user1@192.168.88.10/web /filedst




## 上行同步 （ 上传 ）


格式：rsync -avz /本地目录/* rsync://用户名@服务器地址/共享模块名


```javascript
示例：rsync -avz /filedst/* rsync://user1@192.168.88.10/web
```

拓展：rsync 协议的免密码可以借助一个环境变量实现


# export RSYNC_PASSWORD=虚拟用户密码（客户端生成）




---

# 3. 配置 rsync+inotify 实时同步


- 定期同步的缺点：


- 执行备份的时间固定，延期明显，实时性差


- 当同步源长期不变化时，密集的定期任务是不必要的（浪费资源）


- 实时同步的优点：


- 一旦同步源出现变化，立即启动备份，实时性好


- 只要同步源无变化，则不执行备份，节省资源




##  inotify 简介


inotify 是一个 Linux 内核特性，它监控文件系统，并且及时向专门的应用程序发出相关的事


件警告，比如删除、读、写和卸载操作等。要使用 inotify，必须具备一台带有 2.6.13 版本的内核操


作系统。


inotify 两个监控命令：


```javascript
inotifywait:	用于持续监控，实时输出结果（常用）
inotifywatch：	用于短期监控，任务完成后再出结果
```






## inotify 部署


```javascript
# yum -y install gcc*
# tar -xf inotify-tools-3.14.tar.gz
# cd inotify-tools-3.14
# ./configure && make && make install
```



## inotifywait 命令 格式


格式：inotifywait -mrq -e 监控动作 1,监控动作 2 /监控目录 &


```javascript
示例：inotifywait -mrq -e create,delete /filesrc &
-m：始终保持事件监听状态
-r：递归查询目录
-q：只打印监控事件的信息
```

监控动作：modify（内容）,create,attrib（权限）,move,delete




## 利用 rsynct +inotifywait 结合脚本实现单 向实时同步


```javascript
# vim src.sh
#!/bin/bash
a="inotifywait -mrq -e create,delete /filesrc"
b="rsync -avz /filesrc/* root@192.168.88.20:/filedst"
$a | while read directory event file #while 判断是否接收到监控记录
do
$b
done
```

注：用户登录时要求免密码验证




## 实验结果验证


在服务器端创建，删除文件，查看备份端是否正常




拓展：调整 inotify 监控的文件数量


```javascript
调整 inotify 内核参数（/etc/sysctl.conf）：
mak_queue_events 		监控队列大小
mak_user_instances 	最多监控实例数
max_user_watches 		每个实例最多监控文件数
```



---

# 4、配置 unison+inotify 实现 双向实时同步


rsync 在单向同步上支持的非常好，且效率很高，但是在双向同步支持较差；unison 则是双向同步的


优秀工具，但其缺点是同步效率较低。




## 1. 环境要求


1）准备好同步所需的两个目录





2）如若用 root 来实现登录的话，生成密钥对，以便于免密码验证


3）准备好 inotify 和 unison 的软件包




## 2. 安装 步骤


### 1）先安装 inotify


```javascript
# tar -xf inotify-tools-3.14.tar.gz
# cd inotify-tools-3.14
# ./configure && make && make install
```

### 2）再安装 ocaml


```javascript
# tar -xf ocaml-3.10.1.tar.gz
# cd ocaml-3.10.1
# ./configure #忽略所有报错
# make world opt
# make install
```

### 3)安装 unison


```javascript
# tar -xf unison-2.13.16.tar.gz
# cd unison-2.13.16
# make UISTYLE=text THREADS=true STATIC=true #已经存在Makefile文件，不需要./configure
# cp unison /usr/local/bin/ #把生成的脚本拷贝出来
```

注意 ： 同样的操作在服务器端也做一遍 。


## 3.配置脚本


注：双向自动同步，监 控目录 和 数据 同步时， 源目录不能使用* * 通配 符 传输，否则会变成死循环。


## filesrc 端 ：


```javascript
#!/bin/bash
a="inotifywait -mrq -e create,delete /filesrc"
b="/usr/local/bin/unison -batch /filesrc/ ssh://192.168.88.20//filedst/" #-batch：批处理
$a | while read directory event file
do
$b
done
```

### filedst 端 ：


```javascript
#!/bin/bash
a="inotifywait -mrq -e create,delete /filedst"
b="/usr/local/bin/unison -batch /filedst/ ssh://192.168.88.10//filesrc/" #-batch：批处理
$a | while read directory event file
do
$b
done
```

## 4.测试


讲两个脚本放入后台执行：bash src.sh &


分别在两个主机上创建文件查看是否可以实现双向实时同步（可能会有延迟）