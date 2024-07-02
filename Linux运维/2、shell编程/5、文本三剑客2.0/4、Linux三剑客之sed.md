**Linux三剑客之sed**

- Linux sed命令是利用script来处理文本文件。

- sed可依照script的指令，来处理、编辑文本文件。

- Sed主要用来自动编辑一个或多个文件；简化对文件的反复操作；编写转换程序等。

# 1、语法

```
sed的命令格式： sed [option] 'sed command' filename
sed的脚本格式：sed [option] -f 'sed script' filename
常用选项：
-n ：只打印模式匹配的行
-e ：直接在命令行模式上进行sed动作编辑，此为默认选项
-f ：将sed的动作写在一个文件内，用–f filename 执行filename内的sed动作
-r ：支持扩展表达式
-i ：直接修改文件内容
查询文本的方式
使用行号和行号范围
x：行号
x,y：从x行到y行
x,y!：x行到y行之外
/pattern：查询包含模式的行
/pattern/, /pattern/：查询包含两个模式的行
/pattern/,x：x行内查询包含模式的行
x,/pattern/：x行后查询匹配模式的行
```

# 2、动作说明

```
常用选项：
p：打印匹配的行(-n)
=：显示文件行号
a\：指定行号后添加新文本
i\：指定行号前添加新文本
d：删除定位行
c\：用新文本替换定位文本
w filename：写文本到一个文件
r filename：从另一个文件读文本
s///：替换
替换标记：
g：行内全局替换
p：显示替换成功的行
w：将替换成功的结果保存至指定文件
q：第一个模式匹配后立即退出
{}：在定位行执行的命令组，用逗号分隔
g：将模式2粘贴到/pattern n/
```

a ：新增， a 的后面可以接字串，而这些字串会在新的一行出现(目前的下一行)～

c ：取代， c 的后面可以接字串，这些字串可以取代 n1,n2 之间的行！

d ：删除，因为是删除啊，所以 d 后面通常不接任何咚咚；

i ：插入， i 的后面可以接字串，而这些字串会在新的一行出现(目前的上一行)；

p ：打印，亦即将某个选择的数据印出。通常 p 会与参数 sed -n 一起运行～

s ：取代，可以直接进行取代的工作哩！通常这个 s 的动作可以搭配正规表示法！例如 1,20s/old/new/g

就是啦！

# 3、实例

在testfile文件的第四行后添加一行，并将结果输出到标准输出

```
[root@localhost ~]# sed -e 4a\newline test
line one
line two
line three
line four
newline
line five
```

## 1）以行为单位的新增/删除

将 /etc/passwd 的内容列出并且列印行号，同时，请将第 2~5 行删除

```
[root@localhost ~]# nl /etc/passwd | sed '2,5d'
  1 root:x:0:0:root:/root:/bin/bash
  6 sync:x:5:0:sync:/sbin:/bin/sync
  7 shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
  8 halt:x:7:0:halt:/sbin:/sbin/halt
  9 mail:x:8:12:mail:/var/spool/mail:/sbin/nologin
  10 operator:x:11:0:operator:/root:/sbin/nologin
  11 games:x:12:100:games:/usr/games:/sbin/nologin
  12 ftp:x:14:50:FTP User:/var/ftp:/sbin/nologin
  13 nobody:x:99:99:Nobody:/:/sbin/nologin
  14 systemd-network:x:192:192:systemd Network Management:/:/sbin/nologin
  15 dbus:x:81:81:System message bus:/:/sbin/nologin
  16 polkitd:x:999:998:User for polkitd:/:/sbin/nologin
  17 sshd:x:74:74:Privilege-separated SSH:/var/empty/sshd:/sbin/nologin
  18 postfix:x:89:89::/var/spool/postfix:/sbin/nologin
```

sed 的动作为 '2,5d' ，那个 d 就是删除！因为 2-5 行给他删除了，所以显示的数据就没有 2-5 行罗～ 另

外，注意一下，原本应该是要下达 sed -e 才对，没有 -e 也行啦！同时也要注意的是， sed 后面接的动

作，请务必以 '' 两个单引号括住喔！

2）只要删除第 2 行

```
[root@localhost ~]# nl /etc/passwd | sed '2d'
  1 root:x:0:0:root:/root:/bin/bash
  3 daemon:x:2:2:daemon:/sbin:/sbin/nologin
  4 adm:x:3:4:adm:/var/adm:/sbin/nologin
  5 lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
  6 sync:x:5:0:sync:/sbin:/bin/sync
  7 shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
  8 halt:x:7:0:halt:/sbin:/sbin/halt
  9 mail:x:8:12:mail:/var/spool/mail:/sbin/nologin
  10 operator:x:11:0:operator:/root:/sbin/nologin
  11 games:x:12:100:games:/usr/games:/sbin/nologin
  12 ftp:x:14:50:FTP User:/var/ftp:/sbin/nologin
  13 nobody:x:99:99:Nobody:/:/sbin/nologin
  14 systemd-network:x:192:192:systemd Network Management:/:/sbin/nologin
  15 dbus:x:81:81:System message bus:/:/sbin/nologin
  16 polkitd:x:999:998:User for polkitd:/:/sbin/nologin
  17 sshd:x:74:74:Privilege-separated SSH:/var/empty/sshd:/sbin/nologin
  18 postfix:x:89:89::/var/spool/postfix:/sbin/nologin
```

要删除第 3 到最后一行

```
[root@localhost ~]# nl /etc/passwd | sed '3,$d'
  1 root:x:0:0:root:/root:/bin/bash
  2 bin:x:1:1:bin:/bin:/sbin/nologin
```

在第二行后(亦即是加在第三行)加上『hello world』字样

```
[root@localhost ~]# nl /etc/passwd | sed '2a hello world'
  1 root:x:0:0:root:/root:/bin/bash
  2 bin:x:1:1:bin:/bin:/sbin/nologin
hello world
  3 daemon:x:2:2:daemon:/sbin:/sbin/nologin
  4 adm:x:3:4:adm:/var/adm:/sbin/nologin
  5 lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
  6 sync:x:5:0:sync:/sbin:/bin/sync
  7 shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
  8 halt:x:7:0:halt:/sbin:/sbin/halt
  9 mail:x:8:12:mail:/var/spool/mail:/sbin/nologin
  10 operator:x:11:0:operator:/root:/sbin/nologin
  11 games:x:12:100:games:/usr/games:/sbin/nologin
  12 ftp:x:14:50:FTP User:/var/ftp:/sbin/nologin
  13 nobody:x:99:99:Nobody:/:/sbin/nologin
  14 systemd-network:x:192:192:systemd Network Management:/:/sbin/nologin
  15 dbus:x:81:81:System message bus:/:/sbin/nologin
  16 polkitd:x:999:998:User for polkitd:/:/sbin/nologin
  17 sshd:x:74:74:Privilege-separated SSH:/var/empty/sshd:/sbin/nologin
  18 postfix:x:89:89::/var/spool/postfix:/sbin/nologin
```

加在第二行前面

```
[root@localhost ~]# nl /etc/passwd | sed '2i hello world'
  1 root:x:0:0:root:/root:/bin/bash
hello world
  2 bin:x:1:1:bin:/bin:/sbin/nologin
  3 daemon:x:2:2:daemon:/sbin:/sbin/nologin
  4 adm:x:3:4:adm:/var/adm:/sbin/nologin
  5 lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
  6 sync:x:5:0:sync:/sbin:/bin/sync
  7 shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
  8 halt:x:7:0:halt:/sbin:/sbin/halt
  9 mail:x:8:12:mail:/var/spool/mail:/sbin/nologin
  10 operator:x:11:0:operator:/root:/sbin/nologin
  11 games:x:12:100:games:/usr/games:/sbin/nologin
  12 ftp:x:14:50:FTP User:/var/ftp:/sbin/nologin
  13 nobody:x:99:99:Nobody:/:/sbin/nologin
  14 systemd-network:x:192:192:systemd Network Management:/:/sbin/nologin
  15 dbus:x:81:81:System message bus:/:/sbin/nologin
  16 polkitd:x:999:998:User for polkitd:/:/sbin/nologin
  17 sshd:x:74:74:Privilege-separated SSH:/var/empty/sshd:/sbin/nologin
  18 postfix:x:89:89::/var/spool/postfix:/sbin/nologin
```

增加多行文字

```
[root@localhost ~]# nl /etc/passwd | sed '2a hello world\
> nihao'
  1 root:x:0:0:root:/root:/bin/bash
  2 bin:x:1:1:bin:/bin:/sbin/nologin
hello world
nihao
  3 daemon:x:2:2:daemon:/sbin:/sbin/nologin
  4 adm:x:3:4:adm:/var/adm:/sbin/nologin
  5 lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
  6 sync:x:5:0:sync:/sbin:/bin/sync
  7 shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
  8 halt:x:7:0:halt:/sbin:/sbin/halt
  9 mail:x:8:12:mail:/var/spool/mail:/sbin/nologin
  10 operator:x:11:0:operator:/root:/sbin/nologin
  11 games:x:12:100:games:/usr/games:/sbin/nologin
  12 ftp:x:14:50:FTP User:/var/ftp:/sbin/nologin
  13 nobody:x:99:99:Nobody:/:/sbin/nologin
  14 systemd-network:x:192:192:systemd Network Management:/:/sbin/nologin
  15 dbus:x:81:81:System message bus:/:/sbin/nologin
  16 polkitd:x:999:998:User for polkitd:/:/sbin/nologin
  17 sshd:x:74:74:Privilege-separated SSH:/var/empty/sshd:/sbin/nologin
  18 postfix:x:89:89::/var/spool/postfix:/sbin/nologin
```

## 2）以行为单位的替换与显示

将第2-5行的内容取代成为『No 2-5 number』

```
[root@localhost ~]# nl /etc/passwd | sed '2,5c No 2-5 number'
  1 root:x:0:0:root:/root:/bin/bash
No 2-5 number
  6 sync:x:5:0:sync:/sbin:/bin/sync
  7 shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
  8 halt:x:7:0:halt:/sbin:/sbin/halt
  9 mail:x:8:12:mail:/var/spool/mail:/sbin/nologin
  10 operator:x:11:0:operator:/root:/sbin/nologin
  11 games:x:12:100:games:/usr/games:/sbin/nologin
  12 ftp:x:14:50:FTP User:/var/ftp:/sbin/nologin
  13 nobody:x:99:99:Nobody:/:/sbin/nologin
  14 systemd-network:x:192:192:systemd Network Management:/:/sbin/nologin
  15 dbus:x:81:81:System message bus:/:/sbin/nologin
  16 polkitd:x:999:998:User for polkitd:/:/sbin/nologin
  17 sshd:x:74:74:Privilege-separated SSH:/var/empty/sshd:/sbin/nologin
  18 postfix:x:89:89::/var/spool/postfix:/sbin/nologin
```

仅列出 /etc/passwd 文件内的第 5-7 行

```
[root@localhost ~]# nl /etc/passwd | sed -n '5,7p'
  5 lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
  6 sync:x:5:0:sync:/sbin:/bin/sync
  7 shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
```

## 3）数据的搜寻并显示

搜索 /etc/passwd有root关键字的行

```
[root@localhost ~]# nl /etc/passwd | sed -n '/root/p'
  1 root:x:0:0:root:/root:/bin/bash
  10 operator:x:11:0:operator:/root:/sbin/nologin
```

## 4）数据的搜寻并删除

删除/etc/passwd所有包含root的行，其他行输出

```
[root@localhost ~]# nl /etc/passwd | sed '/root/d'
  2 bin:x:1:1:bin:/bin:/sbin/nologin
  3 daemon:x:2:2:daemon:/sbin:/sbin/nologin
  4 adm:x:3:4:adm:/var/adm:/sbin/nologin
  5 lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
  6 sync:x:5:0:sync:/sbin:/bin/sync
  7 shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
  8 halt:x:7:0:halt:/sbin:/sbin/halt
  9 mail:x:8:12:mail:/var/spool/mail:/sbin/nologin
  11 games:x:12:100:games:/usr/games:/sbin/nologin
  12 ftp:x:14:50:FTP User:/var/ftp:/sbin/nologin
  13 nobody:x:99:99:Nobody:/:/sbin/nologin
  14 systemd-network:x:192:192:systemd Network Management:/:/sbin/nologin
  15 dbus:x:81:81:System message bus:/:/sbin/nologin
  16 polkitd:x:999:998:User for polkitd:/:/sbin/nologin
  17 sshd:x:74:74:Privilege-separated SSH:/var/empty/sshd:/sbin/nologin
  18 postfix:x:89:89::/var/spool/postfix:/sbin/nologin
```

## 6）数据的搜寻并执行命令

搜索/etc/passwd,找到root对应的行，执行后面花括号中的一组命令，每个命令之间用分号分隔，这里

把bash替换为blueshell，再输出这行

```
[root@localhost ~]# nl /etc/passwd | sed -n '/root/{s/bash/blueshell/;p;q}'
  1 root:x:0:0:root:/root:/bin/blueshell
```

最后的q是退出，不然会继续找下去

## 7）数据的搜寻并替换

除了整行的处理模式之外， sed 还可以用行为单位进行部分数据的搜寻并取代

```
sed 's/要被取代的字串/新的字串/g' 
```

查询 IP

原始信息

```
[root@localhost ~]# ifconfig
ens33: flags=4163<UP,BROADCAST,RUNNING,MULTICAST> mtu 1500
   inet 192.168.91.128 netmask 255.255.255.0 broadcast 192.168.91.255
   inet6 fe80::2de4:b37a:36e9:ae2e prefixlen 64 scopeid 0x20<link>
   ether 00:0c:29:d3:76:83 txqueuelen 1000 (Ethernet)
   RX packets 33461 bytes 32133707 (30.6 MiB)
   RX errors 0 dropped 0 overruns 0 frame 0
   TX packets 11322 bytes 1300148 (1.2 MiB)
   TX errors 0 dropped 0 overruns 0 carrier 0 collisions 0
lo: flags=73<UP,LOOPBACK,RUNNING> mtu 65536
   inet 127.0.0.1 netmask 255.0.0.0
   inet6 ::1 prefixlen 128 scopeid 0x10<host>
   loop txqueuelen 1000 (Local Loopback)
   RX packets 10 bytes 697 (697.0 B)
   RX errors 0 dropped 0 overruns 0 frame 0
   TX packets 10 bytes 697 (697.0 B)
   TX errors 0 dropped 0 overruns 0 carrier 0 collisions 0
```

取有IP地址那一行

```
[root@localhost ~]# ifconfig | sed -n '/netmask/p'
   inet 192.168.91.128 netmask 255.255.255.0 broadcast 192.168.91.255
   inet 127.0.0.1 netmask 255.0.0.0
```

删除IP地址前面和后面的东西

```
[root@localhost ~]# ifconfig | sed -n '/netmask/p' | sed 's/^.*inet //g' |
sed 's/ netmask.*$//g'
192.168.91.128
127.0.0.1
```

取第一行

```
[root@localhost ~]# ifconfig | sed -n '/netmask/p' | sed 's/^.*inet //g' |
sed 's/ netmask.*$//g' | sed -n '1p'
192.168.91.128
```

可以在末尾加g替换每一个匹配的关键字,否则只替换每行的第一个

## 8）多点编辑

一条sed命令，删除/etc/passwd第三行到末尾的数据，并把bash替换为blueshell

```
[root@localhost ~]# nl /etc/passwd | sed -e '3,$d' -e 's/bash/blueshell/'
  1 root:x:0:0:root:/root:/bin/blueshell
  2 bin:x:1:1:bin:/bin:/sbin/nologin
```

-e表示多点编辑，第一个编辑命令删除/etc/passwd第三行到末尾的数据，第二条命令搜索bash替换为

blueshell。

## 9）直接修改文件内容(危险动作)

```
root@localhost ~]# cat test
line one.
line two.
line three.
line four.
line five.
```

```
[root@localhost ~]# sed -i 's/\.$/\!/g' test
[root@localhost ~]# cat test
line one!
line two!
line three!
line four!
line five!
```

利用 sed 直接在最后一行加入 # test

```
[root@localhost ~]# sed -i '$a # test' test
[root@localhost ~]# cat test
line one!
line two!
line three!
line four!
line five!
# test
```