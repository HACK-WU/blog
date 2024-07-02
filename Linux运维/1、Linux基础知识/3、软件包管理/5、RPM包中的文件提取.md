# 1、cpio命令

cpio 命令主要有三种基本模式：“-o”模式指的是 copy-out 模式，就是把数据备份到文件库中；


“-i”模式指的是 copy-in 模式，就是把数据从文件库中恢复；“-p”模式指的是复制模式，就是不


把数据备份到 cpio 库中，而是直接复制为其他文件。命令如下：





```javascript
#还原[root@localhost ~]# cpio -o[vcB] > [文件|设备]
#备份
选项：
-o：copy-out 模式，备份
-v：显示备份过程
-c：使用较新的 portable format 存储方式
-B：设定输入输出块为 5120bytes，而不是模式的 512butes
[root@localhost ~]# cpio -i[vcdu] < [文件|设备]
选项：
-i：copy-in 模式，还原
-v：显示还原过程
-c：使用较新的 portable format 存储方式
-d：还原时自动新建目录
-u：自动使用较新的文件覆盖较旧的文件
```



```javascript
[root@localhost ~]# cpio -p 目标目录
```

举几个例子吧，先来看看使用 cpio 备份数据的方法，命令如下：


例子：利用 find 命令找到文件，备份


```javascript
[root@localhost ~]# find /etc -print | cpio -ocvB > /root/etc.cpio
#利用 find 指定要备份/etc/目录，使用>导出到 etc.cpio 文件
[root@localhost ~]# ll -h etc.cpio
-rw-r--r--. 1 root root 21M 6 月 5 12:29 etc.cpio
#etc.cpio 文件生成
```

再来看看如何恢复 cpio 的备份数据，命令如下：


```javascript
[root@localhost ~]# cpio -idvcu < /root/etc.cpio
#还原 etc 的备份
```




#但是如果大家查看下当前目录/root，会发现没有生成 etc 目录。这是因为备份是/etc 目录使用的是绝对路径，所以


恢复的数据直接恢复到了/etc 系统目录中，而没有生成在/root/etc 中。


在 CentOS5.x 的版本中，是可以利用上面的命令备份与恢复指定的文件。但是到 CentOS6.x 当中，


需要更加严谨。如果备份时使用绝对路径，则恢复的数据会直接到绝对路径指定的路径中，如果需要


把数据恢复到当前目录中，则需要使用相对路径，例如：


备份：


```javascript
[root@localhost ~]# cd /etc
#进入/etc 目录
[root@localhost ~]# find . -print | cpio -ocvB > /root/etc.cpio
#利用 find 指定要备份/etc/目录，使用>导出到 etc.cpio 文件
恢复：
[root@localhost ~]# cd /root
#回到/root 目录中
[root@localhost ~]# mkdir etc_test
#建立恢复测试目录
[root@localhost ~]# cd etc_test
#进入测试目录，数据恢复到此
[root@localhost etc_test]# cpio -idvcu < /root/etc.cpio
#还原/etc 目录的数据，因为备份时使用的是相对路径，则会还原到/root/etc_test/目录下
最后来演示一下 cpio 命令的“-p”复制模式，命令如下：
[root@localhost ~]# cd /tmp/
#进入/tmp 目录
[root@localhost tmp]# rm -rf *
#删除/tmp 目录中所有数据
[root@localhost tmp]# mkdir test
#建立备份目录
[root@localhost tmp]# find /boot/ -print | cpio -p /tmp/test
#备份/boot/目录到/tmp/test/目录中
[root@localhost tmp]# ls test/
boot
#在/tmp/test/目录中备份出了 boot 目录
```

# 2、提取 RPM 包中文件


```javascript
[root@localhost ~]# rpm2cpio 包全名 | cpio -idv .文件绝对路径
```

rpm2cpio 将 rpm 包转换为 cpio 格式的命令


cpio 是一个标准工具，它用于创建软件档案文件和从档案文件中提取文件


举个例子，现在我假设把系统中的/bin/ls 命令不小心误删除了，那么我可以修复回来吗？这时有两


种方法修复，要不就是使用—force 选项覆盖安装一遍 coreutils-8.4-19.el6.i686 包，要不就可以


使用 cpio 命令提取出/bin/ls 命令文件，再把它拷贝到对应位置就可以了。不过我是怎么知道/bin/ls


命令是属于 coreutils-8.4-19.el6.i686 这个软件包的呢？还记得-qf 选项吗？命令如下：


```javascript
[root@localhost ~]# rpm -qf /bin/ls

coreutils-8.4-19.el6.i686
#查看 ls 文件属于哪个软件包
```

那么我们在讲 RPM 包中文件提取，所以我们使用第二章方法，cpio 命令提取出 ls 命令文件，然


后拷贝到对应位置，命令如下：


```javascript
[root@localhost ~]# mv /bin/ls /root/
#把/bin/ls 命令移动到/root 目录下，造成误删除的假象
[root@localhost ~]# ls
-bash: ls: command not found
#这时执行 ls 命令，系统会报错“命令没有找到”
[root@localhost ~]# rpm2cpio /mnt/cdrom/Packages/coreutils-8.4-19.el6.i686.rpm | cpio
-idv ./bin/ls
./bin/ls
24772 块
#提取 ls 命令文件到当前目录下
[root@localhost ~]# cp /root/bin/ls /bin/
#把提取出来的 ls 命令文件复制到/bin 目录下
[root@localhost ~]# ls
anaconda-ks.cfg bin inittab install.log install.log.syslog ls
#恭喜你，ls 命令又可以正常使用了
```

