**lsof**

显示Linux系统当前已打开的所有文件列表 

# 描述

**lsof命令**

在linux环境下，任何事物都以文件的形式存在，通过文件不仅仅可以访问常规数据，还可以访问网络连接和硬件。所以如传输控制协议 (TCP) 和用户数据报协议 (UDP) 套接字等，系统在后台都为该应用程序分配了一个文件描述符，无论这个文件的本质如何，该文件描述符为应用程序与基础操作系统之间的交互提供了通用接口。因为应用程序打开文件的描述符列表提供了大量关于这个应用程序本身的信息，因此通过lsof工具能够查看这个列表对系统监测以及排错将是很有帮助的。

# 语法

```
lsof(选项)
选项
    -a：列出打开文件存在的进程；
    -c<进程名>：列出指定进程所打开的文件；
    -g：列出GID号进程详情；
    -d<文件号>：列出占用该文件号的进程；
    +d<目录>：列出目录下被打开的文件；
    +D<目录>：递归列出目录下被打开的文件；
    -n<目录>：列出使用NFS的文件；
    -i<条件>：列出符合条件的进程。（4、6、协议、:端口、 @ip ）
    -p<进程号>：列出指定进程号所打开的文件；
    -u：列出UID号进程详情；(指定用户)
    -h：显示帮助信息；
    -v：显示版本信息。
    -t: 仅显示进程号PID
```

# 实例

```bash
lsof
command     PID USER   FD      type             DEVICE     SIZE       NODE NAME
init          1 root  cwd       DIR                8,2     4096          2 /
init          1 root  rtd       DIR                8,2     4096          2 /
init          1 root  txt       REG                8,2    43496    6121706 /sbin/init
init          1 root  mem       REG                8,2   143600    7823908 /lib64/ld-2.5.so
init          1 root  mem       REG                8,2  1722304    7823915 /lib64/libc-2.5.so
init          1 root  mem       REG                8,2    23360    7823919 /lib64/libdl-2.5.so
init          1 root  mem       REG                8,2    95464    7824116 /lib64/libselinux.so.1
init          1 root  mem       REG                8,2   247496    7823947 /lib64/libsepol.so.1
init          1 root   10u     FIFO               0,17                1233 /dev/initctl
migration     2 root  cwd       DIR                8,2     4096          2 /
migration     2 root  rtd       DIR                8,2     4096          2 /
migration     2 root  txt   unknown                                        /proc/2/exe
ksoftirqd     3 root  cwd       DIR                8,2     4096          2 /
ksoftirqd     3 root  rtd       DIR                8,2     4096          2 /
ksoftirqd     3 root  txt   unknown                                        /proc/3/exe
migration     4 root  cwd       DIR                8,2     4096          2 /
migration     4 root  rtd       DIR                8,2     4096          2 /
migration     4 root  txt   unknown                                        /proc/4/exe
ksoftirqd     5 root  cwd       DIR                8,2     4096          2 /
ksoftirqd     5 root  rtd       DIR                8,2     4096          2 /
ksoftirqd     5 root  txt   unknown                                        /proc/5/exe
events/0      6 root  cwd       DIR                8,2     4096          2 /
events/0      6 root  rtd       DIR                8,2     4096          2 /
events/0      6 root  txt   unknown                                        /proc/6/exe
events/1      7 root  cwd       DIR                8,2     4096          2 /
```

**lsof输出各列信息的意义如下：**

- COMMAND： 进程的名称

- PID：		进程标识符

- PPID：   		父进程标识符（需要指定-R参数）

- USER：		进程所有者

- PGID：		进程所属组

- FD：		文件描述符，应用程序通过文件描述符识别该文件。

1、文件描述符列表：

1. cwd：  表示current work dirctory，即：应用程序的当前工作目录，这是该应用程序启动的目录，除非它本身对这个目录进行更改

1. txt：   该类型的文件是程序代码，如应用程序二进制文件本身或共享库，如上列表中显示的 /sbin/init 程序

1. lnn：  library references (AIX);   库引用

1. er：   FD information error (see NAME column);    FD错误信息

1. jld：   jail directory (FreeBSD);     监狱目录

1. ltx：   shared library text (code and data);   共享库文件

1. mxx ：hex memory-mapped type number xx.  十六进制内存映射类型编号xx

1. m86：DOS Merge mapped file;   DOS合并映射文件

1. mem：memory-mapped file;       内存映射文件

1. mmap：memory-mapped device;   存储器映射设备

1. pd：parent directory;		父目录

1. rtd：root directory;			roo目录

1. tr：kernel trace file (OpenBSD);   内核跟踪文件

1. v86  VP/ix mapped file;

1. 0：表示标准输出

1. 1：表示标准输入

1. 2：表示标准错误

2、一般在标准输出、标准错误、标准输入后还跟着文件状态模式：

1. u：表示该文件被打开并处于读取/写入模式。

1. r：表示该文件被打开并处于只读模式。

1. w：表示该文件被打开并处于。

1. 空格：表示该文件的状态模式为unknow，且没有锁定。

1. -：表示该文件的状态模式为unknow，且被锁定。

3、同时在文件状态模式后面，还跟着相关的锁：

1. N：for a Solaris NFS lock of unknown type;（）

1. r：for read lock on part of the file;  (文件的部分读锁)

1. R：for a read lock on the entire file;  (对于整个文件的读锁)

1. w：for a write lock on part of the file;（文件的部分写锁）

1. W：for a write lock on the entire file;（整个文件的写锁）

1. u：for a read and write lock of any length;（对于任意长度的读写锁）

1. U：for a lock of unknown type;（对于未知类型的锁）

1. x：for an SCO OpenServer Xenix lock on part  of the file;

1. X：for an SCO OpenServer Xenix lock on the   entire file;

1. space：if there is no lock.

4、文件类型：

1. DIR：表示目录。

1. REG：普通文件

1. CHR：表示字符类型。

1. BLK：块设备类型。

1. UNIX： UNIX 域套接字。

1. FIFO：先进先出 (FIFO) 队列。

1. IPv4：网际协议 (IP) 套接字。

1. DEVICE：指定磁盘的名称

1. SIZE：文件的大小

1. NODE：索引节点（文件在磁盘上的标识）

1. NAME：打开文件的确切名称

列出指定进程号所打开的文件:

```bash
lsof -p $pid
```

获取端口对应的进程ID=>pid

```bash
lsof -i:9981 -P -t -sTCP:LISTEN
```

### 列出目录中所有打开的文件

可以使用

现有一个

```
[root@ecs-centos-7 tt]# tree data/
data/
├── dira
│   └── a.txt
└── d.s
1 directory, 2 files
```

列出 

```
[root@ecs-centos-7 tt]# lsof +D ./data/
COMMAND   PID USER   FD   TYPE DEVICE SIZE/OFF   NODE NAME
bash    28473 root  cwd    DIR  253,1     4096 131146 ./data
bash    28502 root  cwd    DIR  253,1     4096 131172 ./data/dira
vim     28530 root  cwd    DIR  253,1     4096 131172 ./data/dira
vim     28530 root    4u   REG  253,1    12288 131174 ./data/dira/.a.txt.swp
[root@ecs-centos-7 tt]# lsof +d ./data/
COMMAND   PID USER   FD   TYPE DEVICE SIZE/OFF   NODE NAME
bash    28473 root  cwd    DIR  253,1     4096 131146 ./data
bash    28502 root  cwd    DIR  253,1     4096 131172 ./data/dira
vim     28530 root  cwd    DIR  253,1     4096 131172 ./data/dira
```

上面例子中，

+D

### 使用-i显示所有连接

有些人喜欢用netstat来获取网络连接，但是我更喜欢使用lsof来进行此项工作。结果以对我来说很直观的方式呈现，我仅仅只需改变我的语法，就可以通过同样的命令来获取更多信息。

```
[root@server1 ~]# lsof -i
COMMAND  PID USER   FD   TYPE DEVICE SIZE NODE NAME
dhcpcd 6061 root 4u IPv4 4510 UDP *:bootpc
sshd 7703 root 3u IPv6  6499 TCP *:ssh (LISTEN)
sshd 7892 root 3u IPv6  6757 TCP 10.10.1.5:ssh->192.168.1.5:49901 (ESTABLISHED)
```

### 使用-i 6仅获取IPv6流量

```javascript
[root@server1 ~]# lsof -i 6

COMMAND  PID   USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
chronyd 6177 chrony    2u  IPv6  33503      0t0  UDP localhost:323 
sshd    6482   root    4u  IPv6  36435      0t0  TCP *:ssh (LISTEN)
master  6707   root   14u  IPv6  36700      0t0  TCP localhost:smtp (LISTEN)
```

### 仅显示TCP连接（同理可获得UDP连接）

你也可以通过在-i后提供对应的协议来仅仅显示TCP或者UDP连接信息。

```
# lsof -iTCP
 
COMMAND  PID USER   FD   TYPE DEVICE SIZE NODE NAME
sshd 7703 root 3u IPv6 6499 TCP *:ssh (LISTEN)
sshd 7892 root 3u IPv6 6757 TCP 10.10.1.5:ssh->192.168.1.5:49901 (ESTABLISHED)
```

### 使用-i:port来显示与指定端口相关的网络信息

或者，你也可以通过端口搜索，这对于要找出什么阻止了另外一个应用绑定到指定端口实在是太棒了。

```
# lsof -i :22
 
COMMAND  PID USER   FD   TYPE DEVICE SIZE NODE NAME
sshd 7703 root 3u  IPv6 6499 TCP *:ssh (LISTEN)
sshd 7892 root 3u  IPv6 6757 TCP 10.10.1.5:ssh->192.168.1.5:49901 (ESTABLISHED)
```

### 使用@host来显示指定到指定主机的连接

这对于你在检查是否开放连接到网络中或互联网上某个指定主机的连接时十分有用。

```
# lsof -i@172.16.12.5
 
sshd 7892 root 3u IPv6 6757 TCP 10.10.1.5:ssh->172.16.12.5:49901 (ESTABLISHED)
```

### 使用@host:port显示基于主机与端口的连接

你也可以组合主机与端口的显示信息。

```
# lsof -i@172.16.12.5:22
 
sshd 7892 root 3u IPv6 6757 TCP 10.10.1.5:ssh->172.16.12.5:49901 (ESTABLISHED)
```

### 出监听端口

找出正等候连接的端口。

```javascript
[root@server1 ~]# lsof -i -sTCP:LISTEN
COMMAND  PID USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
sshd    6482 root    3u  IPv4  36433      0t0  TCP *:ssh (LISTEN)
sshd    6482 root    4u  IPv6  36435      0t0  TCP *:ssh (LISTEN)
master  6707 root   13u  IPv4  36699      0t0  TCP localhost:smtp (LISTEN)
master  6707 root   14u  IPv6  36700      0t0  TCP localhost:smtp (LISTEN)
```

你也可以grep “LISTEN”来完成该任务。

```
[root@server1 ~]# lsof -i | grep -i LISTEN
sshd     6482   root    3u  IPv4  36433      0t0  TCP *:ssh (LISTEN)
sshd     6482   root    4u  IPv6  36435      0t0  TCP *:ssh (LISTEN)
master   6707   root   13u  IPv4  36699      0t0  TCP localhost:smtp (LISTEN)
master   6707   root   14u  IPv6  36700      0t0  TCP localhost:smtp (LISTEN)
```

### 找出已建立的连接

你也可以显示任何已经连接的连接。

```
[root@server1 ~]# lsof -i -sTCP:ESTABLISHED
COMMAND  PID USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
sshd    6975 root    3u  IPv4  38391      0t0  TCP server1:ssh->192.168.23.1:62495 (ESTABLISHED)
sshd    6980 root    3u  IPv4  39055      0t0  TCP server1:ssh->192.168.23.1:62497 (ESTABLISHED)
sshd    7986 root    3u  IPv4  45371      0t0  TCP server1:ssh->192.168.23.1:57224 (ESTABLISHED)
sshd    7991 root    3u  IPv4  45388      0t0  TCP server1:ssh->192.168.23.1:57225 (ESTABLISHED)
sshd    8248 root    3u  IPv4  45768      0t0  TCP server1:ssh->192.168.23.1:61818 (ESTABLISHED)
sshd    8253 root    3u  IPv4  45782      0t0  TCP server1:ssh->192.168.23.1:61819 (ESTABLISHED)
```

你也可以通过grep搜索“ESTABLISHED”来完成该任务

```
[root@server1 ~]# lsof -i | grep -i ESTABLISHED
sshd     6975   root    3u  IPv4  38391      0t0  TCP server1:ssh->192.168.23.1:62495 (ESTABLISHED)
sshd     6980   root    3u  IPv4  39055      0t0  TCP server1:ssh->192.168.23.1:62497 (ESTABLISHED)
sshd     7986   root    3u  IPv4  45371      0t0  TCP server1:ssh->192.168.23.1:57224 (ESTABLISHED)
sshd     7991   root    3u  IPv4  45388      0t0  TCP server1:ssh->192.168.23.1:57225 (ESTABLISHED)
sshd     8248   root    3u  IPv4  45768      0t0  TCP server1:ssh->192.168.23.1:61818 (ESTABLISHED)
sshd     8253   root    3u  IPv4  45782      0t0  TCP server1:ssh->192.168.23.1:61819 (ESTABLISHED)
```

### 用户信息

你也可以获取各种用户的信息，以及它们在系统上正干着的事情，包括它们的网络活动、对文件的操作等。

#### 使用-u显示指定用户打开了什么

```
# lsof -u daniel
 
-- snipped --
Dock 155 daniel  txt REG   14,2   2798436   823208 /usr/lib/libicucore.A.dylib
Dock 155 daniel  txt REG   14,2   1580212   823126 /usr/lib/libobjc.A.dylib
Dock 155 daniel  txt REG   14,2   2934184   823498 /usr/lib/libstdc++.6.0.4.dylib
Dock 155 daniel  txt REG   14,2    132008   823505 /usr/lib/libgcc_s.1.dylib
Dock 155 daniel  txt REG   14,2    212160   823214 /usr/lib/libauto.dylib
-- snipped --
```

#### 使用-u user来显示除指定用户以外的其它所有用户所做的事情

```
# lsof -u ^daniel
 
-- snipped --
Dock 155 jim  txt REG   14,2   2798436   823208 /usr/lib/libicucore.A.dylib
Dock 155 jim  txt REG   14,2   1580212   823126 /usr/lib/libobjc.A.dylib
Dock 155 jim  txt REG   14,2   2934184   823498 /usr/lib/libstdc++.6.0.4.dylib
Dock 155 jim  txt REG   14,2    132008   823505 /usr/lib/libgcc_s.1.dylib
Dock 155 jim  txt REG   14,2    212160   823214 /usr/lib/libauto.dylib
-- snipped --
```

#### 杀死指定用户所做的一切事情

可以消灭指定用户运行的所有东西，这真不错。

```
kill -9 `lsof -t -u daniel`
```

### 命令和进程

可以查看指定程序或进程由什么启动，这通常会很有用，而你可以使用lsof通过名称或进程ID过滤来完成这个任务。下面列出了一些选项：

#### 使用-c查看指定的命令正在使用的文件和网络连接

```
# lsof -c syslog-ng
 
COMMAND    PID USER   FD   TYPE     DEVICE    SIZE       NODE NAME
syslog-ng 7547 root  cwd    DIR    3,3    4096   2 /
syslog-ng 7547 root  rtd    DIR    3,3    4096   2 /
syslog-ng 7547 root  txt    REG    3,3  113524  1064970 /usr/sbin/syslog-ng
-- snipped --
```

#### 使用-p查看指定进程ID已打开的内容

```
# lsof -p 10075
 
-- snipped --
sshd    10068 root  mem    REG    3,3   34808 850407 /lib/libnss_files-2.4.so
sshd    10068 root  mem    REG    3,3   34924 850409 /lib/libnss_nis-2.4.so
sshd    10068 root  mem    REG    3,3   26596 850405 /lib/libnss_compat-2.4.so
sshd    10068 root  mem    REG    3,3  200152 509940 /usr/lib/libssl.so.0.9.7
sshd    10068 root  mem    REG    3,3   46216 510014 /usr/lib/liblber-2.3
sshd    10068 root  mem    REG    3,3   59868 850413 /lib/libresolv-2.4.so
sshd    10068 root  mem    REG    3,3 1197180 850396 /lib/libc-2.4.so
sshd    10068 root  mem    REG    3,3   22168 850398 /lib/libcrypt-2.4.so
sshd    10068 root  mem    REG    3,3   72784 850404 /lib/libnsl-2.4.so
sshd    10068 root  mem    REG    3,3   70632 850417 /lib/libz.so.1.2.3
sshd    10068 root  mem    REG    3,3    9992 850416 /lib/libutil-2.4.so
-- snipped --
```

#### -t选项只返回PID

```
# lsof -t -c Mail
 
350
```

### 文件和目录

通过查看指定文件或目录，你可以看到系统上所有正与其交互的资源——包括用户、进程等。

#### 显示与指定目录交互的所有一切

```
# lsof /var/log/messages/
 
COMMAND    PID USER   FD   TYPE DEVICE   SIZE   NODE NAME
syslog-ng 7547 root    4w   REG    3,3 217309 834024 /var/log/messages
```

### 显示与指定文件交互的所有一切

```javascript
# lsof /home/daniel/firewall_whitelist.txt
```

### 高级用法

与[tcpdump](http://danielmiessler.com/study/tcpdump/)类似，当你开始组合查询时，它就显示了它强大的功能。

#### 显示daniel连接到1.1.1.1所做的一切

```javascript
[root@server1 ~]# lsof  -u hack -i @192.168.23.1 | head
COMMAND  PID USER   FD   TYPE DEVICE  SIZE/OFF     NODE NAME
sshd    6975 root    3u  IPv4  38391       0t0      TCP server1:ssh->192.168.23.1:62495 (ESTABLISHED)
sshd    6980 root    3u  IPv4  39055       0t0      TCP server1:ssh->192.168.23.1:62497 (ESTABLISHED)
sshd    7986 root    3u  IPv4  45371       0t0      TCP server1:ssh->192.168.23.1:57224 (ESTABLISHED)
sshd    7991 root    3u  IPv4  45388       0t0      TCP server1:ssh->192.168.23.1:57225 (ESTABLISHED)
bash    8193 hack  cwd    DIR  253,0        95 17638084 /home/hack
bash    8193 hack  rtd    DIR  253,0       224       64 /
bash    8193 hack  txt    REG  253,0    964608 50332801 /usr/bin/bash
bash    8193 hack  mem    REG  253,0 106075056 17007829 /usr/lib/locale/locale-archive
bash    8193 hack  mem    REG  253,0     61624    14446 /usr/lib64/libnss_files-2.17.so
```

#### 同时使用-t和-c选项以给进程发送 HUP 信号

```
# kill -HUP `lsof -t -c sshd`
```

#### lsof +L1显示所有打开的链接数小于1的文件

这通常（当不总是）表示某个攻击者正尝试通过删除文件入口来隐藏文件内容。

```
# lsof +L1
 
(hopefully nothing)
```

#### 显示某个端口范围的打开的连接

```
# lsof -i @fw.google.com:2150=2180
```