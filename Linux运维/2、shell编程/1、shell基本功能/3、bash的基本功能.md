# **1、历史命令**

## **1) 历史命令的查看**

```javascript
root@localhost ~]# history [选项] [历史命令保存文件]
选项：
    -c： 清空历史命令
    -w： 把缓存中的历史命令写入历史命令保存文件。如果不手工指定历史命令保存文
    		件，则放入默认历史命令保存文件~/.bash_history 中
[root@bogon ~]# history
    1  ping wwww.baidu.com
    2  ip a
    3  yum install bash-completion -y
    4  install 
    5  ping baidu.com
    6  ls -a
    7  ls .bash_history 
    8  cat .bash_history 
    9  history 
   10  history -a
。。。。。。。。
```

- **历史命令都存放在/root/.bash_histroy中，默认最多可以存放1000条；**

```javascript
[root@bogon ~]# vi ./.bash_history 

  1 ping wwww.baidu.com
  2 ip a
  3 yum install bash-completion -y
  4 install
  5 ping baidu.com
  6 ls -a
  7 ls .bash_history
  8 cat .bash_history
  9 history
**。。。。。。。**
```

## **2）可以修改/root/.bash_history文件的可存放的命令的数量，只需修改配置文件/etc/profile:**

```javascript
[root@localhost ~]# vi /etc/profile
…省略部分输出…
HISTSIZE=1000    //此处可以修改为HISTSIZE=10000  最多存储10000条；
…省略部分输出…
```

## **3）我们使用 history 命令查看的历史命令和~/.bash_history 文件中保存的历史命令是不同的。那**

是因为当前登录操作的命令并没有直接写入~/.bash_history 文件，而是保存在缓存当中的。需要等

当前用户注销之后，缓存中的命令才会写入~/.bash_history 文件。如果我们需要把内存中的命令直

接写入~/.bash_history 文件，而不等用户注销时再写入，就需要使用“-w”选项了。命令如下：

```javascript
[root@localhost ~]# history -w
#把缓存中的历史命令直接写入~/.bash_history
```

这时再去查询~/.bash_history 文件，历史命令就和 history 命令查询的一致了。

## **4）如果需要清空历史命令，只需要执行：**

```javascript
[root@localhost ~]# history -c
#清空历史命令
```

## **5）历史命令的调用**

如果想要使用原先的历史命令有这样几种方法：

 使用上、下箭头调用以前的历史命令

 使用“!n”重复执行第 n 条历史命令

 使用“!!”重复执行上一条命令

 使用“!字串”重复执行最后一条以该字串开头的命令

 使用“!$”重复上一条命令的最后一个参数

## **2、命令别名（alias)**

```javascript
命令格式：
[root@localhost ~]# alias
#查询命令别名
[root@localhost ~]# alias 别名='原命令'
#设定命令别名
 
例如：
[root@localhost ~]# alias
#查询系统中已经定义好的别名
alias cp='cp -i'
alias l.='ls -d .* --color=auto'
alias ll='ls -l --color=auto'
alias ls='ls --color=auto'
alias mv='mv -i'
alias rm='rm -i'
alias which='alias | /usr/bin/which --tty-only --read-alias --show-dot --show-tilde'
 
[root@localhost ~]# alias vi='vim'
#定义 vim 命令的别名是 vi
```

- 既然我们说别名的优先级比命令高，那么命令执行时具体的顺序是什么呢？命令执行时的顺序是

这样的：

1、 第一顺位执行用绝对路径或相对路径执行的命令。

2、 第二顺位执行别名。

3、 第三顺位执行 Bash 的内部命令。

4 第四顺位执行按照$PATH 环境变量定义的目录查找顺序找到的第一个命令。

## **1)为了让这个别名永久生效，可以把别名写入环境变量配置文件“~/.bashrc”。命令如下：**

```javascript
[root@bogon ~]# vi ./.bashrc

# .bashrc

# User specific aliases and functions

alias rm='rm -i'
alias cp='cp -i'
alias mv='mv -i'
alias vi='vim'     //定义别名

```

- **保存文件后，可以使用命令：source   .bashrc 是配置文件立即生效（否则需要重启终端才会生效）；**

```javascript
[root@bogon ~]# source .bashrc
```

# **3、Bash 常用快捷键**

| 快捷键 | 作      用 | 
| -- | -- |
| Tab  键 | 命令或文件补全 | 
| Ctrl+A | 把光标移动到命令行的开头。 | 
| Ctrl+E | 把光标移动到命令行的末尾。 | 
| Ctrl+C | 强制终止当前命令 | 
| Ctrl+L | 清屏 | 
| Ctrl+U | 删除或剪切光标之前的命令。 | 
| Ctrl+Y | 粘贴Ctrl+U剪切的内容 | 
| Ctrl+K | 删除或剪切光标之后的内容 | 
| Ctrl+D | 退出当前终端 | 
| Ctrl+Z | 暂停，放入后台。 | 
| Ctrl+S | 暂停屏幕输出 | 
| Ctrl+Q | 恢复屏幕输出 | 


# **4、输入输出重定向**

## **1）Bash的标准输入输出**

| 设备 | 设备文件名 | 文件描述符 | 类型 | 
| -- | -- | -- | -- |
| 键盘 | /dev/stdin | 0 | 标准输入 | 
| 显示器 | /dev/stdout | 1 | 标准输出 | 
| 显示器 | /dev/stderr | 2 | 标准错误输出 | 


## **2）输出重定向**

| 类型 | 符号 | 作用 | 
| -- | -- | -- |
| 标准输出重定向 | 命令  >  文件 | 以覆盖的方式，把命令的正确输出输入到指定的文件或设备中 | 
| 命令  >>  文件 | 以追加的方式，把命令的正确输出输入到指定的文件或设备中 | 
| 标准错误输出重定向 | 错误命令  2>  文件 | 以覆盖的方式，把命令的错误输出输入到指定的文件或者设备中 | 
| 错误命令   2>  文件 | 以追加的方式，把命令的错误输出输入到指定的文件或者设备中 | 
| 正确输出和错误输出同时保存 | 命令  > 文件  2>&1   | 以覆盖的方式，把正确输出和错误输出都保存到同一个文件当中。 | 
| 命令 >> 文件 2>&1 | 以追加的方式，把正确输出和错误输出都保存到同一个文件当中。 | 
| 命令 &>文件 | 以覆盖的方式，把正确输出和错误输出都保存到同一个文件当中。 | 
| 命令 &>>文件 | 以追加的方式，把正确输出和错误输出都保存到同一个文件当中。 | 
| 命令 >>文件 1 2>>文件 2 | 把正确的输出追加到文件 1 中，把错误的输出追加到文件 2 中。 | 


## **3)输入重定向**

```javascript
[root@localhost ~]# wc [选项] [文件名]
选项：
    -c 统计字节数
    -w 统计单词数
    -l 统计行数
```

# **5、多命令顺序执行**

| 多命令执行符 | 格式 | 作用 | 
| -- | -- | -- |
| ； | 命令 1 ；命令 2 | 多个命令顺序执行，命令之间没有任何逻辑联系 | 
| && | 命令 1 && 命令 2 | 当命令 1 正确执行（$?=0），则命令 2 才会执行 | 
| ||  | 命令 1 || 命令 2 | 当命令 1 执行不正确（$? ≠ 0），则命令 2 才会执行 | 


# **6、管道符**

## **1）行提取命令grep（模糊查询）**

```javascript
[root@localhost ~]# grep [选项] "搜索内容" 文件名
选项：
    -A 数字： 列出符合条件的行，并列出后续的 n 行
    -B 数字： 列出符合条件的行，并列出前面的 n 行
    -c： 统计找到的符合条件的字符串的次数
    -i： 忽略大小写ignore case,
    -n： 输出行号
    -v： 查找不含有搜索内容的内容
    --color=auto 搜索出的关键字用颜色显示
[root@bogon ~]# grep "test"  test1  -n
12:test
13:test1
14:test2
15:testlv
```

## **2）find 和 grep 的区别**

- find 命令是在系统当中搜索符合条件的文件名，如果需要模糊查询，使用通配符（通配符我们下

一小节进行介绍）进行匹配，搜索时文件名是完全匹配。

```javascript
[root@localhost ~]# touch abc
#建立文件 abc
[root@localhost ~]# touch abcd
#建立文件 abcd
[root@localhost ~]# find . -name "abc"
./abc
#搜索文件名是 abc 的文件，只会找到 abc 文件，而不会找到文件 abcd
#虽然 abcd 文件名中包含 abc，但是 find 是完全匹配，只能和要搜索的数据完全一样，才能找到
```



注意

照正则表达式规则匹配，而正则表达式是模糊匹配。但是对于初学者而言，find 命

令和 grep 命令本身就不好理解，所以我们这里只按照通配符规则来进行 find 查询。



- grep 命令是在文件当中搜索符合条件的字符串，如果需要模糊查询，使用正则表达式进行匹配，

搜索时字符串是包含匹配。

```javascript
[root@localhost ~]# echo abc > test
#在 test 文件中写入 abc 数据
[root@localhost ~]# echo abcd >> test
#在 test 文件中再追加 abcd 数据
[root@localhost ~]# grep "abc" test
abc
abcd
#grep 命令查找时，只要数据行中包含有 abc，就会都列出
#所以 abc 和 abcd 都可以查询到
```

## **3）管道符**

```javascript
[root@localhost ~]# ll -a /etc/ | more
```

```javascript
[root@localhost ~]# netstat -an | grep "ESTABLISHED"
#查询下本地所有网络连接，提取包含 ESTABLISHED（已建立连接）的行
#就可以知道我们的服务器上有多少已经成功连接的网络连接
```

```javascript
[root@localhost ~]# netstat -an | grep "ESTABLISHED" | wc -l
#如果想知道具体的网络连接数量，就可以再使用 wc 命令统计行数
```

```javascript
[root@localhost ~]# rpm -qa | grep httpd
```

# **7、通配符**

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