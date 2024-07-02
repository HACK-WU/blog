# **1）使用declare声明类型方法**

```javascript
[root@localhost ~]# declare [+/-][选项] 变量名
选项：
    -： 给变量设定类型属性
    +： 取消变量的类型属性
    -a： array-数组将变量声明为数组型
    -A：    声明关系型数组        
    -i： int-整型，将变量声明为整数型（integer）
    -r： read-只读，讲变量声明为只读变量。注意，一旦设置为只读变量，既不能修改变量的值，
    	也不能删除变量，甚至不能通过+r 取消只读属性
    -x： 将变量声明为环境变量
    -p： print-打印，显示指定变量的被声明的类型
    -u    将字母值变为大写
    -l    将字母值变为小写
```

## **例子1：数值运算**

```javascript
[root@localhost ~]# aa=11
[root@localhost ~]# bb=22
#给变量 aa 和 bb 赋值
[root@localhost ~]# declare -i cc=$aa+$bb
#声明变量 cc 的类型是整数型，它的值是 aa 和 bb 的和
[root@localhost ~]# echo $cc
33
#这下终于可以相加了
```

## **例子2：数组变量类型**

```javascript
[root@localhost ~]# name[0]="shen chao"
#数组中第一个变量是沈超（大办公室第一个办公桌坐最高大威猛帅气的人）
[root@localhost ~]# name[1]="li ming"
#数组第二个变量是李明（大办公室第二个办公桌坐头发锃亮的人）
[root@localhost ~]# name[2]="tong gang"
#数组第三个变量是佟刚（大办公室第三个办公桌坐眼睛比超哥还小的老师）
[root@localhost ~]# echo ${name}
shen chao
#输出数组的内容，如果只写数组名，那么只会输出第一个下标变量
[root@localhost ~]# echo ${name[*]}
shen chao li ming tong gang
#输出数组所有的内容
```

**注意**

**不过好像在刚刚的例子中，我们并没有把 name 变量声明为数组型啊，其实只要我们在定义变量**

**时采用了“变量名[下标]”的格式，这个变量就会被系统认为是数组型了，不用强制声明。**

## **例子3：环境变量**

**我们其实也可以使用 declare 命令把变量声明为环境变量，和 export 命令的作用是一样的：**

```javascript
[root@localhost ~]# declare -x test=123
#把变量 test 声明为环境变量
```

## **例子 4：只读属性**

注意一旦给变量设定了只读属性，那么这个变量既不能修改变量的值，也不能删除变量，甚至不

能使用“+r”选项取消只读属性。命令如下：

```javascript
[root@localhost ~]# declare -r test
#给 test 赋予只读属性
[root@localhost ~]# test=456
-bash: test: readonly variable
#test 变量的值就不能修改了
[root@localhost ~]# declare +r test
-bash: declare: test: readonly variable
#也不能取消只读属性
[root@localhost ~]# unset test
-bash: unset: test: cannot unset: readonly variable
#也不能删除变量
```

不过还好这个变量只是命令行声明的，所以只要重新登录或重启，这个变量就会消失了。

## **例子 5：查询变量属性和取消变量属性**

变量属性的查询使用“-p”选项，变量属性的取消使用“+”选项。命令如下：

```javascript
[root@localhost ~]# declare -p cc
declare -i cc="33"
#cc 变量是 int 型
[root@localhost ~]# declare -p name
declare -a name='([0]="shen chao" [1]="li ming" [2]="tong gang")'
#name 变量是数组型
[root@localhost ~]# declare -p test
declare -rx test="123"
#test 变量是环境变量和只读变量
[root@localhost ~]# declare +x test
#取消 test 变量的环境变量属性
[root@localhost ~]# declare -p test
declare -r test="123"
#注意，只读变量属性是不能取消的
```