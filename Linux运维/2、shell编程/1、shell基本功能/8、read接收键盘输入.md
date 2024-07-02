# **1、read**

```javascript
[root@localhost ~]# read [选项] [变量名]
选项：
    -p #print-打印“提示信息”： 在等待 read 输入时，输出提示信息
    -t #time-时间秒数： read 命令会一直等待用户输入，使用此选项可以指定等待时间
    -n #字符数： read 命令只接受指定的字符数，就会执行
    -s： 隐藏输入的数据，适用于机密信息的输入
变量名：
    变量名可以自定义，如果不指定变量名，会把输入保存入默认变量 REPLY
    如果只提供了一个变量名，则整个输入行赋予该变量
    如果提供了一个以上的变量名，则输入行分为若干字，一个接一个地赋予各个变量，而命令行上
    的最后一个变量取得剩余的所有字
```

- 还是写个例子来解释下 read 命令：

```javascript
[root@localhost sh]# vi read.sh
#!/bin/bash
# Author: shenchao （E-mail: shenchao@atguigu.com）
read -t 30 -p "Please input your name: " name
#提示“请输入姓名”并等待 30 秒，把用户的输入保存入变量 name 中
echo "Name is $name"
#看看变量“$name”中是否保存了你的输入
read -s -t 30 -p "Please enter your age: " age
#提示“请输入年龄”并等待 30 秒，把用户的输入保存入变量 age 中
#年龄是隐私，所以我们用“-s”选项隐藏输入
echo -e "\n"
#调整输出格式，如果不输出换行，一会的年龄输出不会换行
echo "Age is $age"
read -n 1 -t 30 -p "Please select your gender[M/F]: " gender
#提示“请选择性别”并等待 30 秒，把用户的输入保存入变量 gender
#使用“-n 1”选项只接收一个输入字符就会执行（都不用输入回车）
echo -e "\n"
echo "Sex is $gender"
```

- **例子：**

```javascript
[root@bogon test]# cat count2.sh 
#!/bin/bash

read -t  30 -p "please input a num1:" num1
read -t 30 -p "please input a num2:" num2


echo "num1+num2=$((num1+num2))"

[root@bogon test]# ./count2.sh 
please input a num1:12
please input a num2:43
num1+num2=55
[root@bogon test]# 
```