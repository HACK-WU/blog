**Control Group(CGroup)资源限制组**

# **1、CGroup概述**

控制组（CGroups）是Linux内核的一个特性，主要用来对共享资源进行隔离、限制、审计等。只有能控制分配到容器的资源，才能避免多个容器同时运行时对宿主机系统的资源竞争。控制组可以提供对容器的内存、CPU、磁盘IO等资源进行限制和计费管理。控制组的设计目标是为不同的应用情况提供统一的接口，从控制单一进程（比如nice工具）到系统级虚拟化（包括OpenVz、Linux-VServer、LXC等）。

具体来看，控制组提供：

资源限制（Resourcelimiting）:可以将组设置为不超过设定的内存限制。比如：内存子系统可以为进程组设定一个内存使用上限，一旦进程组使用的内存达到限额再申请内存，就会出发Out of Memory警告。

- **优先级（Prioritization ) : **通过优先级让一些组优先得到更多的CPU等资源。

- **资源审计（Accounting ) :** 用来统计系统实际上把多少资源用到适合的目的上，可以使用cpuacct子系统记录某个进程组使用的CPU时间。

- **隔离（isolation）**：为组隔离命名空间，这样一个组不会看到另一个组的进程、网络连接和文件系统。

- **控制（Control）：**挂起、恢复和重启动等操作。

- cgroups: Control Groups

- 基于进程的限制，而非用户，因此对于超户运行的进程也是一样

- cgroup将各种子系统定义为资源，命名为controller:

- 可配额/可度量-Control Groups (cgroups)

- cgroups实现了对资源的配额和度量九大子系统的资源:

- **1.blkio**		限制每个块设备的输入输出控制。例如:磁盘,光盘以及usb

- **2.cpu	**	限制使用cpu比例

- **3.cpuacct	**产生cgroup任务的cpu资源报告。

- **4.cpuset	**	多核心的cpu时为cgroup任务分配单独的cpu和内存

- **5.devices **	允许或拒绝对设备的访问。

- **6.freezer **	暂停和恢复cgroup任务。

- **7.memory	**设置内存限制以及产生内存资源报告。

- **8.net_cls	**	标记每个网络包。

- **9.ns	**	        名称空间子系统

例如:对某个进程使用内存进行限制步骤：

1. 需要在controller memory下建立cgroup , 如nginx_mem控制组，并针对该控制组nginx_mem设置相应的内存限制参数

1. 将进程Nginx分配到 memory controller的控制组(nginx_mem)没有使用controller则不会限制。

Cgroup实现资源限制的方法：

a.cgexec 手动分配

b.cgred自动分配

# 2、CGroup使用

- Cgroup部署方法：

```
[root@localhost ~]# yum -y install libcgroup
[root@localhost ~]# systemctl enable cgconfig
[root@localhost ~]# systemctl start cgconfig
[root@localhost ~]# man cgconfig.conf
```

- Cgroup限制步骤：

1.创建cgroup ,定义相应的限制

2.分配程序到cgroup

## 案例1：限制进程使用CPU	

1.使用cpu子系统创建两个cgroup

```
[root@localhost ~]# vim /etc/cgconfig.conf
```

- 实验中，为了让两个进程抢CPU时间片，故意只留一个CPU在线

```
[root@localhost ~]#lscpu
Architecture:          x86_64
CPU op-mode(s):        32-bit, 64-bit
Byte Order:            Little Endian
CPU(s):                4            //4颗CPU数量
On-line CPU(s) list:   0-3
Thread(s) per core:    1
Core(s) per socket:    2
座：                 2
......
[root@localhost ~]#echo 1 > /sys/devices/system/cpu/cpu0/online    //online的值为1，表示CPU0在线
[root@localhost o]#echo 0 > /sys/devices/system/cpu/cpu1/online    //online的值为0，表示CPU1不在线
[root@localhost o]#echo 0 > /sys/devices/system/cpu/cpu2/online    //online的值为0，表示CPU2不在线
[root@localhost o]#echo 0 > /sys/devices/system/cpu/cpu3/online    //online的值为0，表示CPU3不在线
```

```shell
group lesscpu {        #少量CPU资源
    cpu{
        cpu.shares=200;    #将占用CPU200份的资源
    }
}
group morecpu {    #大量CPU资源
    cpu{
        cpu.shares=800;    #将占用CPU800的资源
    }
}
```

- 重启：

```
[root@localhost ~]# systemctl restart  cgconfig.service
```

- 手动分配

```
[root@localhost ~]# cgexec -g cpu:lesscpu sha1sum /dev/zero
[root@localhost ~]# cgexec -g cpu:morecpu md5sum /dev/zero
[root@localhost ~]# top
```

# 案例2：限制进程使用Memory

- ·添加cgroup

```
[root@localhost ~]#vim /etc/cgconfig.conf
group lessmem {
    memory {
        memory . limit_in_bytes=268435465；//物理内存限制256M
        memory .memsw limit_in_bytes=268435465；//虚拟交换分区限制256M
        }
}
[root@localhost ~]#systemct1 restart cgconfig
```

- 创建内存盘

```
[root@localhost ~]# mkdir /mnt/mem_test
[root@localhost ~]# mount -t tmpfs /dev/shm /mnt/mem test
[root@localhost ~]# cgexec -g memory:1essmem dd if=/dev/zero of=/mnt/mem test/file
bs=1M count=200    //oK
[root@localhost ~]# cgexec -g memory:1essmem dd if=/dev/zero of=/mnt/mem test/file
bs=1M count=500     
杀死
[root@localhost ~]#free -m
```

**结果成功**