信号（Signal）：信号是在软件层次上对中断机制的一种模拟，通过给一个进程发送信号，执行相应的处理函数。

linux通过信号来在运行在系统上的进程之间通信，也可以通过信号来控制shell脚本的运行

进程可以通过三种方式来响应一个信号：

- 1）忽略信号，即对信号不做任何处理，其中有两个信号不能忽略：SIGKILL及SIGSTOP。

- 2）捕捉信号。

- 3）执行缺省操作，Linux对每种信号都规定了默认操作。

Linux支持两种信号：

一种是标准信号，编号1-31，称为非可靠信号（非实时），不支持队列，信号可能会丢失，比如发送多次相同的信号，进程只能收到一次，如果第一个信号没有处理完，第二个信号将会丢弃。

另一种是扩展信号，编号32-64，称为可靠信号（实时），支持队列，发多少次进程就可以收到多少次。

| 编号 | 信号名称 | 缺省动作 | 描述 | 
| -- | -- | -- | -- |
| 1 | SIGHUP | 终止 | 终止进程，挂起 | 
| 2 | SIGINT | 终止 | 键盘输入中断命令，一般是CTRL+C | 
| 3 | SIGQUIT | CoreDump | 键盘输入退出命令，一般是CTRL+\ | 
| 4 | SIGILL | CoreDump | 非法指令 | 
| 5 | SIGTRAP | CoreDump | trap指令发出，一般调试用 | 
| 6 | SIGABRT | CoreDump | abort(3)发出的终止信号 | 
| 7 | SIGBUS | CoreDump | 非法地址 | 
| 8 | SIGFPE | CoreDump | 浮点数异常 | 
| 9 | SIGKILL | 终止 | 立即停止进程，不能捕获，不能忽略 | 
| 10 | SIGUSR1 | 终止 | 用户自定义信号1，像Nginx就支持USR1信号，用于重载配置，重新打开日志 | 
| 11 | SIGSEGV | CoreDump | 无效内存引用 | 
| 12 | SIGUSR2 | 终止 | 用户自定义信号2 | 
| 13 | SIGPIPE | 终止 | 管道不能访问 | 
| 14 | SIGALRM | 终止 | 时钟信号，alrm(2)发出的终止信号 | 
| 15 | SIGTERM | 终止 | 终止信号，进程会先关闭正在运行的任务或打开的文件再终止，有时间进程在有运行的任务而忽略此信号。不能捕捉 | 
| 16 | SIGSTKFLT | 终止 | 处理器栈错误 | 
| 17 | SIGCHLD | 可忽略 | 子进程结束时，父进程收到的信号 | 
| 18 | SIGCONT | 可忽略 | 让终止的进程继续执行 | 
| 19 | SIGSTOP | 停止 | 停止进程，不能忽略，不能捕获 | 
| 20 | SIGSTP | 停止 | 停止进程，一般是CTRL+Z | 
| 21 | SIGTTIN | 停止 | 后台进程从终端读数据 | 
| 22 | SIGTTOU | 停止 | 后台进程从终端写数据 | 
| 23 | SIGURG | 可忽略 | 紧急数组是否到达socket | 
| 24 | SIGXCPU | CoreDump | 超出CPU占用资源限制 | 
| 25 | SIGXFSZ | CoreDump | 超出文件大小资源限制 | 
| 26 | SIGVTALRM | 终止 | 虚拟时钟信号，类似于SIGALRM，但计算的是进程占用的时间 | 
| 27 | SIGPROF | 终止 | 类似与SIGALRM，但计算的是进程占用CPU的时间 | 
| 28 | SIGWINCH | 可忽略 | 窗口大小改变发出的信号 | 
| 29 | SIGIO | 终止 | 文件描述符准备就绪，可以输入/输出操作了 | 
| 30 | SIGPWR | 终止 | 电源失败 | 
| 31 | SIGSYS | CoreDump | 非法系统调用 | 


stty(set tty，设置tty)命令用于检查和修改当前注册的终端的通信参数。

UNIX系统为键盘的输入和终端的输出提供了重要的控制手段，可以通过stty命令对特定终端或通信线路设置选项。

可以使用stty -a命令查看当前注册终端的设置情况。

```
[hack@test2 ~]$ stty -a
speed 38400 baud; rows 65; columns 231; line = 0;
intr = ^C; quit = ^\; erase = ^?; kill = ^U; eof = ^D; eol = M-^?; eol2 = M-^?; swtch = <undef>; start = ^Q; stop = ^S; susp = ^Z; rprnt = ^R; werase = ^W; lnext = ^V; flush = ^O; min = 1; time = 0;
-parenb -parodd -cmspar cs8 -hupcl -cstopb cread -clocal -crtscts
-ignbrk -brkint -ignpar -parmrk -inpck -istrip -inlcr -igncr icrnl ixon -ixoff -iuclc ixany imaxbel iutf8
opost -olcuc -ocrnl onlcr -onocr -onlret -ofill -ofdel nl0 cr0 tab0 bs0 vt0 ff0
isig icanon iexten echo echoe echok -echonl -noflsh -xcase -tostop -echoprt echoctl echoke
```