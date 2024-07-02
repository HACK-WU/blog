

1 if 条件判断


1)、 单分支 if 条件语句


2）、 双分支 if 条件语句


例子 1：


例子 2：


3）、 多分支 if 条件语句


# 1 if 条件判断


## 1)、 单分支 if 条件语句


单分支条件语句最为简单，就是只有一个判断条件，如果符合条件则执行某个程序，否则什么事


情都不做。语法如下：


```javascript
if [ 条件判断式 ];then
程序
fi
```

- 单分支条件语句需要注意几个点：


 if 语句使用 fi 结尾，和一般语言使用大括号结尾不同


 [ 条件判断式 ]就是使用 test 命令判断，所以中括号和条件判断式之间必须有空格


 then 后面跟符合条件之后执行的程序，可以放在[]之后，用“；”分割。也可以换行写入，


就不需要“；”了，比如单分支 if 语句还可以这样写：


```javascript
if [ 条件判断式 ]
then
程序
fi
```



```javascript
[root@localhost ~]# vi sh/if1.sh
#!/bin/bash

#统计根分区使用率
# Author: shenchao （E-mail: shenchao@atguigu.com）
rate=$(df -h | grep "/dev/sda3" | awk '{print $5}' | cut -d "%" -f1)
#把根分区使用率作为变量值赋予变量 rate
if [ $rate -ge 80 ]
#判断 rate 的值如果大于等于 80，则执行 then 程序
then
echo "Warning! /dev/sda3 is full!!"
#打印警告信息。在实际工作中，也可以向管理员发送邮件。
fi
```

## 2）、 双分支 if 条件语句


```javascript
if [ 条件判断式 ]
then
条件成立时，执行的程序
else
条件不成立时，执行的另一个程序
fi
```

### 例子 1：


我们写一个数据备份的例子，来看看双分支 if 条件语句。


例子 1：备份 mysql 数据库


```javascript
[root@localhost ~]# vi sh/bakmysql.sh
#!/bin/bash
#备份 mysql 数据库。
# Author: shenchao （E-mail: shenchao@atguigu.com）
ntpdate asia.pool.ntp.org &>/dev/null
#同步系统时间
date=$(date +%y%m%d)
#把当前系统时间按照“年月日”格式赋予变量 date
size=$(du -sh /var/lib/mysql)
#统计 mysql 数据库的大小，并把大小赋予 size 变量
if [ -d /tmp/dbbak ]
#判断备份目录是否存在，是否为目录
then
#如果判断为真，执行以下脚本
echo "Date : $date!" > /tmp/dbbak/dbinfo.txt
#把当前日期写入临时文件
echo "Data size : $size" >> /tmp/dbbak/dbinfo.txt
#把数据库大小写入临时文件
cd /tmp/dbbak

#进入备份目录
tar -zcf mysql-lib-$date.tar.gz /var/lib/mysql dbinfo.txt &>/dev/null
#打包压缩数据库与临时文件，把所有输出丢入垃圾箱（不想看到任何输出）
rm -rf /tmp/dbbak/dbinfo.txt
#删除临时文件
else
mkdir /tmp/dbbak
#如果判断为假，则建立备份目录
echo "Date : $date!" > /tmp/dbbak/dbinfo.txt
echo "Data size : $size" >> /tmp/dbbak/dbinfo.txt
#把日期和数据库大小保存如临时文件
cd /tmp/dbbak
tar -zcf mysql-lib-$date.tar.gz dbinfo.txt /var/lib/mysql &>/dev/null
#压缩备份数据库与临时文件
rm -rf /tmp/dbbak/dbinfo.txt
#删除临时文件
fi
```

### 例子 2：


再举个例子，在工作当中，服务器上的服务经常会宕机。如果我们对服务器监控不好，就会造成


服务器中服务宕机了，而管理员却不知道的情况，这时我们可以写一个脚本来监听本机的服务，如果


服务停止或宕机了，可以自动重启这些服务。我们拿 apache 服务来举例：


例子 2：判断 apache 是否启动，如果没有启动则自动启动


```javascript
[root@localhost ~]# vi sh/autostart.sh
#!/bin/bash
#判断 apache 是否启动，如果没有启动则自动启动
# Author: shenchao （E-mail: shenchao@atguigu.com）
port=$(nmap -sT 192.168.4.210 | grep tcp | grep http | awk '{print $2}')
#使用 nmap 命令扫描服务器，并截取 apache 服务的状态，赋予变量 port
if [ "$port" == "open" ]
#如果变量 port 的值是“open”
then
echo “$(date) httpd is ok!” >> /tmp/autostart-acc.log
#则证明 apache 正常启动，在正常日志中写入一句话即可
else
/etc/rc.d/init.d/httpd start &>/dev/null
#否则证明 apache 没有启动，自动启动 apache
echo "$(date) restart httpd !!" >> /tmp/autostart-err.log
#并在错误日志中记录自动启动 apche 的时间
fi
```

- 以我们使用 nmap 端口扫描命令，nmap 命令格式如下：


```javascript
[root@localhost ~]# nmap -sT 域名或 IP
选项：

-s 扫描
-T 扫描所有开启的 TCP 端口
```

这条命令的执行结果如下：


```javascript
[root@localhost ~]# nmap -sT 192.168.4.210
#可以看到这台服务器开启了如下的服务
Starting Nmap 5.51 ( http://nmap.org ) at 2018-11-25 15:11 CST
Nmap scan report for 192.168.4.210
Host is up (0.0010s latency).
Not shown: 994 closed ports
PORT STATE SERVICE
22/tcp open ssh
80/tcp open http apache 的状态是 open
111/tcp open rpcbind
139/tcp open netbios-ssn
445/tcp open microsoft-ds
3306/tcp open mysql
Nmap done: 1 IP address (1 host up) scanned in 0.49 seconds
```

知道了 nmap 命令的用法，我们在脚本中使用的命令就是为了截取 http 的状态，只要状态是“open”


就证明 apache 启动正常，否则证明 apache 启动错误。来看看脚本中命令的结果：


```javascript
[root@localhost ~]# nmap -sT 192.168.4.210 | grep tcp | grep http | awk '{print $2}'
#扫描指定计算机，提取包含 tcp 的行，在提取包含 httpd 的行，截取第二列
open
#把截取的值赋予变量 port
```

## 3）、 多分支 if 条件语句


```javascript
if [ 条件判断式 1 ]
then
当条件判断式 1 成立时，执行程序 1
elif [ 条件判断式 2 ]
then
当条件判断式 2 成立时，执行程序 2
…省略更多条件…
else
当所有条件都不成立时，最后执行此程序
fi
```

那我们再写一个例子，用 if 多分支条件语句来判断一下用户输入的是一个文件，还是一个目录：


例子：判断用户输入的是什么文件


```javascript
[root@localhost ~]# vi sh/if-elif.sh
#!/bin/bash
#判断用户输入的是什么文件
# Author: shenchao （E-mail: shenchao@atguigu.com）

read -p "Please input a filename: " file
#接收键盘的输入，并赋予变量 file
if [ -z "$file" ]
#判断 file 变量是否为空
then
echo "Error,please input a filename"
#如果为空，执行程序 1，也就是输出报错信息
exit 1
#退出程序，并返回值为 1（把返回值赋予变量$?）
elif [ ! -e "$file" ]
#判断 file 的值是否存在
then
echo "Your input is not a file!"
#如果不存在，则执行程序 2
exit 2
#退出程序，把并定义返回值为 2
elif [ -f "$file" ]
#判断 file 的值是否为普通文件
then
echo "$file is a regulare file!"
#如果是普通文件，则执行程序 3
elif [ -d "$file" ]
#判断 file 的值是否为目录文件
then
echo "$file is a directory!"
#如果是目录文件，则执行程序 4
else
echo "$file is an other file!"
#如果以上判断都不是，则执行程序 5
fi
```

