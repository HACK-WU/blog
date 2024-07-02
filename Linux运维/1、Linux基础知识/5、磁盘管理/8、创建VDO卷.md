## **创建VDO卷**

根据如下要求，创建新的VDO卷：

1）使用未分区的磁盘（/dev/vdc）

2）此VDO卷的名称为myvdo

3）此VDO卷的逻辑大小为50G

4）此VDO卷使用xfs文件系统格式化

5）此VDO卷在系统启动时自动挂载到/vblock目录下

```
[root@serverb ~]# yum -y install vdo kmod-kvdo #安装软件包，考试需要安装 
[root@serverb ~]# vdo create --name=myvdo --device=/dev/vdc --
vdoLogicalSize=50G # 命令man vdo 找example
Creating VDO myvdo
Starting VDO myvdo
Starting compression on VDO myvdo
VDO instance 0 volume is ready at /dev/mapper/myvdo
[root@serverb ~]# mkfs.xfs /dev/mapper/myvdo
[root@serverb ~]# vim /etc/fstab
/dev/mapper/myvdo /vblock xfs defaults,x-systemd.requires=vdo.service 0 0
 #man vdo
[root@serverb ~]# mount -a
[root@serverb ~]# df -h /vblock
Filesystem       Size Used Avail Use% Mounted on
/dev/mapper/myvdo     50G 390M  50G  1% /vblock
#删除vdo卷
[root@serverb ~]# vdo stop -n myvdo
[root@serverb ~]# vdo remove -n myvdo
```