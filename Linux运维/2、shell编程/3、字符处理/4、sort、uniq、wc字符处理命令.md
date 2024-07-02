# **1 排序命令 sort**

```javascript
[root@localhost ~]# sort [选项] 文件名
选项：
    -f： 忽略大小写
    -b： 忽略每行前面的空白部分
    -n： 以数值型进行排序，默认使用字符串型排序
    -r： 反向排序
    -u： 删除重复行。就是 uniq 命令
    -t： 指定分隔符，默认是分隔符是制表符
    -k n[,m]： 按照指定的字段范围排序。从第 n 字段开始，m 字段结束（默认到行尾）
```

- sort 命令默认是用每行开头第一个字符来进行排序的，比如：

```javascript
[root@localhost ~]# sort /etc/passwd
#排序用户信息文件
```

- 如果想要反向排序，请使用“-r”选项：

```javascript
[root@localhost ~]# sort -r /etc/passwd
#反向排序
```

- 如果想要指定排序的字段，需要使用“-t”选项指定分隔符，并使用“-k”选项指定字段号。加

入我想要按照 UID 字段排序/etc/passwd 文件：

```javascript
[root@localhost ~]# sort -t ":" -k 3,3 /etc/passwd
#指定分隔符是“：”，用第三字段开头，第三字段结尾排序，就是只用第三字段排序
```

看起来好像很美，可是如果仔细看看，怎么 daemon 用户的 UID 是 2，反而排在了下面？这是因为

sort 默认是按照字符排序，前面用户的 UID 的第一个字符都是 1，所以这么排序。要想按照数字排序，

请使用“-n”选项：

```javascript
[root@localhost ~]# sort -n -t ":" -k 3,3 /etc/passwd
```

当然“-k”选项可以直接使用“-k 3”，代表从第三字段到行尾都排序（第一个字符先排序，如

果一致，第二个字符再排序，知道行尾）。



# **2 uniq**

```javascript
uniq 命令是用来取消重复行的命令，其实和“sort -u”选项是一样的。命令格式如下：
[root@localhost ~]# uniq [选项] 文件名
选项：
-i： 忽略大小写
-c:统计重复行，有多少
-d或--repeated：仅显示重复出现的行列；
```

# **3 统计命令 wc**

```javascript
[root@localhost ~]# wc [选项] 文件名
选项：
-l： 只统计行数
-w： 只统计单词数
-m： 只统计字符数
```