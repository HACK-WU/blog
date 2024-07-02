# 1、 exit 语句


系统是有 exit 命令的，用于退出当前用户的登录状态。可是在 Shell 脚本中，exit 语句是用来


退出当前脚本的。也就是说，在 Shell 脚本中，只要碰到了 exit 语句，后续的程序就不再执行，而


直接退出脚本。exit 的语法如下：


```javascript
exit [返回值]
```

如果 exit 命令之后定义了返回值，那么这个脚本执行之后的返回值就是我们自己定义的返回值。


可以通过查询$?这个变量，来查看返回值。如果 exit 之后没有定义返回值，脚本执行之后的返回值


是执行 exit 语句之前，最后执行的一条命令的返回值。写一个 exit 的例子：


```javascript
[root@localhost ~]# vi sh/exit.sh

#!/bin/bash
#演示 exit 的作用
# Author: shenchao （E-mail: shenchao@atguigu.com）
read -p "Please input a number: " -t 30 num
#接收用户的输入，并把输入赋予变量 num
y=$(echo $num | sed 's/[0-9]//g')
#如果变量 num 的值是数字，则把 num 的值替换为空，否则不替换
#把替换之后的值赋予变量 y
[ -n "$y" ] && echo "Error! Please input a number!" && exit 18
#判断变量 y 的值如果不为空，输出报错信息，退出脚本，退出返回值为 18
echo "The number is: $num"
#如果没有退出加班，则打印变量 num 中的数字
```

这个脚本中，大家需要思考，如果我输入的不是数字，那么“echo "The number is: $num"”这


个脚本是否会执行？当然不会，因为如果输入的不是数字，“[ -n "$y" ] && echo "Error! Please


input a number!" && exit 18”这句脚本会执行，exit 一旦执行脚本就会终止。执行下这个脚本：


```javascript
[root@localhost ~]# chmod 755 sh/exit.sh
#给脚本服务执行权限
[root@localhost ~]# sh/exit.sh 执行脚本
Please input a number: test 输入值不是数字，而是 test
Error! Please input a number! 输出报错信息，而不会输出 test
[root@localhost ~]# echo $? 查看下返回值
18 返回值居然真是 18 啊
[root@localhost ~]# sh/exit.sh
Please input a number: 10 输入数字 10
The number is: 10 输出数字 10
```

