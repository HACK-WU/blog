# **1、通配符**

| 通配符 | 作     用 | 
| -- | -- |
| ？ | 匹配一个任意字符 | 
| * | 匹配 0 个或任意多个任意字符，也就是可以匹配任何内容 | 
| [  ] | 匹配中括号中任意一个字符。例如：[abc]代表一定匹配一个字符，或 | 
| [ - ] |   | 
| [ ^ ] | 逻辑非，表示匹配不是中括号内的一个字符。例如：[^0-9]代表匹配一 | 


```javascript
[root@localhost tmp]# touch abc
[root@localhost tmp]# touch abcd
[root@localhost tmp]# touch 012
[root@localhost tmp]# touch 0abc
```

```javascript
#建立几个测试文件
[root@localhost tmp]# ls *
012 0abc abc abcd
#“*”代表所有的文件
[root@localhost tmp]# ls ?abc
0abc
#“?”匹配任意一个字符，所以会匹配 0abc
#但是不能匹配 abc，因为“?”不能匹配空
[root@localhost tmp]# ls [0-9]*
012 0abc
#匹配任何以数字开头的文件
[root@localhost tmp]# ls [^0-9]*
abc abcd
#匹配不已数字开头的文件
```

# **2、Bash中其他特殊符号**

| 符号 | 作用 | 
| -- | -- |
| '  ' | 单引号。在单引号中的所有的特殊符号，如“$”没有特殊含义 | 
| " " | 双引号。在双引号中除了"$" 、" `"和"  \"以外，其他特殊符号都没有特殊含义 | 
| `    ` | 反引号。反引号里的内容是系统命令，在Bash中会先执行它。和$()的作用一样，但推荐使用$(); | 
| $() | 括号内执行系统命令 | 
| （） | 用于一串命令执行时，（）中的命令会在子Shell中执行 | 
| {  } | 用于一串命令执行时，{}中的命令会在当前{}中执行。也可以用于变量变形与替换 | 
| [ ] | 用于变量的测试 | 
| # | 在Shell脚本中  ，#开头代表注释 | 
| $ | 用于调用变量的值； | 
| \ | 转义符 | 


## **1）单引号和双引号**

```javascript
[root@localhost ~]# name=sc
#定义变量 name 的值是 sc（就是最正直的人，超哥我了！）
[root@localhost ~]# echo '$name'
$name
#如果输出时使用单引号，则$name 原封不动的输出
[root@localhost ~]# echo "$name"
sc
#如果输出时使用双引号，则会输出变量 name 的值 sc
[root@localhost ~]# echo `date`
2018 年 10 月 21 日 星期一 18:16:33 CST
#反引号括起来的命令会正常执行
[root@localhost ~]# echo '`date`'
`date`
#但是如果反引号命令被单引号括起来，那么这个命令不会执行，`date`会被当成普通字符输出
[root@localhost ~]# echo "`date`"
2018 年 10 月 21 日 星期一 18:14:21 CST
#如果是双引号括起来，那么这个命令又会正常执行
```

## **2）反引号**

```javascript
[root@localhost ~]# echo ls
ls
#如果命令不用反引号包含，命令不会执行，而是直接输出
[root@localhost ~]# echo `ls`
anaconda-ks.cfg install.log install.log.syslog sh test testfile
#只有用反引号包括命令，这个命令才会执行
[root@localhost ~]# echo $(date)
2018 年 10 月 21 日 星期一 18:25:09 CST
#使用$(命令)的方式也是可以的
```

## **3）小括号、中括号和大括号**

在介绍小括号和大括号的区别之前，我们先要解释一个概念，那就是父 Shell 和子 Shell。在我

们的 Bash 中，是可以调用新的 Bash 的，比如：

```javascript
[root@localhost ~]# bash
[root@localhost ~]#
```

这时，我们通过 pstree 命令查看一下进程数：

```javascript
[root@localhost ~]# pstree
init─┬─abrt-dump-oops
…省略部分输出
├─sshd─┬─sshd───bash───bash───pstree
```

知道了父 Shell 和子 Shell，我们接着解释小括号和大括号的区别。如果是用于一串命令的执行，

那么小括号和大括号的主要区别在于：

 ()执行一串命令时，需要重新开一个子 shell 进行执行

 {}执行一串命令时，是在当前 shell 执行；

 ()和{}都是把一串的命令放在括号里面，并且命令之间用;号隔开；

 ()最后一个命令可以不用分号；

 {}最后一个命令要用分号；

 {}的第一个命令和左括号之间必须要有一个空格；

 ()里的各命令不必和括号有空格；

 ()和{}中括号里面的某个命令的重定向只影响该命令，但括号外的重定向则影响到括号里的

所有命令。

```javascript
[root@localhost ~]# name=sc
#在父 Shell 中定义变量 name 的值是 sc
[root@localhost ~]# (name=liming;echo $name)
liming
#如果用()括起来一串命令，这些命令都可以执行
#给 name 变量重新赋值，但是这个值只在子 Shell 中生效
[root@localhost ~]# echo $name
sc
#父 Shell 中 name 的值还是 sc，而不是 liming
[root@localhost ~]# { name=liming;echo $name; }
liming
#但是用大括号来进行一串命令的执行时，name 变量的修改是直接在父 Shell 当中的
#注意大括号的格式
[root@localhost ~]# echo $name
liming
#所以 name 变量的值已经被修改了
```