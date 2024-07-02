centos7最小化安装没有pstree



最小化安装centos之后，使用pstree显示进程树，提示没有此命令

原来是没有安装，需要安装psmisc

- 

```javascript
yum install psmisc
```

- 

接下来介绍一下psmisc



官方下载地址:



Psmisc (22.21):

http://download.sourceforge.net/psmisc/



Psmisc的内容



Psmisc软件包包含三个帮助管理/proc目录的程序。



安装下列程序: fuser, killall,pstree和pstree.x11(到pstree的链接)



简短说明



fuser 显示使用指定文件或者文件系统的进程的PID。



killall 杀死某个名字的进程，它向运行指定命令的所有进程发出信号。



pstree 树型显示当前运行的进程。



pstree.x11 与pstree功能相同，只是在退出前需要确认。



Psmisc 安装依赖关系



Psmisc 依赖于: Bash, Binutils, Coreutils, Diffutils, GCC, Gettext, Glibc, Grep, Make, Ncurses, Sed.





