我们 Linux 系统中有两种常见的分区表 MBR 分区表（主引导记录分区表）和 GPT 分区表（GUID 分区表），其中：


- ** MBR 分区表**：支持的最大分区是 2TB（1TB=1024GB）；最多支持 4 个主分区，或 3 个主分区 1个扩展分区


- ** GPT 分区表**：支持最大 18EB 的分区（1EB=1024PB=1024*1024TB）；最多支持 128 个分区，其中 1 个系统保留分区，127 个用户自定义分区不过 parted 命令也有点小问题，就是命令自身分区的时候只能格式化成** ext2 文件系统**，不支持ext3 文件系统，那就更不用说 ext4 文件系统了（截止到 CentOS 6.8 还是这样，这里只是指不能用parted 命令把分区格式化成 ext4 文件系统，但是 parted 命令还是可以识别 ext4 文件系统的）。

不过这没有太多的影响，因为我们可以先分区再用 mkfs 进行格式化嘛！

**fdisk命令，只适用于MBR分区表。**

**如果需要使用更大的磁盘，就需要使用GPT分区表，这时候就需要使用**

# **一）parted 交互模式**

# **parted :分开****
**

在进行分区之前，需要手工加入一块硬盘，这是前提准备；

**parted  /dev/sdb 命令开始分区，（**

```javascript
[root@server1 ~]# parted /dev/sdb
GNU Parted 3.1
使用 /dev/sdb
Welcome to GNU Parted! Type 'help' to view a list of commands.
(parted) p                                                                
Model: VMware, VMware Virtual S (scsi)
Disk /dev/sdb: 21.5GB
Sector size (logical/physical): 512B/512B
Partition Table: msdos			#表明当前正咋使用的分区表为MBR分区表
Disk Flags: 

Number  Start   End     Size    Type      File system  标志
 1      1049kB  2149MB  2147MB  primary   ext4
 2      2149MB  4296MB  2147MB  primary   ext4
 3      4296MB  21.5GB  17.2GB  extended
 5      4297MB  7518MB  3221MB  logical   ext4
 6      7519MB  10.7GB  3221MB  logical   ext4
```

## **parted 交互命令 说 明**

## **centos7中，此命令中没有mkfs选项，分区完成之后，直接手动mkfs写入文件系统即可。
**

```shell
check NUMBER                           #做一次简单的文件系统检测

cp [FROM-DEVICE] FROM-NUMBER TO-NUMBER #复制文件系统到另外一个分区

help [COMMAND]                         #显示所有的命令帮助

mklabel,mktable LABEL-TYPE             #创建新的磁盘卷标（分区表）

mkfs NUMBER FS-TYPE                     #在分区上建立文件系统  (centos7 中没有）

mkpart PART-TYPE [FS-TYPE] START END     #创建一个分区

mkpartfs PART-TYPE FS-TYPE START END     #创建分区，并建立文件系统

move NUMBER START END                    #移动分区

name NUMBER NAME                         #给分区命名

print [devices|free|list,all|NUMBER]     #显示分区表，活动设备，空闲空间，所有分区

quit                     #退出

rescue START END         #修复丢失的分区

resizepart  NUMBER START END     #修改分区大小(centos6   是 resize)

rm NUMBER                   #删除分区

select DEVICE               #选择需要编辑的设备

set NUMBER FLAG STATE       #改变分区标记

toggle [NUMBER [FLAG]]      #切换分区表的状态l

unit UNIT                   #设置默认的单位

Version                     #显示版本

```

## **二）建立分区
**

## **1、 查看分区
**

```javascript
(parted) print
#输入 print 指令
Model: VMware, VMware Virtual S (scsi) 硬盘参数，是虚拟机啊
Disk /dev/sdb: 21.5GB 硬盘大小
Sector size (logical/physical): 512B/512B 扇区大小
Partition Table: msdos 分区表类型，就是 MBR 分区表
Number Start  End   Size    Type    File   system 标志
1     32.3kB 5379MB 5379MB primary
2     5379MB 21.5GB 16.1GB extended
5     5379MB 7534MB 2155MB logical  ext4
6     7534MB 9689MB 2155MB logical  ext4
#看到了我们使用 fdisk 分的区，其中 1 分区没有格式化，2 分区是扩展分区不能格式化
```

使用 print 可以查看分区表信息，包括硬盘参数，硬盘大小，扇区大小，分区表类型和分区信息。


分区信息总共七列，分别是：


```javascript
 Number：分区号

 Start：分区起始位置，这里不再像 fdisk 用柱面表示，而是使用 Byte 更加直观

 End：分区结束位置

 Size：分区大小

 Type：分区类型

 File system：文件系统类型。我不是说 parted 不支持 ext4 文件系统吗？注意，我一直都是说 parted 不能直接把分区直接格式化成 ext4 文件系统，但是它是可以识别的。

 标志：Flags，就是分区的标记

```

## **2、修改成 GPT 分区表
**




```javascript
(parted) mklabel gpt		
#修改分区表命令
警告: 正在使用 /dev/sdb 上的分区。 由于/dev/sdb 分区已经挂载，所以有警告
注意如果强制修改，原有分区及数据会消失
忽略/Ignore/放弃/Cancel? ignore 输入 ignore 忽略报错
警告: The existing disk label on /dev/sdb will be destroyed and all data on this disk will
be lost. Do you want to continue?
是/Yes/否/No? yes 输入 yes
警告: WARNING: the kernel failed to re-read the partition table on /dev/sdb (设备或资源
忙). As a result, it may not reflect all of
your changes until after reboot. 下次重启后，才能生效
(parted) print 查看下分区表吧
Model: VMware, VMware Virtual S (scsi)
Disk /dev/sdb: 21.5GB
Sector size (logical/physical): 512B/512B
Partition Table: gpt 分区表已经变成 GPT
Number Start End Size File system Name 标志
所有的分区都消失了
```

修改了分区表，如果这块硬盘已经有分区了，那么原有分区和分区中的数据都会消失，而且需要


重启系统才会生效。


还有我们转换分区表的目的是为了支持大于 2TB 的分区，如果分区并没有大于 2TB，那么这步是


可以不执行的。


**注意：一定要把/etc/fstab 文件中和原有分区的内容删除掉，才能重启动。要不系统启动就一定会报错的。**

## **3、 建立分区
**

因为修改过了分区表，所以/dev/sdb 中的所有数据都消失了，所以我们可以重新对这块硬盘分区


了。不过建立分区时，默认文件系统就只能建立成 ext2 了，命令如下：


```javascript
(parted) mkpart
#输入创建分区命令，后面不要参数，全部靠交互指定
分区名称？ []? disk1 	#分区名称，我起名叫 disk1
文件系统类型？ [ext2]? 	#文件系统类型，直接回车，使用默认 ext2
起始点？ 1MB 	#分区从 1MB 开始
结束点？ 5GB 	#分区到 5GB 结束
#分区完成
(parted) print 查看下吧
Model: VMware, VMware Virtual S (scsi)
Disk /dev/sdb: 21.5GB
Sector size (logical/physical): 512B/512B
Partition Table: gpt
Number Start   End    Size   File system Name 标志
1      1049kB 5000MB 4999MB disk1                 分区 1 已经出现
```

不知道大家注意到了吗？我们现在的 print 查看的分区，和第一次查看 MBR 分区表的分区时有些


不一样了，少了 Type 这个字段，也就是分区类型的字段，多了 Name 分区名字段。分区类型是标识主


分区、扩展分区和逻辑分区的，不过这种标识只在 MBR 分区表中使用，我们现在已经变成了 GPT 分区


表了，所以就不再有 Type 类型了。也就说折磨我们很久的主分区、扩展分区和逻辑分区的概念不再


有用了，阿门！


## **4、 建立文件系统
**

**注意：在centos7中，此命令中没有mkfs选项，分区完成后，直接mkfs手动写入文件系统即可。**

分区分完了，我们还需要格式化。不过我们已经知道如果使用 parted 交互命令格式化的话，只


能格式化成 ext2 文件系统。我们这里是要演示下 parted 命令的格式化方法，所以就格式化成 ext2


吧，命令如下：


```javascript
(parted) mkfs
#格式化命令（很奇怪也是 mkfs，但是这只是 parted 的交互命令）
WARNING: you are attempting to use parted to operate on (mkfs) a file system.
parted's file system manipulation code is not as robust as what you'll find in
dedicated, file-system-specific packages like e2fsprogs. We recommend
you use parted only to manipulate partition tables, whenever possible.
Support for performing most operations on most types of file systems
will be removed in an upcoming release.
警告: The existing file system will be destroyed and all data on the partition will be lost.
Do you want to continue?
是/Yes/否/No? yes 警告你格式化数据会丢失，没关系，已经丢失过了
分区编号？ 1
文件系统类型？ [ext2]? 指定文件系统类型，写别的也没有用，直接回车
(parted) print 格式化完成，查看下
Model: VMware, VMware Virtual S (scsi)
Disk /dev/sdb: 21.5GB
Sector size (logical/physical): 512B/512B
Partition Table: gpt
Number Start  End    Size  File system Name 标志
1     1049kB 5000MB 4999MB ext2 disk1 拥有了文件系统
```

如果要格式化成 ext4 文件系统，请 mkfs 命令帮忙吧（注意不是 parted 交互命令中的 mkfs，而


是系统命令 mkfs）！


## **5、 调整分区大小
**

parted 命令还有一大优势，就是可以调整分区的大小（windows 中也可以实现，不过要不需要转


换成动态磁盘，要不需要依赖第三方工具，如硬盘分区魔术师）。起始 Linux 中 LVM 和 RAID 是可以


支持分区调整的，不过这两种方法也可以看成是动态磁盘方法，我们在下一个章节中介绍。使用 parted


命令调整分区要更加简单。


**注意：parted 调整已经挂载使用的分区时，是不会影响分区中的数据的，也就是说数据不
**

**会丢失。但是一定要**

**要调整大小的分区必须已经建立了文件系统（格式化），否则会报错。**

**-调整完毕后，需要从新写入文件系统。**

命令如下：


```javascript
(parted) resizepart
分区编号？ 1 指定要修改的分区编号
起始点？ [1049kB]? 1MB 分区起始位置
结束点？ [5000MB]? 6GB 分区结束位置
(parted) print 查看下
Model: VMware, VMware Virtual S (scsi)
Disk /dev/sdb: 21.5GB
Sector size (logical/physical): 512B/512B
Partition Table: gpt
Number Start End    Size   File system Name 标志
1     1049kB 6000MB 5999MB ext2 disk1 分区大小改变
```

## **6、 删除分区
**

```javascript
(parted) rm
#删除分区命令
分区编号？ 1 指定分区号
(parted) print 查看下
Model: VMware, VMware Virtual S (scsi)
Disk /dev/sdb: 21.5GB
Sector size (logical/physical): 512B/512B
Partition Table: gpt
Number Start End Size File system Name 标志 分区消失
```

还有件事要注意，parted 中所有的操作都是立即生效，

那么到底使用 fdisk 进行分区？还是 parted 命令呢？这个完全看个人习惯，我们更加习惯 fdisk命令。