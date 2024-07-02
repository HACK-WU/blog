**stty 命令**

1、作用

　　为了自己能够编写适合自己的终端驱动函数，即去修改终端驱动程序里面的设置。在Linux中有这个命令，就是 stty。

　　stty：用于显示和修改终端命令行的相关设置

2、常用参数

　　stty 命令不带参数可以打印终端行设置，加上 -a 参数可以打印得更详细些。

　　stty size ：可以显示终端的大小，即行数和列数。

　　stty 命令还可以更改终端行的设置，格式如下：stty SETTING CHAR

　　其中，SETTING可以是如下：

```
eof : 输入结束，文件结束，默认为Ctrl+D。比如：用cat >file来创建文件时，按Ctrl+D来结束输入。
erase : 向后删除字符，擦除最后一个输入字符，默认为Ctrl+?。注意默认情况下退格键Backspace不是删除字符。
intr : 中断当前程序，默认为Ctrl+C。
kill : 删除整条命令，删除整行，默认为Ctrl+U。
quit :退出当前程序，默认为Ctrl+\或Ctrl+|。
start : 启动屏幕输出，默认为Ctrl+Q。
stop :停止屏幕输出，默认为Ctrl+S。
susp : terminal stop当前程序，默认为Ctrl+Z。这样当前进程就会变成后台进程了。
werase：删除最后一个单词，默认为Ctrl+W。
```

tty 命令还有一些其他用法，如：

```
#在命令行下，禁止输出大写的方法：
stty iuclc     #开启
stty -iuclc    #恢复

#在命令行下禁止输出小写：
stty olcuc    #开启
stty -olcuc   #恢复

#打印出终端的行数和列数：
stty size

#改变Ctrl+D的方法:
stty eof "string"
#系统默认是Ctrl+D来表示文件的结束，而通过这种方法，可以改变！

#屏蔽显示：
stty -echo   #禁止回显
stty echo    #打开回显
#测试方法:
stty -echo;read;stty echo;read

#忽略回车符：
stty igncr     #开启
stty -igncr    #恢复
```

3、使用示例

（1）打印终端行设置

```
[root@web ~]# stty
speed 38400 baud; line = 0;
-brkint -imaxbel
[root@web ~]# stty -a
speed 38400 baud; rows 41; columns 132; line = 0;
```

2）打印当前终端的大小（行数和列数）

```
[root@web ~]# stty size
24 80
```

（3）设置退格键Backspace的删除行为

**　　在默认情况下，我们按退格键Backspace时，会在屏幕上回显^H，而不是把前一个字符删除。比如使用sftp/ftp/sqlplus/ij等命令时，就会碰到这种情况。我们可以使用stty命令把Backspace的行为变成删除前一个字符。**

```
sftp> get abc^H^H^H^H
#修改删除行为
[root@web ~]# stty erase ^H
```

（4）在vi编辑文件时按Ctrl+Q来结束终端僵死的局面

```
[root@web ~]# vi 1.txt
1
2
3
Ctrl+S
~
注：按了Ctrl+S之后，就会禁止屏幕输出，从而出现终端僵死的情况。
这个时候，只要按Ctrl+Q就会结束这种局面，因为它会允许屏幕输出。
Ctrl+Q
```