# **1、echo 命令**

```javascript
[root@localhost ~]# echo [选项] [输出内容]
选项：
    -e： 支持反斜线控制的字符转换（具体参见表 11-2）
    -n： 取消输出后行末的换行符号（就是内容输出后不换行）
```

- **例子 1：**


```javascript
[root@localhost ~]# echo "Mr. Shen Chao is the most honest man！"
#echo 的内容就会打印到屏幕上。
Mr. Shen Chao is the most honest man！
[root@localhost ~]#
```

- **例子 2：**


```javascript
[root@localhost ~]# echo -n "Mr. Shen Chao is the most honest man!"
Mr. Shen Chao is the most honest man![root@localhost ~]#
#如果加入了“-n”选项，输出内容结束后，不会换行直接显示新行的提示符。
```

- **在 echo 命令中如果使用了“-e”选项，则可以支持控制字符，如表 11-2 所示：**

| 控制字符 | 作      用 | 
| -- | -- |
| \\ | 输出"\"本身 | 
| \a | 输出警告音 | 
| \b | 退格键，就是想左删除键 | 
| \c | 取消输出行末的换行符。和”-n"选项一致 | 
| \e | ESCAPE键 | 
| \f | 换页符 | 
| \n | 换行符 | 
| \r | 回车键 | 
| \t | 制表符，也就是Tab键 | 
| \v | 垂直制表符 | 
| \0nnn | 按照八进制ASCII码表输出字符。其中0为数字零，nnn是三位八进制数 | 
| \xhh | 按照十六进制ASCII码表输出字符。其中hh是两位十六进制数 | 


```javascript
例子 3：
[root@localhost ~]# echo -e "\\ \a"
\
#这个输出会输出\，同时会在系统音响中输出一声提示音
例子 4：
[root@localhost ~]# echo -e "ab\bc"
ac
#这个输出中，在 b 键左侧有“\b”，所以输出时只有 ac
例子 5：
[root@localhost ~]# echo -e "a\tb\tc\nd\te\tf"
a b c
d e f
#我们加入了制表符“\t”和换行符“\n”,所以会按照格式输出
例子 6：
[root@localhost ~]# echo -e "\0141\t\0142\t\0143\n\0144\t\0145\t\0146"
a b c
d e f
#还是会输出上面的内容，不过是按照八进制 ASCII 码输出的。
```

也就是说 141 这个八进制，在 ASCII 码中代表小写的“a”，其他的以此类推。


```javascript
例子 7：
[root@localhost ~]# echo -e "\x61\t\x62\t\x63\n\x64\t\x65\t\x66"
a b c
d e f
#如果按照十六进制 ASCII 码也同样可以输出
echo 命令还可以进行一些比较有意思的东西，比如：
例子 8：
[root@localhost ~]# echo -e "\e[1;31m abcd \e[0m"
这条命令会把 abcd 按照红色输出。解释下这个命令\e[1 是标准格式，代表颜色输出开始，\e[0m
代表颜色输出结束，31m 定义字体颜色是红色。echo 能够识别的颜色如下：30m=黑色，31m=红色，32m=
绿色，33m=黄色，34m=蓝色，35m=洋红，36m=青色，37m=白色。
例子 9：
[root@localhost ~]# echo -e "\e[1;42m abcd \e[0m"
```

这条命令会给 abcd 加入一个绿色的背景。echo 可以使用的背景颜色如下：40m=黑色，41m=红色，


42m=绿色，43m=黄色，44m=蓝色，45m=洋红，46m=青色，47m=白色。

# **2、Shell脚本执行**

- **“.sh"文件开头：#！/bin/Bash   ;必须写，它告诉系统，这是一个shell脚本；**

```javascript
[root@localhost sh]# vi hello.sh
#!/bin/bash
#The first program
# Author: shenchao （E-mail: shenchao@atguigu.com）
echo -e "Mr. Shen Chao is the most honest man. "
```

**shell 脚本写好了，那么这个脚本该如何运行呢？在 Linux 中脚本的执行主要有这样两种种方法：**

### ** 赋予执行权限，直接运行
**

这种方法是最常用的 Shell 脚本运行方法，也最为直接简单。就是赋予执行权限之后，直接运行。


当然运行时可以使用绝对路径，也可以使用相对路径运行。命令如下：


```javascript
[root@localhost sh]# chmod 755 hello.sh
#赋予执行权限
[root@localhost sh]# /root/sh/hello.sh
Mr. Shen Chao is the most honest man.
#使用绝对路径运行
[root@localhost sh]# ./hello.sh
Mr. Shen Chao is the most honest man.
#因为我们已经在/root/sh 目录当中，所以也可以使用相对路径运行
```

### ** 通过 Bash 调用执行脚本
**

这种方法也非常简单，命令如下：


```javascript
[root@localhost sh]# bash hello.sh
Mr. Shen Chao is the most honest man.
```