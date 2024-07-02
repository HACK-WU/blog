# 1、函数


```javascript
function 函数名 () {
程序
}
```

那我们写一个函数吧，还记得从 1 加到 100 这个循环吗？这次我们用函数来实现它，不过不再是





从 1 加到 100 了，我们让用户自己来决定加到多少吧：


例子：


```javascript
[root@localhost ~]# vi sh/function.sh
#!/bin/bash
#接收用户输入的数字，然后从 1 加到这个数字
# Author: shenchao （E-mail: shenchao@atguigu.com）
function sum () {
#定义函数 sum
s=0
for (( i=0;i<=$1;i=i+1 ))
#循环直到 i 大于$1 为止。$1 是函数 sum 的第一个参数
#在函数中也可以使用位置参数变量，不过这里的$1 指的是函数的第一个参数
do
s=$(( $i+$s ))
done
echo "The sum of 1+2+3...+$1 is : $s"
#输出 1 加到$1 的和
}
read -p "Please input a number: " -t 30 num
#接收用户输入的数字，并把值赋予变量 num
y=$(echo $num | sed 's/[0-9]//g')
#把变量 num 的值替换为空，并赋予变量 y
if [ -z "$y" ]
#判断变量 y 是否为空，以确定变量 num 中是否为数字
then
sum $num
#调用 sum 函数，并把变量 num 的值作为第一个参数传递给 sum 函数
else
echo "Error!! Please input a number!"
#如果变量 num 的值不是数字，则输出报错信息
fi
```

