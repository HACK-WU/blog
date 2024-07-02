# **1.RPM包升级**

```javascript
[root@localhost ~]# rpm –Uvh 包全名
选项：
-U（大写） (update,更新数据)升级安装，如果没有安装过，系统直接安装。如果安装过的版本较旧，则
升级到新版本（upgrade）
[root@localhost ~]# rpm –Fvh 包全名
选项：
-F（大写） (freshen,刷新)升级安装，如果没有安装过，则不会安装。必须安装有较旧版本，才能升级（freshen）
```

# **2、卸载**

```javascript
[root@localhost ~]# rpm -e 包名
选项：
--nodeps 不检查依赖性
-e 卸载	#erase，擦去
```

# **3、查询**

## **1）查询软件包是否安装
**

可以查询软件包是否安装，命令格式如下：


```javascript
[root@localhost ~]# rpm –q 包名
选项：
-q： 查询（query）
rpm ‐q [query‐options]
    ‐a：(all,所有)查询所有安装包
    ‐f：（file，文件）查询指定的文件由哪个程序包安装生成
    ‐‐whatprovide CAPABILITY：查询指定的CAP包由哪个包所提供
    ‐‐whatrequires CAPABILITY：查询指定的CAP包被哪个包所依赖
    ‐‐changelog：查询rpm包的changelog
    ‐c：(config,配置)查询程序的配置文件
    ‐d：(doc,文档)查询程序的文档
    ‐i：information
    ‐l：(look,查看生成的文件)查看指定的程序文件安装后生成的所有文件
    ‐‐scripts：程序包自带的脚本片段
    ‐R：查询指定的程序包所依赖的CAPABILITY
    ‐‐provides：列出指定程序包所提供的CAPABILITY
```

```javascript
# 从本地的rpm数据库
[root@localhost ~]# rpm -q vim-enhanced # 查询指定包是否安装
[root@localhost ~]# rpm -qa |grep vim-enhanced
[root@localhost ~]# rpm -ql vim-enhanced # 查找指定包安装的文件
[root@localhost ~]# rpm -qf /etc/ssh/sshd_config # 查询指定文件属于哪个rpm包
[root@localhost ~]# rpm -qi openssh-server # 查询包的information信息
[root@localhost ~]# rpm -qc openssh-server # 查询指定包的配置文件
[root@localhost ~]# rpm -qd openssh-server # 查询安装的帮助文档
# 针对未安装的包，直接从套件中查询
[root@localhost ~]# rpm -qip vim-enhanced-7.4.629-6.el7.x86_64.rpm
[root@localhost ~]# rpm -qlp vim-enhanced-7.4.629-6.el7.x86_64.rpm
[root@localhost ~]# rpm -qcp vim-enhanced-7.4.629-6.el7.x86_64.rpm
[root@localhost ~]# rpm -qdp vim-enhanced-7.4.629-6.el7.x86_64.rpm
```

## **2）查询系统中的所有安装软件包
**

可以查询 Linux 系统中所有已经安装的软件包，命令格式如下：


```javascript
[root@localhost ~]# rpm -qa
选项：
-a：所有（all）
当然，可以用管道符来查看所需的内容，比如：
[root@localhost ~]# rpm -qa | grep httpd
```

你会发现，使用“rpm -q 包名”只能查看这个包是否安装，但是使用“rpm -qa | grep 包名”


会把包含包名称的所有包都列出来。


## **3）查询软件包的详细信息
**

可以查询已经安装的某个软件包的详细信息，命令格式如下：





```javascript
[root@localhost ~]# rpm –qi 包名
选项：
-i： 查询软件信息（information）
```

也可以查询还没有安装的软件包的详细信息，命令格式如下：


```javascript
[root@localhost ~]# rpm –qip 包全名
选项：
-p： 查询没有安装的软件包（package）
```

## **4）查询软件包中的文件列表
**

可以查询已经安装的软件包中的文件列表和安装的完整目录，命令格式如下：


```javascript
[root@localhost ~]# rpm –ql 包名
选项：
-l： 列出软件包中所有的文件列表和软件所安装的目录（list）
```

那么，可以查询还没有安装的软件包中的文件列表和打算安装的位置吗？答案是可以，命令格式


如下：


```javascript
[root@localhost ~]# rpm –qlp 包全名
选项：
-p： 查询没有安装的软件包信息（package）
```

## **5）查询系统文件属于哪个 RPM 包
**

既然可以知道每个 RPM 包中的文件的安装位置，那么可以查询系统文件属于哪个 RPM 包吗？当然


可以，不过需要注意的是，手工建立的文件是不能查询的，因为这些文件不是通过 RPM 包安装的，当


然不能反向查询它属于哪个 RPM 包。命令格式如下：


```javascript
[root@localhost ~]# rpm –qf 系统文件名
选项：
-f： 查询系统文件属于哪个软件包（file）
```

## **6）查询软件包所依赖的软件包
**

查询系统中和已经安装的软件包有依赖关系的软件包，命令格式如下：


```javascript
[root@localhost ~]# rpm –qR 包名
选项：
-R： 查询软件包的依赖性（requires）
```

可以查询没有安装的软件包的依赖性吗？加“-p”选项即可。例如，查看一下还没有安装的 bind


软件包的依赖包，可以执行如下命令：


```javascript
[root@localhost ~]# rpm -qRp /mnt/cdrom/Packages/bind-9.8.2-0.10.rc1.el6.i686.rpm
```

# **4、例题**

## **1）查询系统中以c开头的软件，如何查询？**

```javascript
[root@server1 ~]# rpm -qa|grep ^c |wc -l
15
[root@server1 ~]# rpm -qa|grep ^c 
chkconfig-1.7.4-1.el7.x86_64
chrony-3.2-2.el7.x86_64
ca-certificates-2018.2.22-70.0.el7_5.noarch
coreutils-8.22-23.el7.x86_64
cpio-2.11-27.el7.x86_64
cyrus-sasl-lib-2.1.26-23.el7.x86_64
curl-7.29.0-51.el7.x86_64
cryptsetup-libs-2.0.3-3.el7.x86_64
cronie-anacron-1.4.11-19.el7.x86_64
crontabs-1.11-6.20121102git.el7.noarch
centos-release-7-6.1810.2.el7.centos.x86_64
cracklib-2.9.0-11.el7.x86_64
cracklib-dicts-2.9.0-11.el7.x86_64
centos-logos-70.0.6-3.el7.centos.noarch
cronie-1.4.11-19.el7.x86_64
```

## **2）查询httpd的配置文件位置**

```javascript
[root@server1 ~]# rpm -qc httpd
/etc/httpd/conf.d/autoindex.conf
/etc/httpd/conf.d/userdir.conf
/etc/httpd/conf.d/welcome.conf
/etc/httpd/conf.modules.d/00-base.conf
/etc/httpd/conf.modules.d/00-dav.conf
/etc/httpd/conf.modules.d/00-lua.conf
/etc/httpd/conf.modules.d/00-mpm.conf
/etc/httpd/conf.modules.d/00-proxy.conf
/etc/httpd/conf.modules.d/00-systemd.conf
/etc/httpd/conf.modules.d/01-cgi.conf
/etc/httpd/conf/httpd.conf
/etc/httpd/conf/magic
/etc/logrotate.d/httpd
/etc/sysconfig/htcacheclean
/etc/sysconfig/httpd
```

## **3)当我误删除httpd的配置文件之后，打算从新安装系统却提示，软件已经安装，这个时候，我该怎么从新安装？**

具体情况显示：

```javascript
[root@server1 ~]# systemctl start httpd.service 
Job for httpd.service failed because the control process exited with error code. See "systemctl status httpd.service" and "journalctl -xe" for details.
[root@server1 ~]# 
[root@server1 ~]# 
[root@server1 ~]# rpm -ivh httpd-2.4.6-97.el7.centos.4.x86_64.rpm 
准备中...                          ################################# [100%]
	软件包 httpd-2.4.6-97.el7.centos.4.x86_64 已经安装
```

解决办法：

```javascript
[root@server1 ~]# ls
anaconda-ks.cfg  httpd-2.4.6-97.el7.centos.4.x86_64.rpm  
[root@server1 ~]# 
[root@server1 ~]# 
[root@server1 ~]# 
[root@server1 ~]# rpm -ivh httpd-2.4.6-97.el7.centos.4.x86_64.rpm  --replacepkgs 
准备中...                          ################################# [100%]
正在升级/安装...
   1:httpd-2.4.6-97.el7.centos.4      ################################# [100%]
[root@server1 ~]# 
[root@server1 ~]# 
[root@server1 ~]# systemctl start httpd
[root@server1 ~]# systemctl status httpd
● httpd.service - The Apache HTTP Server
   Loaded: loaded (/usr/lib/systemd/system/httpd.service; disabled; vendor preset: disabled)
   Active: active (running) since 三 2022-02-09 07:38:40 GMT; 12s ago
     Docs: man:httpd(8)
           man:apachectl(8)
  Process: 26691 ExecStop=/bin/kill -WINCH ${MAINPID} (code=exited, status=1/FAILURE)
 Main PID: 26753 (httpd)
   Status: "Total requests: 0; Current requests/sec: 0; Current traffic:   0 B/sec"
   CGroup: /system.slice/httpd.service
           ├─26753 /usr/sbin/httpd -DFOREGROUND
           ├─26754 /usr/sbin/httpd -DFOREGROUND
           ├─26755 /usr/sbin/httpd -DFOREGROUND
           ├─26756 /usr/sbin/httpd -DFOREGROUND
           ├─26757 /usr/sbin/httpd -DFOREGROUND
           └─26758 /usr/sbin/httpd -DFOREGROUND

2月 09 07:38:15 server1 systemd[1]: Starting The Apache HTTP Server...
2月 09 07:38:30 server1 httpd[26753]: AH00558: httpd: Could not reliably determine the server's fully qualified domain name, using fe80::b395:d9c3...s message
2月 09 07:38:40 server1 systemd[1]: Started The Apache HTTP Server.
Hint: Some lines were ellipsized, use -l to show in full.
[root@server1 ~]# 
```

# **5、例题**

```javascript
找出你的 Linux 是否有安装 logrotate 这个软件？	#rpm -q logrotate
列出上题当中，属于该软件所提供的所有目录与档案	#rpm -ql logrotate
列出 logrotate 这个软件的相关说明数据		#rpm -qi logrotate
找出 /bin/sh 是那个软件提供的？			#rpm -qf /bin/sh
如果我误砍了某个重要档案，例如 /etc/crontab，偏偏不晓得他属于哪一个软件，该怎么办？ #rpm -qf /etc/crontab
```