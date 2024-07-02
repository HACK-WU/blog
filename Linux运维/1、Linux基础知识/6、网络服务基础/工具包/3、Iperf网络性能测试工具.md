安装包下载地址[3.01、iperf-2.0.13-1.el7.x86_64](note://WEBf58b237261ac36077fbe516afd1559b6)

网络性能评估主要是监测网络带宽的使用率，将网络带宽利用最大化是保证网络性能的基础，但是由于网络设计不合理、网络存在安全漏洞等原因，都会导致网络带宽利用率不高。要找到网络带宽利用率不高的原因，就需要对网络传输进行监控，此时就需要用到一些网络性能评估工具，而Iperf就是这样一款网络带宽测试工具。

# **一、Iperf简介**

## **1.1、什么是iperf？**

Iperf是美国伊利诺斯大学（University of Illinois）开发的一种开源的网络性能测试工具。可以用来测试网络节点间（也包括回环）TCP或UDP连接的性能，包括带宽、抖动以及丢包率，其中抖动和丢包率适应于UDP测试，而带宽测试适应于TCP和UDP。

Iperf是一款基于TCP/IP和UDP/IP的网络性能测试工具，可以用来测量网络带宽和网络质量，提供网络延迟抖动、数据包丢失率、最大传输单元等统计信息。网络管理员可以根据这些信息了解并判断网络性能问题，从而定位网络瓶颈，解决网络故障。

Iperf 是一款基于命令行模式的网络性能测试工具，是跨平台的，提供横跨Windows、Linux、Mac的全平台支持。iperf 全程使用内存作为发送/接收缓冲区，不受磁盘性能的影响，对于机器配置要求很低。不过由于是命令行工具， iperf 不支持输出测试图形。

Iperf可以测试TCP和UDP带宽质量，具有多种参数和UDP特性，可以用来测试一些网络设备如路由器，防火墙，交换机等的性能。

## **1.2、Iperf的功能**

**（1）TCP方面**

① 测量网络带宽

② 报告MSS/MTU值的大小和观测值

③ 支持TCP窗口值通过套接字缓冲

④ 当P线程或Win32线程可用时，支持多线程。客户端与服务端支持同时多重连接

**（2）UDP方面**

① 客户端可以创建指定带宽的UDP流

② 测量丢包

③ 测量延迟

④ 支持多播

⑤ 当P线程可用时，支持多线程。客户端与服务端支持同时多重连接（不支持Windows）

**（3）其他方面**

① 在适当的地方，选项中可以使用K（kilo-）和M（mega-）。例如131072字节可以用128K代替。

② 可以指定运行的总时间，甚至可以设置传输的数据总量。

③ 在报告中，为数据选用最合适的单位。

④ 服务器支持多重连接，而不是等待一个单线程测试。

⑤ 在指定时间间隔重复显示网络带宽，波动和丢包情况。

⑥ 服务器端可作为后台程序运行。

⑦ 服务器端可作为Windows 服务运行。

⑧ 使用典型数据流来测试链接层压缩对于可用带宽的影响。

# **二、Iperf的安装**

## **2.1、iperf的版本**

Iperf有两种版本，windows版和linux版本。

- （1）Unix/Linux版

Unix/Linux版更新比较快，版本最新，目前最新的版本是iperf3.0。

Linux版本下载地址：[http://code.google.com/p/iperf/downloads/list](http://code.google.com/p/iperf/downloads/list)

为了测试的准确性，尽量使用linux环境测试。

- （2）Windows版

Windows版iperf叫jperf，或者xjperf，更新慢，目前最新版本为1.7（打包在jperf中）。

Windows版本下载地址：[http://sourceforge.net/projects/iperf/files/jperf/jperf%202.0.0/](http://sourceforge.net/projects/iperf/files/jperf/jperf%202.0.0/)

jperf是在iperf基础上开发的图形界面程序，简化了复杂命令行参数的构造，而且还能保存测试结果,同时实时图形化显示结果。

## **2.2、Windows版iperf安装**

对于windows版的iperf，下载安装包后直接解压，然后将解压出来的iperf.exe和cygwin1.dll复制到%systemroot%目录即可。

## **2.3、Linux版iperf安装**

- （1）在线安装：

CentOS安装：

Debian和Ubuntu安装：

- （2）离线安装：下载相应版本的安装包

gunzip -c iperf-.tar.gz | tar -xvf – cd iperf- ./configure make make install

# 三、Iperf的使用

## **3.1、Iperf的工作模式**

Iperf可以运行在任何IP网络上，包括本地以太网、接入因特网、Wi-Fi网络等。在工作模式上，iperf运行于服务器、客户端模式下，其

在完成iperf安装后，执行“iperf3 –h”即可显示iperf的详细用法。iperf的命令行选项共分为三类，分别是客户端与服务器端公用选项、服务器端专用选项和客户端专用选项。

## **3.2、Iperf常用参数（测试够用）**

```
-s,–server：        iperf服务器模式，默认启动的监听端口为5201，eg：iperf -s
-c,–client host：  iperf客户端模式，host是server端地址，eg：iperf -c 222.35.11.23
-i，–interval：    指定每次报告之间的时间间隔，单位为秒，eg：iperf3 -c 192.168.12.168 -i 2
-p，–port：        指定服务器端监听的端口或客户端所连接的端口，默认是5001端口。
-u，–udp：        表示采用UDP协议发送报文，不带该参数表示采用TCP协议。
-l，–len：        设置读写缓冲区的长度，单位为 Byte。TCP方式默认为8KB，UDP方式默认为1470字节。通常测试 PPS 的时候该值为16，测试BPS时该值为1400。
-b，–bandwidth [K|M|G]：    指定UDP模式使用的带宽，单位bits/sec，默认值是1 Mbit/sec。
-t，–time：            指定数据传输的总时间，即在指定的时间内，重复发送指定长度的数据包。默认10秒。
-A：                    CPU亲和性，可以将具体的iperf3进程绑定对应编号的逻辑CPU，避免iperf进程在不同的CPU间调度。
```

**3、通用参数（Server端和Client端共用）**

```
-f，–farmat [k|m|g|K|M|G]：    指定带宽输出单位，“[k|m|g|K|M|G]”分别表示以Kbits, Mbits, Gbits, KBytes, MBytes,GBytes显示输出结果，默认Mbits，eg：iperf3 -c 192.168.12.168 -f M
-p，–port：             指定服务器端监听的端口或客户端所连接的端口，默认是5001端口。
-i，–interval：         指定每次报告之间的时间间隔，单位为秒，eg：iperf3 -c 192.168.12.168 -i 2
-F：                    指定文件作为数据流进行带宽测试。例如：iperf3 -c 192.168.12.168 -F web-ixdba.tar.gz
```

**4、Server端专用参数**

```
-s,–server：         iperf服务器模式，默认启动的监听端口为5201，eg：iperf -s
-c,–client host：    如果iperf运行在服务器模式，并且用-c参数指定一个主机，那么iperf将只接受指定主机的连接。此参数不能工作于UDP模式。
-D：                Unix平台下将Iperf作为后台守护进程运行。在Win32平台下，Iperf将作为服务运行。
-R：                卸载Iperf服务(仅用于Windows)。
-o：                重定向输出到指定文件(仅用于Windows)。
-P,–parallel：        服务器关闭之前保持的连接数。默认是0，这意味着永远接受连接。
```

**5、Client端专用参数**

```
-c,–client host：    iperf客户端模式，host是server端地址，eg：iperf -c 222.35.11.23
-u，–udp：            表示采用UDP协议发送报文，不带该参数表示采用TCP协议。
-b，–bandwidth [K|M|G]：    指定UDP模式使用的带宽，单位bits/sec，默认值是1 Mbit/sec。
-t，–time：                指定数据传输的总时间，即在指定的时间内，重复发送指定长度的数据包。默认10秒。
-l，–len：            设置读写缓冲区的长度，单位为 Byte。TCP默认为8KB，UDP默认为1470字节。通常测试 PPS 的时候该值为16，测试BPS时该值为1400。
-n，–num [K|M|G]：    指定传输数据包的字节数，例如：iperf3 -c 192.168.12.168 –n 100M
-P,–parallel：        指定客户端与服务端之间使用的线程数。默认是1个线程。需要客户端与服务器端同时使用此参数。
-w，–window：         指定套接字缓冲区大小，在TCP方式下，此设置为TCP窗口的大小。在UDP方式下，此设置为接受UDP数据包的缓冲区大小，用来限制可以接收数据包的最大值
-B，–bind：           用来绑定一个主机地址或接口，这个参数仅用于具有多个网络接口的主机。在UDP模式下，此参数用于绑定和加入一个多播组。
-M，–mss：            设置TCP最大信息段的值
-N，–nodelay：        设置TCP无延时
-V：                  绑定一个IPv6地址。
-d，–dualtest：       运行双测试模式。将使服务器端反向连接到客户端，使用-L参数中指定的端口（或默认使用客户端连接到服务器端的端口）。使用参数-r以运行交互模式。
-L,–listenport：      指定服务端反向连接到客户端时使用的端口。默认使用客户端连接至服务端的端口。
-r，–tradeoff：       往复测试模式。当客户端到服务器端的测试结束时，服务器端反向连接至客户端。当客户端连接终止时，反向连接随即开始。如果需要同时进行双向测试，请尝试-d参数。
```

**6、其他参数**

```
（1）-h，–help：显示命令行参考并退出。
    [root]# iperf3 -hUsage: iperf3 [-s|-c host] [options] iperf3 [-h|--help] [-v|--version]
（2）-v，–version：显示版本信息和编译信息并退出。
```

# **四、Iperf使用实例**

## **4.1、环境准备：**

（1）Server端IP地址：192.168.0.120

（2）Server端IP地址：192.168.0.121

## **4.2、测试TCP吞吐量**

（1）Server端开启iperf的服务器模式，指定TCP端口：

```
[root@iperf-server ～]# iperf3 -s -i 1 -p 520
------------------------------------------------------------
Server listening on TCP port 520
TCP window size: 85.3 KByte (default)
------------------------------------------------------------
```

（2）Client端启动iperf的客户端模式，连接服务端

```
[root@iperf-client ～]# iperf -c 192.168.0.120 -i 1 -t 60 -p 520
------------------------------------------------------------
Client connecting to 192.168.0.120, TCP port 520
TCP window size: 45.0 KByte (default)
------------------------------------------------------------
[ 3] local 192.168.0.121 port 50616 connected with 192.168.0.120 port 520
[ ID] Interval Transfer Bandwidth
[ 3] 0.0-10.1 sec 1.27 GBytes 1.08 Gbits/sec
```

（3）Server端监听结果

```
------------------------------------------------------------
Server listening on TCP port 5001 TCP window size: 85.3 KByte (default)
------------------------------------------------------------
[ 4] local 192.168.0.120 port 520 connected with 192.168.0.121 port 50616
[ ID] Interval Transfer Bandwidth
[ 4] 0.0-10.1 sec 1.27 GBytes 1.08 Gbits/sec
```

① Interval	表示时间间隔。

② Transfer	表示时间间隔里面转输的数据量。

③ Bandwidth	是时间间隔里的传输速率。

（4）测试多线程TCP吞吐量

如果没有指定发送方式，iPerf客户端只会使用单线程。

```
iperf3 -c 192.168.0.120 -P 30 -t 60
```

（5）进行上下行带宽测试（双向传输）

```
iperf3 -c 192.168.0.120 -d -t 60
```

（6）停止iperf3服务进程

要停止iperf3服务进程，请按CTRL+z或CTRL+c

建议在Server端执行sar命令来统计实际收到的包并作为实际结果：

```
sar -n DEV 1 320
```

## **4.3、测试UDP吞吐量**

带宽测试通常采用UDP模式，因为能测出极限带宽、时延抖动、丢包率。在进行测试时，首先以链路理论带宽作为数据发送速率进行测试，例如，从客户端到服务器之间的链路的理论带宽为100Mbps，先用-b 100M进行测试，然后根据测试结果（包括实际带宽，时延抖动和丢包率），再以实际带宽作为数据发送速率进行测试，会发现时延抖动和丢包率比第一次好很多，重复测试几次，就能得出稳定的实际带宽。

（1）Server端开启iperf的服务器模式，指定UDP端口：

```
[root@iperf-server ～]# iperf3 -s -i 1 -p 521
------------------------------------------------------------
Server listening on port 521
------------------------------------------------------------
```

（2）Client端启动iperf的客户端模式，连接服务端

```
[root@iperf-client ～]# iperf3 -u -c 192.168.0.120 -b 100m -t 60 -p 521
------------------------------------------------------------
Client connecting to 192.168.0.120, port 521
------------------------------------------------------------
[ 3] local 192.168.0.121 port 50616 connected with 192.168.0.120 port 521
[ ID] Interval Transfer Bandwidth TotalDatagrams
[ 3] 0.0-10.1 sec 1.27 GBytes 1.08 Gbits/sec 82
```

（3）Server端监听结果

```
------------------------------------------------------------
Server listening on port 521
------------------------------------------------------------
[ 4] local 192.168.0.120 port 520 connected with 192.168.0.121 port 50616
[ ID] Interval Transfer Bandwidth Jitter Lost/Total Datagrams
[ 4] 0.0-10.1 sec 1.27 GBytes 1.08 Gbits/sec 0.007 ms 0/82 (0%)
```

① Jitter为抖动，在连续传输中的平滑平均值差。

② Lost为丢包数量。

③ Total Datagrams为包数量。

（4）测试多线程UDP吞吐量

如果没有指定发送方式，iPerf客户端只会使用单线程。

```
iperf3 -u -c 192.168.1.1 -b 5M -P 30 -t 60
```

（5）进行上下行带宽测试（双向传输）

```
iperf3 -u -c 192.168.1.1 -b 100M -d -t 60
```