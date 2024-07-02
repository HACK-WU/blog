**find文件查找**

- 实时查找工具，通过遍历指定路径下的文件系统完成文件查找

- 工作特点

- 查找速度略慢

- 精确查找

- 实时查找

- 可以满足多种条件匹配

```
find [选项] [路径] [查找条件 + 处理动作]
查找路径：指定具体目录路径，默认是当前文件夹
查找条件：指定的查找标准（文件名/大小/类型/权限等），默认是找出所有文件
处理动作：对符合条件的文件做什么操作，默认输出屏幕
```

# 1、查找条件

```
根据文件名查找：
 -name "filename" 支持global
 -iname "filename" 忽略大小写
 -regex "PATTERN" 以Pattern匹配整个文件路径字符串，而不仅仅是文件名称
根据属主和属组查找：
 -user USERNAME：查找属主为指定用户的文件
 -group GROUPNAME：查找属组为指定属组的文件
 -uid UserID：查找属主为指定的ID号的文件
 -gid GroupID：查找属组为指定的GID号的文件
 -nouser：查找没有属主的文件
 -nogroup：查找没有属组的文件
根据文件类型查找：
 -type Type：
 f/d/l/s/b/c/p
根据文件大小来查找：
-size [+|-]N[bcwkMG]
根据时间戳:
 天：
   -atime [+|-]N
   -mtime
   -ctime
 分钟：
   -amin N
   -cmin N
   -mmin N
根据权限查找：
 -perm [+|-]MODE
 MODE：精确权限匹配
 /MODE：任何一类(u，g，o)对象的权限中只要能一位匹配即可
 -MODE：每一类对象都必须同时拥有为其指定的权限标准
组合条件：
 与：-a
 或：-o
 非：-not
相关案例：找出/tmp目录下，属主不是root，且文件名不是fstab的文件：
find /tmp \( -not -user root -a -not -name 'fstab'\) -ls
```

## 1）根据文件名查找

```
[root@localhost ~]# find /etc -name "ifcfg-ens33"
[root@localhost ~]# find /etc -iname "ifcfg-ens33" # 忽略大小写
[root@localhost ~]# find /etc -iname "ifcfg*"
```

## 2）按文件大小

```
[root@localhost ~]# find /etc -size +5M # 大于5M
[root@localhost ~]# find /etc -size 5M # 等于5M
[root@localhost ~]# find /etc -size -5M # 小于5M
[root@localhost ~]# find /etc -size +5M -ls # 找到的处理动作-ls
```

## 3）指定查找的目录深度

```
[root@localhost ~]# find / -maxdepth 3 -a -name "ifcfg-ens33" # 最大查找深度
# -a是同时满足，-o是或
[root@localhost ~]# find / -mindepth 3 -a -name "ifcfg-ens33" # 最小查找深度
```

## 4）按时间找

```
[root@localhost ~]# find /etc -mtime +5 # 修改时间超过5天
[root@localhost ~]# find /etc -mtime 5 # 修改时间等于5天
[root@localhost ~]# find /etc -mtime -5 # 修改时间5天以内
```

## 5）按照文件属主、属组找

```
[root@localhost ~]# find /home -user xwz # 属主是xwz的文件
[root@localhost ~]# find /home -group xwz
[root@localhost ~]# find /home -user xwz -group xwz
[root@localhost ~]# find /home -user xwz -a -group root
[root@localhost ~]# find /home -user xwz -o -group root
[root@localhost ~]# find /home -nouser # 没有属主的文件
[root@localhost ~]# find /home -nogroup # 没有属组的文件
```

## 6）按文件类型

```
[root@localhost ~]# find /dev -type d 
```

## 7）按文件权限

```
[root@localhost ~]# find / -perm 644 -ls
[root@localhost ~]# find / -perm -644 -ls # 权限小于644的
[root@localhost ~]# find / -perm 4000 -ls
[root@localhost ~]# find / -perm -4000 -ls
```

## 8）按正则表达式

```
[root@localhost ~]# find /etc -regex '.*ifcfg-ens[0-9][0-9]'
# .* 任意多个字符
# [0-9] 任意一个数字
```

## 9）条件组合

```
-a：多个条件and并列
-o：多个条件or并列
-not：条件取反
```

## 2、处理动作

```
-print：默认的处理动作，显示至屏幕
-ls：类型于对查找到的文件执行“ls -l”命令
-delete：删除查找到的文件
-fls /path/to/somefile：查找到的所有文件的长格式信息保存至指定文件中
-ok COMMAND {}\：对查找到的每个文件执行由COMMAND指定的命令
对于每个文件执行命令之前，都会交换式要求用户确认
-exec COMMAND {} \：对查找到的每个文件执行由COMMAND指定的命令
[root@server1 ~]# find /etc/init.d/ -perm -111 -exec cp -r {} dir1/ \;
{}：用于引用查找到的文件名称自身
注意：find传递查找到的文件至后面指定的命令时，查找到所有符合条件的文件一次性传递给后面的命
令；另一种方式可规避此问题
find | xargs COMMAND
```