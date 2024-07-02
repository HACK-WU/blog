# **1、sed命令**

**sed 主要是用来将数据进行选取、替换、删除、新增的命令，我们看看命令的语法：**

```javascript
[root@localhost ~]# sed [选项] ‘[动作]’ 文件名
选项：
    -n： 一般 sed 命令会把所有数据都输出到屏幕，如果加入此选择，则只会把经过 sed 命令处理的行输出到屏幕。
    -e： 允许对输入数据应用多条 sed 命令编辑。
    -f 脚本文件名： 从 sed 脚本中读入 sed 操作。和 awk 命令的-f 非常类似。
    -r： 在 sed 中支持扩展正则表达式。regexp
    -i： 用 sed 的修改结果直接修改读取数据的文件，而不是由屏幕输出 
动作：
    a \： add-追加，在当前行后添加一行或多行。添加多行时，除最后 一行外，每行末尾需要用“\”代表数据未完结。
    c \： change-行替换（直接替换一整行），改变，用 c 后面的字符串替换原数据行，替换多行时，除最后一行外，每行末尾需用“\”代表数据未完结。
    i \： insert-插入，在当期行前插入一行或多行。插入多行时，除最后 一行外，每行末尾需要用“\”代表数据未完结。
    d： delete-删除，删除指定的行。

    p： print-打印，输出指定的行。
    s： string-字串替换，用一个字符串替换另外一个字符串。格式为“行范围 s/旧字串/新字串/g”（和 vim 中的替换格式类似）。
        -或者使用 /旧字符窜/c新字符串/    #就会将就字符串那一行整个替换掉
    w: ，可以将对应的内容，写入到其他文件中。        
    h:暂存
    g:取出暂存的内容
    {h；d}：{}内可以执行多个命令，这里的意思就是先暂存，再删除，类似于剪切。
```

对 sed 命令大家要注意，sed 所做的修改并不会直接改变文件的内容（如果是用管道符接收的命

令的输出，这种情况连文件都没有），而是把修改结果只显示到屏幕上，除非使用“-i”选项才会直

接修改文件。

## **1）显示行数据**

闲话少叙，直奔主题，我们举几个例子来看看sed命令到底是干嘛的。假设我想查看下student.txt

的第二行，那么就可以利用“p”动作了：

```javascript
[root@localhost ~]# sed '2p' student.txt
ID Name PHP Linux MySQL Average
1 Liming 82 95 86 87.66
1 Liming 82 95 86 87.66
2 Sc 74 96 87 85.66
3 Tg 99 83 93 91.66
```

好像看着不怎么顺眼啊！“p”命令确实输出了第二行数据，但是 sed 命令还会把所有数据都输

出一次，这时就会看到这个比较奇怪的结果。那如果我想指定输出某行数据，就需要“-n”选项的帮

助了：

```javascript
[root@localhost ~]# sed -n '2p' student.txt
1 Liming 82 95 86 87.66
```

## **2）删除行数据**

```javascript
[root@localhost ~]# sed '2,4d' student.txt
#删除第二行到第四行的数据
ID Name PHP Linux MySQL Average
[root@localhost ~]# cat student.txt
#但是文件本身并没有修改
ID Name PHP Linux MySQL Average
1 Liming 82 95 86 87.66
2 Sc 74 96 87 85.66
3 Tg 99 83 93 91.66
```

## **3）插入行数据**

```javascript
[root@localhost ~]# sed '2a hello' student.txt
#在第二行后加入 hello
```

“a”会在指定行后面追加入数据，如果想要在指定行前面插入数据，则需要使用“i”动作：

```javascript
[root@localhost ~]# sed '2i hello > world' student.txt
#在第二行前插入两行数据
```

如果是想追加或插入多行数据，除最后一行外，每行的末尾都要加入“\”代表数据未完结。再

来看看“-n”选项的作用：

```javascript
[root@localhost ~]# sed -n '2i hello \
#只查看 sed 命令操作的数据

> world' student.txt
“-n”只查看 sed 命令操作的数据，而不是查看所有数据。
```

## **4）修改行数据**

```javascript
[root@localhost ~]# cat student.txt | sed '2c No such person'
```

sed 命令默认情况是不会修改文件内容的，如果我确定需要让 sed 命令直接处理文件的内容，可

以使用“-i”选项。不过要小心啊，这样非常容易误操作，在操作系统文件时请小心谨慎。可以使用

这样的命令：

```javascript
[root@localhost ~]# sed -i '2c No such person' student.txt
```

## **5）字符串替换**

“c”动作是进行整行替换的，如果仅仅想替换行中的部分数据，就要使用“s”动作了。s 动作

的格式是：

```javascript
[root@localhost ~]# sed ‘s/旧字串/新字串/g’ 文件名
```

替换的格式和 vim 非常类似，假设我觉得我自己的 PHP 成绩太低了，想作弊给他改高点，就可以

这样来做：

```javascript
[root@localhost ~]# sed '3s/74/99/g' student.txt
#在第三行中，把 74 换成 99
```

这样看起来就比较爽了吧。如果我想把 Tg 老师的成绩注释掉，让他不再生效（没有成绩了吧？

补考去吧？）。可以这样做：

```javascript
[root@localhost ~]# sed '4s/^/#/g' student.txt
#这里使用正则表达式，“^”代表行首
```

在 sed 中只能指定行范围，所以很遗憾我在他们两个的中间，不能只把他们两个注释掉，那么我

们可以这样：

```javascript
[root@localhost ~]# sed -e 's/Liming//g ; s/Tg//g' student.txt
#同时把“Liming”和“Tg”替换为空
```

“-e”选项可以同时执行多个 sed 动作，当然如果只是执行一个动作也可以使用“-e”选项，但

是这时没有什么意义。还要注意，多个动作之间要用“；”号或回车分割，例如上一个命令也可以这

样写：

```javascript
[root@localhost ~]# sed -e 's/Liming//g > s/Tg//g' student.txt
```