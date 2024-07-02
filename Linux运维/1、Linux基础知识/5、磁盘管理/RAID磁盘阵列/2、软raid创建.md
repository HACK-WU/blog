# 1、环境准备

添加1一个大小为20G的虚拟硬盘，并分10个分区

# 2、创建RAID0

## 2.1 创建RAID0

```shell
#安装mdadm工具：
yum -y install mdadm
#创建raid0:
[root@server ~]# mdadm --create /dev/mdo --raid-devices=2 /dev/sdb[12] --leve1=0
mdadm:Defaulting to version 1.2 metadata
mdadm:array /dev/mdo started.
# /dev/md0   md0名字可以自定义，随便写
# --create  [raid名称] 创建raid  
# --raid-devices=2    指定需要使用的磁盘的个数为2个
# /dev/sdb[12]        指明具体需要使用的磁盘为/dev/sdb1 和/dev/sdb2
# --leve1=0            #指定raid的级别为0
或者
[root@server ~]mdadm -C /dev/mdo -l 0 -n 2  /dev/sdc1 /dev/sdc2
-c:创建软raid
-l:指定raid级别
-n:指定raid中设备个数
```

## 2.2 查看raid情况

```
[root@server1 ~]# mdadm  -D /dev/md0  #查看raid情况
/dev/md0:
           Version : 1.2
     Creation Time : Wed Dec  7 22:37:09 2022
        Raid Level : raid0
        Array Size : 4188160 (3.99 GiB 4.29 GB)
      Raid Devices : 2
     Total Devices : 2
       Persistence : Superblock is persistent

       Update Time : Wed Dec  7 22:37:09 2022
             State : clean 
    Active Devices : 2
   Working Devices : 2
    Failed Devices : 0
     Spare Devices : 0

        Chunk Size : 512K

Consistency Policy : none

              Name : server1:0  (local to host server1)
              UUID : 111a9d04:96db8962:c9c3b51c:e85eb9b0
            Events : 0

    Number   Major   Minor   RaidDevice State
       0       8       17        0      active sync   /dev/sdb1
       1       8       18        1      active sync   /dev/sdb2
# 创建好的raid就可当成一个全新的设备去正常使用即可。
```

## 2.3 格式化并挂载raid

```
[root@server1 ~]# mkfs.ext4 /dev/md0        #格式化文件系统
[root@server1 ~]# mkdir /u01                #创建挂载点
[root@server1 ~]# mount /dev/md0  /u01      #临时挂载raid
[root@server1 ~]# df -h                     #查看挂载情况
文件系统                 容量  已用  可用 已用% 挂载点
.........
/dev/md0                 3.9G   16M  3.7G    1% /u01
```

## 2.4 测试

```
[root@server1 ~]# iostat -m -d /dev/sdb[12] 2            #监控sdb1和sdb2的io情况
Linux 3.10.0-957.el7.x86_64 (server1) 	2022年12月07日 	_x86_64_	(4 CPU)

Device:            tps    MB_read/s    MB_wrtn/s    MB_read    MB_wrtn
sdb1              0.21         0.00         0.04          4         65
sdb2              0.19         0.00         0.04          4         65
。。。。。。。。
[root@server1 ~]# dd if=/dev/zer0 of=/u01/bigfile  bs=1M count=512    #向挂载点中写入一个512M大写的文件
```

# 3、创建RAID1

## 3.1 创建RAID1

```
创建raid1:
[root@server ~]mdadm -C /dev/md1 -l 1 -n 2 /dev/sdb[35]
```

## 3.2 查看状态

```
[root@server ~]watch -n 1 cat /proc/mdstat    watch命令监控该文件变化情况，1秒钟显示一次
或者直接查看
[root@server ~]cat /proc/mdstat
Personalities [raido][raid1]
md1 active raid1 sdc6[1]sdc5[0]
1059200b1 ocks super1.2[2/2][UU]
[=====>,,,,,,,.]
resync=25.2%(267520/1059200)finish=0.3min
speed=38217K/sec
    #以上信息说明两块盘在进行同步，100%说明同步完成
mdo active raido sdc2[1]sdc1[0]
    2119680 blocks super 1.2 512k chunks
unused devices:<none>
root@server1 ~]# mdadm -D /dev/md1
/dev/md1:
           Version : 1.2
     Creation Time : Thu Dec  8 13:52:29 2022
        Raid Level : raid1
        Array Size : 2094080 (2045.00 MiB 2144.34 MB)
     Used Dev Size : 2094080 (2045.00 MiB 2144.34 MB)
      Raid Devices : 2
     Total Devices : 2
       Persistence : Superblock is persistent

       Update Time : Thu Dec  8 13:52:32 2022
             State : clean 
    Active Devices : 2
   Working Devices : 2
    Failed Devices : 0
     Spare Devices : 0

Consistency Policy : resync

              Name : server1:1  (local to host server1)
              UUID : dcc0a4b6:1b84991b:3badbe52:b73aee6e
            Events : 17

    Number   Major   Minor   RaidDevice State
       0       8       19        0      active sync   /dev/sdb3
       1       8       21        1      active sync   /dev/sdb5
```

## 3.3 格式化并挂载

```
root@server1 ~]# mkfs.ext4  /dev/md1    #写入文件系统（格式化）
root@server1 ~]# mkdir /u02    #创建挂载点
root@server1 ~]# mount /dev/md1  /u02  #挂载
root@server1 ~]# df  -h    #查看挂载情况 
```

## 3.4 测试验证热插拔

```
```