

1、 while 循环


例子：1 加到 100


2、until 循环


例子：从 1 加到 100


# 1、 while 循环


```javascript
while [ 条件判断式 ]
do
程序
done
```

对 while 循环来讲，只要条件判断式成立，循环就会一直继续，直到条件判断式不成立，循环才


会停止。好吧，我们还是写个 1 加到 100 的例子吧，这种例子虽然对系统管理帮助不大，但是对理解


循环非常有帮助：


## 例子：1 加到 100


```javascript
#!/bin/bash
#从 1 加到 100

# Author: shenchao （E-mail: shenchao@atguigu.com）
i=1
s=0
#给变量 i 和变量 s 赋值
while [ $i -le 100 ]
#如果变量 i 的值小于等于 100，则执行循环
do
s=$(( $s+$i ))
i=$(( $i+1 ))
done
echo "The sum is: $s"
```



# 2、until 循环


再来看看 until 循环，和 while 循环相反，until 循环时只要条件判断式不成立则进行循环，并


执行循环程序。一旦循环条件成立，则终止循环。语法如下：


```javascript
until [ 条件判断式 ]
do
程序
done
```

还是写从 1 加到 100 这个例子，注意和 while 循环的区别：


## 例子：从 1 加到 100


```javascript
[root@localhost ~]# vi sh/until.sh
#!/bin/bash
#从 1 加到 100
# Author: shenchao （E-mail: shenchao@atguigu.com）
i=1
s=0
#给变量 i 和变量 s 赋值
until [ $i -gt 100 ]
#循环直到变量 i 的值大于 100，就停止循环
do
s=$(( $s+$i ))
i=$(( $i+1 ))
done
echo "The sum is: $s"
```

