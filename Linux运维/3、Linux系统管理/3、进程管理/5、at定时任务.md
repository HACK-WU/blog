

1、at 一次性执行定时任务（yum install -y at)

1）at 服务管理与访问控制

2）、 at 命令


3）、 其他 at 管理命令


2 、batch命令，在CPU负载小于0.8的时候执行。

3、写入黑面单测试，

vim /etc/at.denyect

4、/var/spool/at/目录下文件测试

at  now +5minutes	 设定一个定时任务

ll  /var/spool/at/ 查看任务文件

atq 查看任务号码

atrm 11删除任务

ll /var/spool/at 再次查看任务文件（任务文件消失）

# 1、at 一次性执行定时任务（yum install -y at)

1、当用at命令产生了一个定时任务时，这个任务的将会被以文件的形式记录在/var/spool/at/目录下，然后等待atd服务使用执行这个文件。

2、当任务执行完毕之后，这个文件将会被系统自动删除。

3、在编辑定时任务时，命令建议使用绝对路径，避免出问题。

## 1）at 服务管理与访问控制

at 命令想要正确执行，需要atd服务的支持。atd服务是独立的服务，所以启动命令是

```javascript
[root@localhost ~]# service atd start
正在启动 atd：
```

如果想要让 atd 服务开机时自动启动，可以使用如下命令：


```javascript
[root@localhost ~]# chkconfig atd on

```

Centos 7:

```javascript
[root@localhost ~]# yum install -y at	#安装at服务
[root@localhost ~]# systemctl start atd 	#启动atd服务
[root@localhost ~]# systemctl enable atd	#开机自启atd服务
[root@localhost ~]# systemclt status atd	#查看服务状态
● atd.service - Job spooling tools
   Loaded: loaded (/usr/lib/systemd/system/atd.service; enabled; vendor preset: enabled)
   Active: active (running) since 日 2022-02-06 03:36:41 GMT; 2s ago
 Main PID: 7378 (atd)
   CGroup: /system.slice/atd.service
           └─7378 /usr/sbin/atd -f

2月 06 03:36:41 server1 systemd[1]: Started Job spooling tools.
```

- atd 服务启动之后，at 命令才可以正常使用，不过我们还要学习下 at 命令的访问控制。这里的


访问控制指的是允许哪些用户使用 at 命令设定定时任务，或者不允许哪些用户使用 at 命令。大家可


以想象成为设定黑名单或设定白名单，这样更容易理解。at 的访问控制是依靠/etc/at.allow 文件（白


名单）和/etc/at.deny 文件（黑名单）这两个文件来实现的，具体规则如下：


```javascript
 如果系统中有/etc/at.allow 文件，那么只有写入/etc/at.allow 文件（白名单）中的用户
  可以使用 at 命令，其他用户不能使用 at 命令（/etc/at.deny 文件会被忽略，也就是说同一
  个用户既写入/etc/at.allow 文件，也写入/etc/at.deny 文件，那么这个用户是可以使用 at
  命令的，因为/etc/at.allow 文件优先级更高。）。
 如果系统中没有/etc/at.allow 文件，只有/etc/at.deny 文件，那么写入/etc/at.deny 文件
 （黑名单）中的用户不能使用 at 命令，其他用户可以使用 at 命令。不过这个文件对 root
用户不生效。
 如果系统中这两个文件都不存在，那么只有 root 用户可以使用 at 命令。
```

- 系统中默认时只有/etc/at.deny 文件，而且这个文件是空的，这样的话系统中所有的用户都可以


使用 at 命令。不过如果我们打算控制用户的 at 命令权限，把用户写入/etc/at.deny 文件即可




- /etc/at.allow 文件的权限更高，如果/etc/at.allow 文件存在，则/etc/at.deny 文件失效。


/etc/at.allow 管理行为更加严格，因为只有写入这个文件的用户才能使用 at 命令，如果需要禁用


at 命令的用户较多，则可以把少数用户写入这个文件。/etc/at.deny 文件的管理较为松散，如果允


许使用 at 命令的用户较多，则可以把禁用的用户写入这个文件。不过这两个文件都不能对 root 用户


生效。



## 2）、 at 命令


```javascript
[root@localhost ~]# at [选项] 时间
选项：
-m:(must,必须，必须发送)当 at 工作完成后，无论是否命令有输出，都用 email 通知执行 at命令的用户
-c:(choose,选择工作号)工作号： 显示该 at 工作的实际内容
-d:(delete,删除)相当于atrm,可以取消一个at计划中的任务
-v:(view,可见的)可以使用较明显的时间格式列出at计划中的任务列表
-l:(look,查看)列出目前系统上面的该使用者的at计划
时间：
at 支持的时间格式如下：
    -HH:MM 在指定的“小时:分钟”执行命令，例如：02:30
    -HH:MM YYYY-MM-DD 在指定的“小时:分钟 年-月-日”执行，例如 02:30 2018-07-25
    -HH:MM[am|pm] [month] [date] 在指定的“小时:分钟[上午|下午][月][日]”执行，例如 02:30 July 25
    -HH:MM[am|pm] + [minutes|hours|days|weeks] 在指定的时间“再加多久执行”，例如 now + 5 minutes，05am +2 hours
```

at 命令只要指定正确的时间，就可以输入需要在指定时间执行的命令了，这个命令可以是系统命


令，也可以是 shell 脚本。举几个例子吧：


例子 1：


```javascript
[root@localhost ~]# cat /root/hello.sh
#!/bin/bash
/bin/echo "hello world!!"
#该脚本会打印“hello workd！！”

[root@localhost ~]# at now +2 minutes
at> /root/hello.sh >> /root/hello.log

#执行 hello.sh 脚本，并把输出写入/root/hello.log 文件
at> <EOT> 使用 ctrl+d 保存 at 任务
job 8 at 2018-07-25 20:54 这是第 8 个 at 任务，会在 2018 年 7 月 25 日 20:54
分执行
```



```javascript
[root@localhost ~]# at -c 8
#查询第 8 个 at 任务的内容
…省略部分内容… 主要是定义系统的环境变量
/root/hello.sh >> /root/hello.log
#可以看到 at 执行的任务
```

例子 2：


```javascript
[root@localhost ~]# at 02:00 2018-07-26
at> /bin/sync
at> /sbin/shutdown -h now
at> <EOT>
job 9 at 2018-07-26 02:00
#在指定的时间关机。在一个 at 任务中，是可以执行多个系统命令的

```

注意：当at计划执行完毕后，执行的结果，系统会以mail的形式通知用户，具体的内容用mail命令查看。

如果想要某个结果输出到用户终端屏幕，可以使用这个(echo "test" > /dev/pts/1)：

```javascript
[root@server1 ~]# at now +2 minutes
at> echo "现在已经到两分钟了" > /dev/pts/1
at> <EOT>
job 6 at Sun Feb  6 04:55:00 2022
您在 /var/spool/mail/root 中有新邮件

```

## 3）、 其他 at 管理命令


at 还有查询和删除命令，命令如下：

atq查询


```javascript
[root@localhost ~]# atq
#查询当前服务器上的 at 工作
例如：
[root@localhost ~]# atq
9 2018-07-26 02:00 a root
#说明 root 用户有一个 at 任务在 2018 年 7 月 26 日的 02:00 执行，工作号是 9
```

atrm 删除

```javascript
[root@localhost ~]# atrm [工作号]
#删除指定的 at 任务
例如：
[root@localhost ~]# atrm 9
[root@localhost ~]# atq
#删除 9 号 at 任务，再查询就没有 at 任务存在了
```



---

# 2 、batch命令，在CPU负载小于0.8的时候执行。

CPU负载：指的是CPU在单一时间点所负责的任务数量

```javascript
[root@server1 ~]# batch
at> echo "ahha" > /dev/pts/1
at> <EOT>
job 9 at Sun Feb  6 05:09:00 2022
[root@server1 ~]# ahha

[root@server1 ~]#
```



---

# 3、写入黑面单测试，

## vim /etc/at.denyect

```javascript
[root@server1 ~]# vim /etc/at.deny 

user1

```



```javascript
[user1@server1 ~]$ at now +1 minutes
You do not have permission to use at.
```



---

# 4、/var/spool/at/目录下文件测试

## at  now +5minutes	 设定一个定时任务

```javascript
[root@server1 at]# at now +5minutes
at> echo "hsdjkjdsk"
at> <EOT>
job 11 at Sun Feb  6 06:06:00 2022

```

## ll  /var/spool/at/ 查看任务文件

```javascript
[root@server1 ~]# ll /var/spool/at
总用量 4
-rwx------. 1 root root 2848 2月   6 06:01 a0000b01a21f8e
drwx------. 2 root root    6 2月   6 05:53 spool
```

## atq 查看任务号码

```javascript
[root@server1 ~]# atq
11	Sun Feb  6 06:06:00 2022 a root
```

## atrm 11删除任务

```javascript
[root@server1 ~]# atrm 11
```

## ll /var/spool/at 再次查看任务文件（任务文件消失）

```javascript
[root@server1 ~]# ll /var/spool/at
总用量 0
drwx------. 2 root root 6 2月   6 05:53 spool

```

