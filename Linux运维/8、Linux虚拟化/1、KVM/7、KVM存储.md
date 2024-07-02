**KVM存储**

- kvm必须配置一个目录当做存储磁盘镜像(存储卷)的目录，这个目录为存储池

- 默认存储池： /var/lib/libvirt/images/

# 1、存储池创建使用相关命令

KVM平台以存储池的形式对存储进行统一管理，所谓存储池可以理解为本地目录、通过远端磁盘阵列

（iSCSI、NFS）分配过来磁盘或目录。

- **创建基于文件夹的存储池(目录)，并且定义存储池与其目录**

```
[root@localhost ~]# mkdir -p /data/vmfs
[root@localhost ~]# virsh pool-define-as vmdisk --type dir --target  /data/vmfs
定义池 vmdisk·
```

- **创建已定义的存储池，然后查看已定义的存储池，存储池不激活无法使用**

```
# 构建池 vmdisk
[root@localhost ~]# virsh pool-build vmdisk
# 查看池
[root@localhost ~]# virsh pool-list --all
名称        状态   自动开始
-------------------------------------------
default       活动   是   
vmdisk        不活跃 否   
# 启用存储池
[root@localhost ~]# virsh pool-start vmdisk
池 vmdisk 已启动
# 设置开机自动启动
[root@localhost ~]# virsh pool-autostart vmdisk
池 vmdisk 标记为自动启动
[root@localhost ~]# virsh pool-list --all
名称        状态   自动开始
-------------------------------------------
default       活动   是   
vmdisk        活动   是
```

- **在存储池中创建虚拟机存储卷**

```
root@localhost ~]# virsh vol-create-as vmdisk vm1-2.qcow2 10G --format qcow2
创建卷 vm1-2.qcow2
```

**注1：**

池，虚拟机存储卷创建完成后，剩下的操作与无存储卷的方式无任何区别

**注2：**

# 2、存储池删除关管理命令

- 在相存储池中删除虚拟机存储卷

```
[root@localhost ~]# virsh vol-delete --pool vmdisk vm1-2.qcow2 
```

- 取消激活存储池

```
[root@localhost ~]# virsh pool-destroy vmdisk 
```

- 删除存储池定义的目录

```
[root@localhost ~]# virsh pool-delete vmdisk
```

- 取消定义存储池

```
[root@localhost ~]# virsh pool-undefine vmdisk 
```

# 3、磁盘格式

- raw

- 原始格式，性能最好 直接占用你一开始给多少 系统就占多少 不支持快照

- qcow

- 性能远不能和raw相比，所以很快夭折了，所以出现了qcow2。（性能低下 早就被抛弃）

- qcow2

- 性能上还是不如raw，但是raw不支持快照，qcow2支持快照。

## 1）写时拷贝(copy on write)

raw立刻分配空间，不管你有没有用到那么多空间

qcow2只是承诺给你分配空间，但是只有当你需要用空间的时候，才会给你空间。最多只给你承诺空间

的大小，避免空间浪费

# 2)不同格式磁盘大小比较

可以看到qcow2的文件大小初始很小

```
[root@localhost ~]# qemu-img create -f qcow2 test1.qcow2 1G
Formatting 'test1.qcow2', fmt=qcow2 size=1073741824 encryption=off
cluster_size=65536 lazy_refcounts=off
[root@localhost ~]# qemu-img create -f raw test2.raw 1G
Formatting 'test2.qcow2', fmt=raw size=1073741824
[root@localhost ~]# ls -lh test1.qcow2 test2.raw
-rw-r--r-- 1 root root 193K 10月  2 14:39 test1.qcow2
-rw-r--r-- 1 root root 1.0G 10月  2 14:39 test2.raw
```

# 4、虚拟机磁盘挂载至宿主机

- 当虚拟机无法启动的时候，可以将虚拟磁盘文件直接挂载到主机上，以获得其中的文件

- 查看磁盘镜像分区信息

```
[root@localhost ~]# virt-df -h -d vm1        #
文件系统              大小 已用空间 可用空间 使用百分比%
vm1:/dev/sda1              1014M    100M    914M  10%
vm1:/dev/centos/root            8.0G    971M    7.0G  12%
[root@localhost ~]# virt-filesystems -d vm1
/dev/sda1
/dev/centos/root
```

- 挂载磁盘镜像分区，挂载完成之后可以看到vm1虚拟机根目录下的文件

```
[root@localhost ~]# guestmount -d vm1 -m /dev/centos/root --rw /root/vm1
[root@localhost ~]# ls vm1/
bin  dev home lib64 mnt proc run  srv tmp var
boot etc lib  media opt root sbin sys usr
```

- 取消挂载

```
[root@localhost ~]# guestunmount /root/vm1 
```