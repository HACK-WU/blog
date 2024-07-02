# **1、磁盘配额的概念**

就是为用户设置他们能够使用的磁盘的最大容量和能够建立的最大的文件数量；

## **2、磁盘配额的数量**

## **1）内核必须支持磁盘配额可以通过以下命令查看，搜索若有如下结果，则支持磁盘配额****（quota,配额）**

```javascript
[root@bogon ~]# grep QUOTA /boot/config-3.10.0-1160.el7.x86_64 
CONFIG_NETFILTER_XT_MATCH_QUOTA=m
CONFIG_XFS_QUOTA=y
CONFIG_QUOTA=y
CONFIG_QUOTA_NETLINK_INTERFACE=y
CONFIG_PRINT_QUOTA_WARNING=y
# CONFIG_QUOTA_DEBUG is not set
CONFIG_QUOTA_TREE=y
CONFIG_QUOTACTL=y
CONFIG_QUOTACTL_COMPAT=y
```

## **2）系统中必须安装quota工具；（yum install -y quota)**

```javascript
[root@localhost ~]# rpm -qa | grep quota
quota-3.17-16.el6.i686
```

# **3、具体磁盘配额概念**

**1）用户配额和组配额**

- 用户配额：就是设定每个用户最多可以使用磁盘的多大容量，比如设定每个用户最多使用100MB；

- 组配额:就是设定每个用户组，里面的用户一起最多可以使用的容量，比如有三个用户，组配额为300MB，所以就是三个用户一起最多能用300MB。如果user就用了300MB，那么use2和user3就都不能使用。


**2）磁盘容量限制和文件个数限制**

磁盘容量限制，就是为了保证磁盘的容量不会被全部占用。

因为磁盘中，INode的数量是有限制的，所以限制文件的同时，也是限制了iNode，以此防止iNode被全部占用。


**3）软限制和硬限制**

- 软限制：就是警告作用，不会真的限制，比如90MB，到90MB的时候就会提醒用户，超标了。

- 硬限制：就是一旦达到了临界值，就会立马限制用户继续使用。


**4）宽限时间**

如果用户的空间占用数处于软限制和硬限制之间，统会在用户登陆时警告用户磁盘将满，，这个时间就是宽限时间，默认是 7 天。如果达到了宽限时间，用户的磁盘占用量还超过软限制，那么软限制就会升级为硬限制。

# **4、磁盘配额规划**

我们开始磁盘配额实验，首先我们来规划下我们的实验：


 磁盘配额是限制的普通用户在分区上使用磁盘空间和文件个数的，所以我们需要指定一个分区。那么我们手工建立一个 5GB 的/dev/sdb1 分区，把它挂载到/disk 目录当中。


 还有我们需要建立被限制的用户和用户组。那么我们假设需要限制 user1、user2 和 user3用户，这三个用户属于 test 用户组。


 其中 test 组磁盘容量硬限制为 500MB，软限制 450MB，文件个数不做限制。user1 用户为了便于测试，磁盘容量硬限制为 50MB，软限制为 40MB，文件个数硬限制限制为 10 个，软限制为 8 个。user2 和 user3 用户磁盘容量硬限制为 250MB，软限制为 200MB，文件个数不做限制。


 大家发现 user1、user2 和 user3 用户加起来的磁盘容量限制为 550MB，超过了 test 组的磁盘容量限制 500MB。这样的话，某个用户可能达不到自己的用户限制，而达到组限制时就不能再写入数据了。也就是说，如果用户限制和组限制同时存在，那么哪个限制更小，哪个限制优先生效。


 系统宽限时间我们改为 8 天。

# **5、磁盘配额步骤 ****Centos6**

## **1）分 5GB 的/dev/sdb1 分区，并将它挂载到/disk 目录当中
**

## **2）建立需要做限制的用户和用户组
**

```javascript
[root@localhost ~]# groupadd test
[root@localhost ~]# useradd -G test user1
[root@localhost ~]# useradd -G test user2
[root@localhost ~]# useradd -G test user3
[root@localhost ~]# passwd user1
[root@localhost ~]# passwd user2
[root@localhost ~]# passwd user3
```

## **3）在分区上开启磁盘配额功能
**

```javascript
[root@localhost ~]# mount -o remount,usrquota,grpquota /disk
#重新挂载/disk 分区，并加入用户和用户组的磁盘配额功能
```

我们要想永久生效，则需要修改/etc/fstab 文件，改成：


```javascript
[root@localhost ~]# vi /etc/fstab
/dev/sdb1 /disk ext4 defaults ,usrquota,grpquota 0 0
…省略部分输出…
[root@localhost ~]# mount –o remount /disk
#修改配置文件如果想要生效，必须重启系统，否则也需要把分区重新挂载一遍。
```

## **4）、建立磁盘配额的配置文件
**

```javascript
[root@localhost ~]# quotacheck [选项] [分区名]
选项：
-a：扫描/etc/mtab 文件中所有启用磁盘配额功能的分区。如果加入此参数，命令后面就不需要加入分区名了
-c：不管原有的配置文件，重新扫描并建立新的配置文件
-u：建立用户配额的配置文件，也就是生成 aquota.user 文件
-g：建立组配额的配置文件，会生成 aquota.group 文件
-v：显示扫描过程
-m：强制以读写的方式扫描文件系统，和-M 类似。一般扫描根分区时使用。
-f：强制扫描文件系统，并写入新的配置文件。一般扫描新添加的硬盘分区时使用
```

```javascript
[root@localhost ~]# quotacheck -avug
```

**需要关闭 SELinux，否则会报错**

```javascript
[root@server1 ~]# getenforce 
Enforcing
[root@server1 ~]# setenforce 0	#0是关闭，1开启 临时生效
[root@server1 ~]# getenforce 
Permissive
#永久生效
[root@server1 ~]# vim /etc/selinux/config 
SELINUX=disabled	#修改为这个，reboot生效


```




```javascript
[root@localhost ~]# ll /disk/
总用量 24
-rw------- 1 root root 6144 4 月 17 01:08 aquota.group
-rw------- 1 root root 6144 4 月 17 01:08 aquota.user
#/disk 目录中两个配额配置文件已经建立
```

如果需要给根分区开启配额功能，需要：


```javascript
[root@localhost ~]# vi /etc/fstab
UUID=c2ca6f57-b15c-43ea-bca0-f239083d8bd2 / ext4 defaults ,usrquota,grpquota 1 1
#开启/分区的配额功能
[root@localhost ~]# mount -o remount /
#重新挂载/分区
[root@localhost ~]# quotacheck -avugm
```

如果我们自动扫描/分区建立配额配置文件时，因为/分区已经挂载成读写系统，而 quotacheck需要把分区先挂载成只读分区，然后建立配置文件，最后再挂载回来，所以不能直接在/分区建立配置文件。这时就需要使用-m 强制以读写方式扫描文件系统了

## **5）、 设置用户和组的配额限制
**

```javascript
[root@localhost ~]# edquota [选项] [用户名或组名]
选项：
-u 用户名： 设定用户配额
-g 组名： 设定组配额
-t： 设定宽限时间
-p： 复制配额限制。如果已经设定好某个用户的配额限制，其他用户的配额限
制如果和这个用户相同，那么可以直接复制配额限制，而不用都手工指定
```

我们给 user1 用户设定的配额限制是：磁盘空间软限制是 40MB，硬限制是 50MB；文件个数的软限制是 8 个，硬限制是 10 个（稍微小一点，一会测试时方便测试）。命令如下：


```javascript
[root@localhost ~]# edquota -u user1
#edquota 命令进入之后，就是标准的 vi 操作方法
Disk quotas for user user1 (uid 500):
#磁盘配额是设定用户 user1（UID 是 500）
Filesystem blocks soft hard inodes soft hard
/dev/sdb1  0      0     0    0     0     0
#分区名 已占用容量 软限制 硬限制 已占用文件数 软限制 硬限制
Disk quotas for user user1 (uid 500):
Filesystem blocks soft   hard  inodes soft hard
/dev/sdb1  0      40000  50000  0      8    10
#不用对齐，是七列就行
```

再给 user2 用户配置限额，user2 用户要求是空间软限制 250MB，硬限制 250MB，文件个数不做限制：


```javascript
[root@localhost ~]# edquota -u user2
Disk quotas for user user2 (uid 501):
Filesystem blocks soft hard inodes soft hard
/dev/sdb1 0 250000 300000 0 0 0
```

接下来给 test 组配置限额，test 组要求是空间软限制是 450MB，硬限制 500MB，文件个数不做限制：


```javascript
[root@localhost ~]# edquota -g test
Disk quotas for group test (gid 500):
Filesystem blocks soft hard inodes soft hard
/dev/sdb1 0 450000 500000 0 0 0
```

## **6）、 配额复制
**

user3 用户的配额值和 user2 用户完全一样，我们就可以使用 user2 用户作为模板进行复制。这样我们如果需要建立大量的配额值一致的用户时，就会非常方便，不用一个个手工建立了。复制命令如下：


```javascript
[root@localhost ~]# edquota -p user2 -u user3
#命令 -p 源用户 -u 目标用户
```

## **7）、修改宽限时间
**

我们要求把宽限时间改为 8 天，修改命令如下：


```javascript
[root@localhost ~]# edquota –t
Grace period before enforcing soft limits for users:
Time units may be: days, hours, minutes, or seconds
Filesystem Block grace period Inode grace period
/dev/sdb1 8days 8days
#分区名 容量的宽限时间 个数的宽限时间
```

## **8）、启动和关闭配额
**

配额的配置完成，接下来只需要启动配额就大功告成了，启动命令如下：


```javascript
[root@localhost ~]# quotaon [选项] [分区名]
选项：
-a：依据/etc/mtab 文件启动所有的配额分区。如果不加-a，后面就一定要指定分区名
-u：启动用户配额
-g：启动组配额
-v：显示启动过程的信息
[root@localhost ~]# quotaon -vug /disk/
/dev/sdb1 [/disk]: group quotas turned on
/dev/sdb1 [/disk]: user quotas turned on
#启动/disk 分区的配额
[root@localhost ~]# quotaon –avug
#这条命令也可以
```

关闭配额的命令如下：





```javascript
[root@localhost ~]# quotaoff [选项] [分区名]
选项
-a：依据/etc/mtab 文件关闭所有的配额分区。如果不加-a，后面就一定要指定分区名
-u：关闭用户配额
-g：关闭组配额
[root@localhost ~]# quotaoff –a
#依据/etc/mtab 文件关闭配额分区
```

# **5.01 鸟哥的磁盘配额步骤 Centos7**

```javascript
1、有五个用户：myquota{1..2}     ；密码均为：123 ；初始组均为：myquotagrp;
2、每个账号的磁盘容量限制：soft:250MB;		hard:300MB;
3、用户组配额：myquota 的限制为1000MB；
    	或者使用 目录配额：设定共享目录/disk5/myquota/  的容量最多有500MB；用户对此目录拥有全部的权限；
    	**注意：用户组配额和目录配额不能同时存在**
4、宽限时间grace time：14天；
```

## **1、使用脚本建立五个账号：**

addcount.sh:

```javascript
[root@server1 ~]# vim addaccount.sh
#!/bin/bash
#使用脚本来建立磁盘配额的所需环境

groupadd myquotagrp     #建立一个用户组
mkdir   /disk5/myquota	
chgrp   myquotagrp      /disk5/myquota   #更改这个目录的所属组
chown   2770 /disk5/myquota      #更改这个目录的权限

for username in myquota1 myquota2 myquota3 myquota4 myquota5
do
        useradd -G myquotagrp   $username       #指定附加组myquotagrp
        echo “123” | passwd --stdin $username
        touch test_$username	#为用户创建一个测试文件
        abc=test_$username		
        chown $username:  $abc		将这个测试文件的所有者修改为刚刚创建的那个用户
done


```

## **注意：这里在创建用户的同时，又为每一个创建了一个文件，并修改了文件的所有者，目的是为了后面使用**

**xfs_quota -x -c "report -ubih"时能够显示出用户的配额信息，如果这个目录中没有对应的用户的文件，那么这个**

**命令就不会显示相应的用户的信息；**

**-如果一开始就将共享目录设置在家目录（/home)当中的话，就不需要创建这些文件了，因为创建用户时，就已经穿件了对应的文件。**

## **2、文件系统的支持与查看**

查看文件系统是ext4还是xfs,这个实验使用的是xfs文件系统。

**注意xfs文件系统不需要额外的纪录文件，而且能够针对目录进行磁盘配额；**

**df -hT /disk5/myquota**

```javascript
[root@server1 ~]# df -hT /disk5/myquota/
文件系统       类型  容量  已用  可用 已用% 挂载点
/dev/sdb5      xfs   5.0G   33M  5.0G    1% /disk5

```

## **3、开启磁盘配额功能：**

使用mount -o remount,usrquota,grpquota /home 来开启磁盘配额功能，但是只是临时生效；

永久生效写入/etc/fstab文件中

```javascript
[root@server1 ~]# mount -o remount,usrquota,grpquota /home 
或者
[root@server1 ~]# vim /etc/fstab
UUID=ffd14010-cc0b-4258-8b86-9dc193feebe1 /disk5 xfs    defaults,usrquota,grpquota      0 0 
[root@server1 ~]# mount | grep disk5
/dev/sdb5 on /disk5 type xfs (rw,relatime,attr2,inode64,usrquota,grpquota)	#已经开启

[root@server1 ~]# umount /disk5	#卸载
[root@server1 ~]# mount -a			#重新挂载
[root@server1 myquota]# mount | grep disk5
/dev/sdb5 on /disk5 type xfs (rw,relatime,attr2,inode64,usrquota,grpquota)
#可以看到usrquota,grpquota,磁盘配额开启成功
```

## **4、xfs_quota查看磁盘配额报告数据：**

使用xfs_quota命令查看quota报告

```javascript
xfs_quota命令格式:xfs_quota -x -c "comm" [mount_dir]
    -x专家模式，只有使用了该参数，才能使用-c指定命令
    -c指定命令
    	-print 列出目前主机内的文件系统参数等数据
    	-df 与系统的df命令一样
    	-report （reprot,报告）列出目前的quota项目，有ugr(user/group/project)及bi（block,inode)等数据
    	-state 说明目前支持quota的文件系统的信息
     		state的选项（如果不加，默认显示全部）：
       		-u:只显示user的状态
         		-g:只显示group的状态
    		-p:只显示project的状态
     
限额的命令格式:
xfs_quota -x -c "limit [-ug] b[soft|hard]=N i[soft|hard]=N name" 
xfs_quota -x -c "timer [-ug] [-bir] Ndays"
选项与参数：
-limit 指定限定的项目，可以针对user和group限制：
    bsoft/bhard block的soft/hard的限制值，可以加单位
    isoft/ihard inode(文件数量)的hard与soft的限制值
```

### **1）：列出目前系统的各个文件系统，以及文件系统的磁盘配额挂载参数支持**

```javascript
[root@server1 myquota]# xfs_quota -x -c "print"
Filesystem          Pathname
/                   /dev/mapper/centos-root
/disk2              /dev/sdb2 (uquota, gquota)
/disk5              /dev/sdb5 (uquota, gquota)
/boot               /dev/sda1
```

### **2）列出目前/disk5/myquota支持磁盘配额的挂载点文件系统使用情况**

这个命令其实系统自带的df差不多，但是这个会更准确

```javascript
[root@server1 myquota]# xfs_quota -x -c "df -h" /disk5
Filesystem     Size   Used  Avail Use% Pathname
/dev/sdb5      5.0G  32.2M   5.0G   1% /disk5
```

### **3）列出目前/disk5的所有用户的磁盘配额限制值**

注意这个磁盘下需要有所属主为其他用户的文件，否则除了root以外，这个命令其他用户不会显示。

```javascript
[root@server1 myquota]# xfs_quota -x -c "report -ubih" /disk5
User quota on /disk5 (/dev/sdb5)
                        Blocks                            Inodes              
User ID      Used   Soft   Hard Warn/Grace     Used   Soft   Hard Warn/Grace  
---------- --------------------------------- --------------------------------- 
root           4K      0      0  00 [------]      4      0      0  00 [------]
myquota1        0      0      0  00 [------]      2      0      0  00 [------]
myquota2        0      0      0  00 [------]      1      0      0  00 [------]
myquota3        0      0      0  00 [------]      1      0      0  00 [------]
myquota4        0      0      0  00 [------]      1      0      0  00 [------]
myquota5        0      0      0  00 [------]      2      0      0  00 [------]

#如果soft/hard为0，代表没有限制
```

### **4）列出目前支持的磁盘配额文件系统是否启动了磁盘配额功能？**

```javascript
[root@server1 myquota]# xfs_quota -x -c "state"
User quota state on /disk5 (/dev/sdb5)
  Accounting: ON	#有启用计算功能
  Enforcement: ON	#有实际磁盘配额功能
  Inode: #68 (2 blocks, 2 extents)			#这四行说明有启动user的限制能力	
Group quota state on /disk5 (/dev/sdb5)
  Accounting: ON
  Enforcement: ON
  Inode: #69 (2 blocks, 2 extents)			#这四行说明有启动group的限制能力	
Project quota state on /disk5 (/dev/sdb5)
  Accounting: OFF
  Enforcement: OFF
  Inode: #69 (2 blocks, 2 extents)			#这四行说明project并未支持
Blocks grace time: [7 days]		#宽限时间
Inodes grace time: [7 days]
Realtime Blocks grace time: [7 days]
```

## **5、限制值设置方式：**

```javascript
限额的命令格式:
xfs_quota -x -c "limit [-ug] b[soft|hard]=N i[soft|hard]=N name" 
xfs_quota -x -c "timer [-ug] [-bir] Ndays"
选项与参数：
-limit 指定限定的项目，可以针对user和group限制：
    bsoft/bhard block的soft/hard的限制值，可以加单位（就是容量大小）
    isoft/ihard inode(文件数量)的hard与soft的限制值
    name:就是用户/用户组的名称
```

### **1）设置user的block限制值：**

```javascript
[root@server1 myquota]# xfs_quota -x -c "limit -u bsoft=250M bhard=300M myquota1" /disk5
[root@server1 myquota]# xfs_quota -x -c "limit -u bsoft=250M bhard=300M myquota2" /disk5
[root@server1 myquota]# xfs_quota -x -c "limit -u bsoft=250M bhard=300M myquota3" /disk5
[root@server1 myquota]# xfs_quota -x -c "limit -u bsoft=250M bhard=300M myquota4" /disk5
[root@server1 myquota]# xfs_quota -x -c "limit -u bsoft=250M bhard=300M myquota5" /disk5
[root@server1 myquota]# xfs_quota -x -c "report -uh" /disk5	#查看报告
User quota on /disk5 (/dev/sdb5)
                        Blocks              
User ID      Used   Soft   Hard Warn/Grace   
---------- --------------------------------- 
root           4K      0      0  00 [------]
myquota1        0   250M   300M  00 [------]
myquota2        0   250M   300M  00 [------]
myquota3        0   250M   300M  00 [------]
myquota4        0   250M   300M  00 [------]
myquota5        0   250M   300M  00 [------]
```

### **2）设置group的block的限制值：**

```javascript
root@server1 myquota]# xfs_quota -x -c "limit -g bsoft=950M bhard=1G myquotagrp" /disk5
[root@server1 myquota]# xfs_quota -x -c "report -gh" /disk5
Group quota on /disk5 (/dev/sdb5)
                        Blocks              
Group ID     Used   Soft   Hard Warn/Grace   
---------- --------------------------------- 
root            0      0      0  00 [------]
myquotagrp     4K   950M     1G  00 [------]
```

### **3）设置grace time为14天：**

** -c "time -ug" 如果报错，把u和g分开写，可能就OK了 **

```javascript
[root@server1 myquota]# xfs_quota -x -c "timer -u -b 14days" /disk5
[root@server1 myquota]# xfs_quota -x -c "timer -g -b 14days" /disk5
[root@server1 myquota]# xfs_quota -x -c "state" /disk5	#查询状态
User quota state on /disk5 (/dev/sdb5)
  Accounting: ON
  Enforcement: ON
  Inode: #68 (2 blocks, 2 extents)
Group quota state on /disk5 (/dev/sdb5)
  Accounting: ON
  Enforcement: ON
  Inode: #69 (2 blocks, 2 extents)
Project quota state on /disk5 (/dev/sdb5)
  Accounting: OFF
  Enforcement: OFF
  Inode: #69 (2 blocks, 2 extents)
```

### **4）测试是否可以限制，用**** dd if=/dev/zero of=/disk/testfile bs=1M count=400 命令**

每次复制1MB，复制400次

**注意切换普通用户：**

```javascript
[myquota1@server1 myquota]$ dd if=/dev/zero of=/disk5/myquota/testfile  bs=1MB count=500
dd: 写入"/disk5/myquota/testfile" 出错: 超出磁盘限额	#超出限额
记录了315+0 的读入
记录了314+0 的写出
314048512字节(314 MB)已复制，0.610516 秒，514 MB/秒
```

再次查看报告，myquota1用户已经配额已经写满。

```javascript
[root@server1 myquota]# xfs_quota -x -c "report -uh" /disk5
User quota on /disk5 (/dev/sdb5)
                        Blocks              
User ID      Used   Soft   Hard Warn/Grace   
---------- --------------------------------- 
root           4K      0      0  00 [0 days]
myquota1   299.5M   250M   300M  00 [14 days]
myquota2        0   250M   300M  00 [------]
myquota3        0   250M   300M  00 [------]
myquota4        0   250M   300M  00 [------]
myquota5        0   250M   300M  00 [------]
```

## **6、project的限制（针对目录的限制）**

**注意：针对目录和用户组的限制不能同时存在，所以要先取消掉grpquota,**

**然后加上prjquota。**

```javascript
[root@server1 ~]# umount /disk5
[root@server1 ~]# vim /etc/fstab 
[root@server1 ~]# 
[root@server1 ~]# vim /etc/fstab 
UUID=ffd14010-cc0b-4258-8b86-9dc193feebe1 /disk5 xfs    defaults,usrquota,prjquota              0 0 
#修改为prjquota

[root@server1 ~]# mount -a
[root@server1 ~]# mount | grep disk5
/dev/sdb5 on /disk5 type xfs (rw,relatime,attr2,inode64,usrquota,prjquota)
**#可以看到功能已经开启**
```

### **1）查看state状态**

```javascript
[root@server1 ~]# xfs_quota -x -c "state -gp" /disk5
Group quota state on /disk5 (/dev/sdb5)
  Accounting: OFF
  Enforcement: OFF
  Inode: #69 (2 blocks, 2 extents)			#grpquota已经关闭
Project quota state on /disk5 (/dev/sdb5)
  Accounting: ON
  Enforcement: ON
  Inode: #69 (2 blocks, 2 extents)			#prjquota已经开启
Blocks grace time: [14 days]
Inodes grace time: [7 days]
Realtime Blocks grace time: [7 days]
```

### **2）规范目录、选项名称（project)和选项ID**

需要为目录设定一个选项名称（就是给目录起一个别名），然后还要给目录一个ID号（自拟）；

1、然后将ID号和目录路径以“ID：路径”的格式写在一个/etc/projects文件中（projects这个文件需要自己创建）；

2、将别名和ID号以“别名：ID”的格式写在/etc/projid文件中，（projid这个文件需要自己创建）；

3、然后执行xfs_quota -x -c "project -s 别名"这个命令

4、查看xfs_quota -x -c "print"  /disk5 

5、查看报告xfs_quota -x  -c "report -ph" /disk5

```javascript
[root@server1 ~]# echo "11:/disk5/myquota" >> /etc/projects
[root@server1 ~]# echo "myquota_quota:11" >> /etc/projid

[root@server1 ~]# xfs_quota -x -c "project -s myquota_quota"
Setting up project myquota_quota (path /disk5/myquota)...
Processed 1 (/etc/projects and cmdline) paths for project myquota_quota with recursion depth infinite (-1).
Setting up project myquota_quota (path /disk5/myquota)...
Processed 1 (/etc/projects and cmdline) paths for project myquota_quota with recursion depth infinite (-1).
Setting up project myquota_quota (path /disk5/myquota)...
Processed 1 (/etc/projects and cmdline) paths for project myquota_quota with recursion depth infinite (-1).
Setting up project myquota_quota (path /disk5/myquota)...
Processed 1 (/etc/projects and cmdline) paths for project myquota_quota with recursion depth infinite (-1).

[root@server1 ~]# xfs_quota -x -c "print" /disk5
Filesystem          Pathname
/disk5              /dev/sdb5 (uquota, pquota)
/disk5/myquota      /dev/sdb5 (project 11, myquota_quota)

[root@server1 ~]# xfs_quota -x -c "reprot -ph" /disk5
command "reprot" not found
[root@server1 ~]# xfs_quota -x -c "report -ph" /disk5
Project quota on /disk5 (/dev/sdb5)
                        Blocks              
Project ID   Used    Soft   Hard Warn/Grace   
---------- --------------------------------- 
#0              0      0      0  00 [------]
myquota_quota 1021.1M  0      0  00 [------]
```

### **3)实际设置规范和测试**

```javascript
[root@server1 ~]# xfs_quota -x -c "limit -p bsoft=1100M bhard=1200M myquota_quota" /disk5
[root@server1 ~]# xfs_quota -x -c "report -ph" /disk5
Project quota on /disk5 (/dev/sdb5)
                        Blocks              
Project ID   Used   Soft   Hard Warn/Grace   
---------- --------------------------------- 
#0              0      0      0  00 [------]
myquota_quota 1021.1M   1.1G   1.2G  00 [------]
[root@server1 ~]# dd if=/dev/zero of=/disk5/myquota/123.img  bs=1M count=200
dd: 写入"/disk5/myquota/123.img" 出错: 设备上没有空间
记录了179+0 的读入
记录了178+0 的写出
186646528字节(187 MB)已复制，0.118344 秒，1.6 GB/秒
[root@server1 ~]# xfs_quota -x -c "report -ph" /disk5
Project quota on /disk5 (/dev/sdb5)
                        Blocks              
Project ID   Used   Soft   Hard Warn/Grace   
---------- --------------------------------- 
#0           100M      0      0  00 [------]
myquota_quota   1.2G   1.1G   1.2G  00 [14 days]
```

## **7、xfs磁盘配额的管理**

```javascript
disable:	暂时取消磁盘配额的限制，但其实系统还是在计算磁盘配额中，知识没有管制而已，是比较常用的功能
enable：	恢复磁盘配额管制的状态，与disable可以相互取消，启用
off:	完全关闭磁盘配额的限制，使用了这个状态后，只有从新挂载才能够启动磁盘配额。建议少量使用。
remove:	必须在off状态下才能够执行，remove可以删除磁盘配额的限制设置。
    	例如取消project的设置，只要remove -p即可。
```

### **1）暂时关闭系统的磁盘配额功能：**

```javascript
[root@server1 ~]# xfs_quota -x -c "disable -up" /disk1
[root@server1 ~]# xfs_quota -x -c "state" /disk1
User quota state on /disk1 (/dev/sdb1)
  Accounting: ON			#系统依然在计算磁盘配额
  Enforcement: OFF			#但是没有管制
  Inode: #67 (2 blocks, 2 extents)
Group quota state on /disk1 (/dev/sdb1)
  Accounting: OFF
  Enforcement: OFF
  Inode: #68 (2 blocks, 2 extents)
Project quota state on /disk1 (/dev/sdb1)
  Accounting: ON
  Enforcement: OFF
  Inode: #68 (2 blocks, 2 extents)
Blocks grace time: [4 days]
Inodes grace time: [4 days]
Realtime Blocks grace time: [7 days]
[root@server1 ~]# xfs_quota -x -c "report -h" /disk1
User quota on /disk1 (/dev/sdb1)
                        Blocks              
User ID      Used   Soft   Hard Warn/Grace   
---------- --------------------------------- 
root            0      0      0  00 [------]
user1       99.2M    20M   100M  00 [------]	#可以看到宽限时间已经消失
user2       60.1M    20M   100M  00 [------]

Project quota on /disk1 (/dev/sdb1)
                        Blocks              
Project ID   Used   Soft   Hard Warn/Grace   
---------- --------------------------------- 
#0              0      0      0  00 [------]
shareDir_quota 159.3M   120M   160M  00 [------]
```

### **2）恢复磁盘配额管制：**

```javascript
[root@server1 ~]# xfs_quota -x -c "enable -up" /disk1
[root@server1 ~]# xfs_quota -x -c "state" /disk1
User quota state on /disk1 (/dev/sdb1)
  Accounting: ON
  Enforcement: ON
  Inode: #67 (2 blocks, 2 extents)
Group quota state on /disk1 (/dev/sdb1)
  Accounting: OFF
  Enforcement: OFF
  Inode: #68 (2 blocks, 2 extents)
Project quota state on /disk1 (/dev/sdb1)
  Accounting: ON
  Enforcement: ON
  Inode: #68 (2 blocks, 2 extents)
Blocks grace time: [4 days]
Inodes grace time: [4 days]
Realtime Blocks grace time: [7 days]
[root@server1 ~]# xfs_quota -x -c "report -h" /disk1
User quota on /disk1 (/dev/sdb1)
                        Blocks              
User ID      Used   Soft   Hard Warn/Grace   
---------- --------------------------------- 
root            0      0      0  00 [------]
user1       99.2M    20M   100M  00 [3 days]	#宽限时间出现      
user2       60.1M    20M   100M  00 [3 days]

Project quota on /disk1 (/dev/sdb1)
                        Blocks              
Project ID   Used   Soft   Hard Warn/Grace   
---------- --------------------------------- 
#0              0      0      0  00 [------]
shareDir_quota 159.3M   120M   160M  00 [3 days]
```

### **3）完全关闭磁盘配的限制功能：**

```javascript
[root@server1 ~]# xfs_quota -x -c "off -up" /disk1		#关闭磁盘配额功能
[root@server1 ~]# xfs_quota -x -c "state" /disk1		#无反应
[root@server1 ~]# xfs_quota -x -c "report" /disk1		#无反应
```

### **4）删除project:**

```javascript
[root@server1 ~]# xfs_quota -x -c "off -up" /disk1
[root@server1 ~]# xfs_quota -x -c "remove -p" /disk1
[root@server1 ~]# !u
umount /disk1
[root@server1 ~]# mount -a
[root@server1 ~]# mount | grep disk1
/dev/sdb1 on /disk1 type xfs (rw,relatime,seclabel,attr2,inode64,usrquota,prjquota)
[root@server1 ~]# 
[root@server1 ~]# 
[root@server1 ~]# xfs_quota -x -c "report -p" /disk1
Project quota on /disk1 (/dev/sdb1)
                               Blocks                     
Project ID       Used       Soft       Hard    Warn/Grace     
---------- -------------------------------------------------- 
#0                  0          0          0     00 [--------]
shareDir_quota     163136          0          0     00 [--------]
```

**可以给共享目录中的目录或者文件，做一个软链接，链接到用户的家目录当中，这个用户在自己的家目录就可以写入文件，而且同时也会受到磁盘配额的限制。**

# **6、磁盘配额查询
**

 quota 查询用户或用户组配额：


```javascript
[root@localhost ~]# quota [选项] [用户名或组名]
选项：
-u 用户名： 查询用户配额
-g 组名： 查询组配额
-v： 显示详细信息
-s： 以习惯单位显示容量大小，如 M，G
[root@localhost ~]# quota -uvs user1
 repquota 查询文件系统配额
[root@localhost ~]# repquota [选项] [分区名]
选项：
-a： 依据/etc/mtab 文件查询配额。如果不加-a 选项，就一定要加分区名
-u： 查询用户配额
-g： 查询组配额
-v： 显示详细信息
-s： 以习惯单位显示容量大小
[root@localhost ~]# repquota –augvs
```

# **7、测试
**

```javascript
[user1@localhost disk]$ dd if=/dev/zero of=/disk/testfile bs=1M count=60
#建立 testfile 文件，指定大小 60MB
```

# **8、非交互设定用户磁盘配额
**

```javascript
[root@localhost ~]# setquota -u 用户名 容量软限制 容量硬限制 个数软限制 \
个数硬限制 分区名
[root@localhost ~]# useradd user4
[root@localhost ~]# passwd user4
#建立用户
[root@localhost ~]# setquota -u user4 10000 20000 5 8 /disk
#设定用户在/disk 分区的容量软限制为 10MB，硬限制 20MB。文件个数软限制 5 个，硬限制#8 个 。
```




这个命令在写脚本批量设置时更加方便。当然写脚本时也可以先建立一个模板的用户，设定好磁


盘配额，再进行配额复制，也是可以的。