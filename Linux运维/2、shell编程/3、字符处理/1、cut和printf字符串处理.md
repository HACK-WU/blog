# **1、cut 列提取命令**

```javascript
[root@localhost ~]# cut [选项] 文件名
选项：
    -f 列号： 提取第几列  #f,field,场地区域；
    -d 分隔符： 按照指定分隔符分割列 , #--delimiter=分界符
    -c 字符范围： 不依赖分隔符来区分列，而是通过字符范围（行首为 0）来进行字段
                    提取。“n-”表示从第 n 个字符到行尾；“n-m”从第 n 个字符到第 m
                    个字符；“-m”表示从第 1 个字符到第 m 个字符。
```

**cut 命令的默认分隔符是制表符，也就是“tab”键，不过对空格符可是支持的不怎么好啊。**

- 我们先建立一个测试文件，然后看看 cut 命令的作用吧：

```javascript
[root@bogon ~]# vi test1
ID      Name    gender  Mark
1       Liming  M       86
2       SC      M       90
3       TG      M       83

[root@bogon ~]# cut -f 2 test1
Name
Liming
SC
TG
[root@bogon ~]# 
```

- **那如果想要提取多列呢？只要列号直接用“，”分开，命令如下**

```javascript
[root@bogon ~]# cut -f 1,2 test1

ID	Name
1	Liming
2	SC
3	TG
[root@bogon ~]# 
```

- **cut 可以按照字符进行提取，需要注意“8-”代表的是提取所有行的第十个字符开始到行尾，而**

**“10-20”代表提取所有行的第十个字符到第二十个字符，而“-8”代表提取所有行从行首到第八个**

**字符：**

```javascript
[root@bogon ~]# cut -c 8- test1

	gender	Mark
g	M	86
90
83
[root@bogon ~]# 
```

- **用分隔符提取多列（cut -d ":"  test1） **

```javascript
[root@bogon ~]# grep -v "root" /etc/passwd  |  grep /bin/bash  | cut -d ":" -f 1
user1
user2
user3
```

# **2、printf格式化输出**

# **（不识别管道符，例如：grep " str" 文件1 | print  "..."这是错误的，但是print "..." | grep "..." 文件2  这是可以的）**

```javascript
[root@localhost ~]# printf ‘输出类型输出格式’ 输出内容
输出类型：
    %ns： 输出字符串。n 是数字指代输出几个字符，s,string
    %ni： 输出整数。n 是数字指代输出几个数字,i,int整数
    %m.nf： 输出浮点数f,float。m 和 n 是数字，指代输出的整数位数和小数位数。如%8.2f
            代表共输出 8 位数，其中 2 位是小数，6 位是整数。
    输出格式：
    \a: 输出警告声音
    \b: 输出退格键，也就是 Backspace 键
    \f: 清除屏幕
    \n: 换行
    \r: 回车，也就是 Enter 键
    \t: 水平输出退格键，也就是 Tab 键
    \v: 垂直输出退格键，也就是 Tab 键
```

为了演示 printf 命令，我们需要修改下刚刚 cut 命令使用的 student.txt 文件，文件内容如下：

```javascript
[root@localhost ~]# vi student.txt
ID Name    PHP Linux MySQL Average
1  Liming  82  95    86    87.66
2  Sc      74  96    87    85.66
3  Tg      99  83    93    91.66
```

我们使用 printf 命令输出下这个文件的内容：

```javascript
[root@localhost ~]# printf '%s' $(cat student.txt)
IDNamegenderPHPLinuxMySQLAverage1LimingM82958687.662ScM74968785.663TgM99839391.66[root@
localhost ~]#
```

晕菜，全变乱了一锅粥。这就是 printf 命令，如果不指定输出格式，则会把所有输出内容连在

一起输出。其实文本的输出本身就是这样的，cat 等文本输出命令之所以可以按照格式漂亮的输出，

那是因为 cat 命令已经设定了输出格式。那么为了用 printf 输出合理的格式，应该这样做：

```javascript
[root@localhost ~]# printf '%s\t %s\t %s\t %s\t %s\t %s\t \n' $(cat student.txt)
#注意在 printf 命令的单引号中，只能识别格式输出符号，而手工输入的空格是无效的
ID Name PHP Linux MySQL Average
1 Liming 82 95 86 87.66
2 Sc 74 96 87 85.66
3 Tg 99 83 93 91.66
```