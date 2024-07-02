当在centos下编译带有头文件<curses.h> 的程序时，出现以下错误： “致命错误：curses.h：没有那个文件或目录”

解决方法：

yum install ncurses-libs

yum install ncurses-devel

```javascript
[root@server1 cmatrix-1.2a]# make
gcc -DHAVE_CONFIG_H -I. -I. -I.     -g -O2 -Wall -Wno-comment -c cmatrix.c
cmatrix.c:37:20: 致命错误：curses.h：没有那个文件或目录
 #include <curses.h>
                    ^
编译中断。
make: *** [cmatrix.o] 错误 1

```

