# **1、分区，并修改为swap分区****（分区表应该是MBR分区表，如果不是要先转换成MBR分区表）**




```javascript
[root@localhost ~]# fdisk /dev/sdb
#拿/dev/sdb 分区
Command (m for help): t 修改分区的系统 ID
Selected partition 1 只有一个分区，所以不用选择分区了
Hex code (type L to list codes): 82 改为 swap 的 ID
Changed system type of partition 1 to 82 (Linux swap / Solaris)
```

# **2、格式化mkswap  /dev/sdb1
**




```javascript
[root@localhost ~]# mkswap /dev/sdb1
Setting up swapspace version 1, size = 522076 KiB
no label, UUID=c3351dc3-f403-419a-9666-c24615e170fb
```




# **3、使用swap分区**

buffer

cache:

buffer：

cache:

在使用 swap 分区之前，我们先来说说 

**free,查看内存使用情况：**

```javascript
[root@localhost ~]# free
total used free shared buffers cached
Mem: 1030796 130792 900004 0 15292 55420
-/+ buffers/cache: 60080 970716
Swap: 2047992 0 2047992
```