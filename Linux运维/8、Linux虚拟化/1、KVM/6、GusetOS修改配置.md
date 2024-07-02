**GusetOS修改配置**

这些基本上都可以通过图形化的方式进行修改

# 1、添加磁盘

## 1）修改配置文件（vim /etc/libvirt/qemu/vm1.xml)

- 比如添加磁盘，那就添加如下配置

- 需要修改slot:插口；source file :映像文件改一下名字就行了；tartget  dev,设备名改一下,顺序是：vda，vdb,vdc

```
<disk type='file' device='disk'>
<driver name='qemu' type='qcow2'/>
<source file='/var/lib/libvirt/images/vm1-1.qcow2'/>
<target dev='vdb' bus='virtio'/>
<address type='pci' domain='0x0000' bus='0x00' slot='0x17' function='0x0'/>
</disk>
```

**特别注意:centos8里面硬盘和网卡的添加已经不能修改slot了，要求修改的是bus地址**

## 2）创建新的空磁盘卷

**(-f ，format,格式，指的是磁盘映像的格式）（qcow2是qemu支持的磁盘镜像格式）（encryption,加密）**

```
[root@localhost ~]# cd /var/lib/libvirt/images/
[root@localhost images]# qemu-img create -f qcow2 vm1-1.qcow2 10G    #到这回车
Formatting 'vm1-1.qcow2', fmt=qcow2 size=10737418240 encryption=off  
cluster_size=65536 lazy_refcounts=off
```

## 3）重新定义虚拟机

```
[root@localhost qemu]# virsh define /etc/libvirt/qemu/vm1.xml
定义域 vm1（从 vm1.xml）
```

## 4检查配置是否升级

```
[root@localhost ~]# virsh start vm1
域 vm1 已开始
[root@localhost ~]# virsh console vm1
连接到域 vm1
换码符为 ^]
CentOS Linux 7 (Core)
Kernel 3.10.0-957.el7.x86_64 on an x86_64
vm1 login: root
密码：
Last login: Fri Oct  2 13:27:39 on tty1
[root@vm1 ~]# lsblk
NAME      MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
sr0        11:0   1 1024M  0 rom 
vda       252:0   0  10G  0 disk
├─vda1      252:1   0  1G  0 part /boot
└─vda2      252:2   0  9G  0 part
├─centos-root 253:0   0  8G  0 lvm /
└─centos-swap 253:1   0  1G  0 lvm [SWAP]
vdb       252:16  0  10G  0 disk
```

# 2、CPU添加

- 热添加的意思就是可以在虚拟机不重启的情况下，完成CPU的升级

- 不支持热减少

- GuestOS只支持Linux发行套件，不支持其他系统

```
[root@localhost ~]# virsh edit vm1
<domain type='kvm'>
<name>vm1</name>
<uuid>b0c26bcd-ce6b-46ef-96e8-eb12ea52e5e0</uuid>
<memory unit='KiB'>1048576</memory>
<currentMemory unit='KiB'>1048576</currentMemory>
<vcpu placement='auto' current='1'>4</vcpu> # 最大cpu使用4个，默认是1个
[root@localhost ~]# virsh vcpucount vm1
最大值  配置     4
当前    配置     1
[root@localhost ~]# virsh start vm1
域 vm1 已开始
[root@localhost ~]# virsh dominfo vm1
Id:       2
名称：    vm1
UUID:      b0c26bcd-ce6b-46ef-96e8-eb12ea52e5e0
OS 类型：  hvm
状态：    running
CPU：      1 # 可以看到目前只有一颗CPU
CPU 时间：  16.6s
最大内存： 1048576 KiB
使用的内存： 1048576 KiB
持久：    是
自动启动： 禁用
管理的保存： 否
安全性模式： none
安全性 DOI： 0
[root@localhost ~]# virsh setvcpus vm1 2 --live
[root@localhost ~]# virsh dominfo vm1
Id:       2
名称：    vm1
UUID:      b0c26bcd-ce6b-46ef-96e8-eb12ea52e5e0
OS 类型：  hvm
状态：    running
CPU：      2 # 现在有两颗CPU
CPU 时间：  19.0s
最大内存： 1048576 KiB
使用的内存： 1048576 KiB
持久：    是
自动启动： 禁用
管理的保存： 否
安全性模式： none
安全性 DOI： 0
```

# 3、内存热添加

- 将最大可用内存调整为2048M

```
<domain type='kvm'>
<name>vm1</name>
<uuid>b0c26bcd-ce6b-46ef-96e8-eb12ea52e5e0</uuid>
<memory unit='KiB'>2097152</memory> # 最大可用内存
<currentMemory unit='KiB'>1048576</currentMemory> # 内存当前大小
<vcpu placement='auto' current='1'>4</vcpu>
```

- 重启虚拟机让其生效

```
[root@localhost ~]# virsh reboot vm1 
```

- 查看现在内存大小

```
[root@localhost ~]# virsh qemu-monitor-command vm1 --hmp --cmd info balloon
balloon: actual=1024
```

- 调整内存到2048M

```
[root@localhost ~]# virsh qemu-monitor-command vm1 --hmp --cmd balloon 2048
[root@localhost ~]# virsh qemu-monitor-command vm1 --hmp --cmd info balloon
balloon: actual=2048
```