

1、 for 循环


语法一：


语法二：


1)、 语法一举例：


例子 1：打印时间


例子 2：批量解压缩


2)、 语法二举例


例子 1：从 1 加到 100


例子 2：批量添加指定数量的用户


例子 3：批量删除用户


例子1：过滤合法IP

# 1、 for 循环


for 循环是固定循环，也就是在循环时已经知道需要进行几次的循环，有时也把 for 循环称为计


数循环。for 的语法有如下两种：


## 语法一：


```javascript

for 变量 in 值 1 值 2 值 3…
do
程序
done
```

这种语法中 for 循环的次数，取决于 in 后面值的个数（空格分隔），有几个值就循环几次，并


且每次循环都把值赋予变量。也就是说，假设 in 后面有三个值，for 会循环三次，第一次循环会把值


1 赋予变量，第二次循环会把值 2 赋予变量，以此类推。




## 语法二：


```javascript
for (( 初始值;循环控制条件;变量变化 ))
do
程序
done
```

语法二中需要注意：


-  初始值：在循环开始时，需要给某个变量赋予初始值，如 i=1；


-  循环控制条件：用于指定变量循环的次数，如 i<=100，则只要 i 的值小于等于 100，循环就


会继续；


-  变量变化：每次循环之后，变量该如何变化，如 i=i+1。代表每次循环之后，变量 i 的值都


加 1。


## 1)、 语法一举例：


我们先看看语法一是什么样子的：


### 例子 1：打印时间


```javascript
[root@localhost ~]# vi sh/for.sh
#!/bin/bash
#打印时间
# Author: shenchao （E-mail: shenchao@atguigu.com）
for time in morning noon afternoon evening
do
echo "This time is $time!"
done
```

批量解压缩脚本就应该这样写：


### 例子 2：批量解压缩


```javascript
[root@localhost ~]# vi sh/auto-tar.sh
#!/bin/bash
#批量解压缩脚本
# Author: shenchao （E-mail: shenchao@atguigu.com）
cd /lamp
#进入压缩包目录
ls *.tar.gz > ls.log
#把所有.tar.gz 结尾的文件的文件覆盖到 ls.log 临时文件中
for i in $(cat ls.log)

#读取 ls.log 文件的内容，文件中有多少个值，就会循环多少次，每次循环把文件名赋予变量 i
do
tar -zxf $i &>/dev/null
#加压缩，并把所有输出都丢弃
done
rm -rf /lamp/ls.log
#删除临时文件 ls.log
```

## 2)、 语法二举例


那语法二就和其他语言中的 for 循环更加类似了，也就是事先决定循环次数的固定循环了。先举


个简单的例子：


### 例子 1：从 1 加到 100


```javascript
#!/bin/bash
#从 1 加到 100
# Author: shenchao （E-mail: shenchao@atguigu.com）
s=0
for (( i=1;i<=100;i=i+1 ))
#定义循环 100 次
do
s=$(( $s+$i ))
每次循环给变量 s 赋值
done
echo "The sum of 1+2+...+100 is : $s"
#输出 1 加到 100 的和
```

## 例子 2：批量添加指定数量的用户


```javascript
[root@localhost ~]# vi useradd.sh
#!/bin/bash
#批量添加指定数量的用户
# Author: shenchao （E-mail: shenchao@atguigu.com）
read -p "Please input user name: " -t 30 name
#让用户输入用户名，把输入保存入变量 name
read -p "Please input the number of users: " -t 30 num
#让用户输入添加用户的数量，把输入保存入变量 num
read -p "Please input the password of users: " -t 30 pass
#让用户输入初始密码，把输入保存如变量 pass
if [ ! -z "$name" -a ! -z "$num" -a ! -z "$pass" ]
#判断三个变量不为空
then
y=$(echo $num | sed 's/[0-9]//g')

#定义变量的值为后续命令的结果
#后续命令作用是，把变量 num 的值替换为空。如果能替换为空，证明 num 的值为数字
#如果不能替换为空，证明 num 的值为非数字。我们使用这种方法判断变量 num 的值为数字
if [ -z "$y" ]
#如果变量 y 的值为空，证明 num 变量是数字
then
for (( i=1;i<=$num;i=i+1 ))
#循环 num 变量指定的次数
do
/usr/sbin/useradd $name$i &>/dev/null
#添加用户，用户名为变量 name 的值加变量 i 的数字
echo $pass | /usr/bin/passwd --stdin $name$i &>/dev/null
#给用户设定初始密码为变量 pass 的值
done
fi
fi
```

## 例子 3：批量删除用户


```javascript
[root@localhost ~]# vi sh/userdel.sh
#!/bin/bash
#批量删除用户
# Author: shenchao （E-mail: shenchao@atguigu.com）
user=$(cat /etc/passwd | grep "/bin/bash"|grep -v "root"|cut -d ":" -f 1)
#读取用户信息文件，提取可以登录用户，取消 root 用户，截取第一列用户名
for i in $user
#循环，有多少个普通用户，循环多少次
do
userdel -r $i
#每次循环，删除指定普通用户
done
```





# 例子1：过滤合法IP

```javascript
#!/bin/bash
  1 
  2 
  3 grep "^[0-9]\{1,3\}\.[0-9]\{1,3\}\.[0-9]\{1,3\}\.[0-9]\{1,3\}$" /root/ip.txt > /root/iptest1.txt
  4 
  5 line=$(cat iptest1.txt | wc -l)
  6 
  7 echo " " > iptest2.txt
  8 
  9 for (( i=1;i<=$line;i++))
 10         do
 11                 cat iptest1.txt  |   awk 'NR=='$i' {print} '  >  /root/iptest2.txt
 12                 bb=0
 13                 for j in 1 2 3 4
 14                 do      
 15                         aa=$( cat /root/iptest2.txt | cut -d "." -f $j)
 16                                         
 17                         case $j in
￥ 18                         "1")  if [ $aa -lt 1 -o $aa -gt 255 ]
 19                                 then 
 20                                         $bb=1
 21                                 fi   ;; 
 22                         "*")  if [ "$aa" -lt 0 -o "$aa" -gt 255 ]
 23                                 then
 24                                         $bb=1
 25                                 fi   ;; 
 26                                 
 27                         esac
 28                 done
 29                 if [ "$bb" -eq 0 ]
 30                           then  
 31                                   cat /root/iptest2.txt >> /root/iptest3.txt                                                          
 32                 fi
 33                        
```

