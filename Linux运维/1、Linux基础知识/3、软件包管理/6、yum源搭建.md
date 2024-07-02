# **1、rpm包在线安装（yum安装）**

## **1）yum源文件解析；**

yum源配置文件保存在/etc/yum.repos.d/目录中，文件的扩展名是“*.repos”。也就是说，yum源配置文件只有是“.repos”才会生效；

```javascript
[root@localhost ~]# ls /etc/yum.repos.d/
CentOS-Base.repo   CentOS-Debuginfo.repo   CentOS-fasttrack.repo   CentOS-Media.repo   CentOS-Vault.repo
```

这个目录中有 5 个 yum 源配置文件，默认情况下 CentOS-Base.repo 文件生效。我们打开这个文


件看看，命令如下：


```javascript
[root@localhost yum.repos.d]# vim /etc/yum.repos.d/CentOS-Base.repo
[base]
name=CentOS-$releasever - Base
mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&
repo=os
baseurl=http://mirror.centos.org/centos/$releasever/os/$basearch/
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6
```




…省略部分输出…


在 CentOS-Base.repo 文件中有 5 个 yum 源容器，这里只列出了 base 容器，其他容器和 base 容


器类似。我们解释一下 base 这个容器。


```javascript
 [base]：容器名称，一定要放在[]中。

 name：容器说明，可以自己随便写。

 mirrorlist：镜像站点，这个可以注释掉。

 baseurl：我们的 yum 源服务器的地址。默认是 CentOS 官方的 yum 源服务器，是可以使用的。

    	如果你觉得慢，则可以改成你喜欢的 yum 源地址。

 enabled：此容器是否生效，如果不写或写成 enabled=1 则表示此容器生效，写成 enabled=0则表示此容器不生效。

 gpgcheck：如果为 1 则表示 RPM 的数字证书生效；如果为 0 则表示 RPM 的数字证书不生效。

 gpgkey：数字证书的公钥文件保存位置。不用修改。
```

# **2、搭建本地光盘yum源**

第一步：放入Centos安装光盘，并挂载到指定位置。

```javascript
[root@localhost ~]# mkdir /mnt/cdrom
#创建 cdrom 目录，作为光盘的挂载点
[root@localhost ~]# mount /dev/sr0 /mnt/cdrom/
mount: block device /dev/sr0 is write-protected, mounting read-only
#挂载光盘到/mnt/cdrom 目录下
```

第二步：修改其他几个 yum 源配置文件的扩展名，让它们失效，因为只有扩展名是“*.repo”的


文件才能作为 yum 源配置文件。当然也可以删除其他几个 yum 源配置文件，但是如果删除了，当你


又想用网络作为 yum 源时，就没有了参考文件，所以最好还是修改扩展名。命令如下：


```javascript
[root@localhost ~]# cd /etc/yum.repos.d/
[root@localhost yum.repos.d]# mv CentOS-Base.repo CentOS-Base.repo.bak
[root@localhost yum.repos.d]# mv CentOS-Debuginfo.repo CentOS-Debuginfo.repo.bak
[root@localhost yum.repos.d]# mv CentOS-Vault.repo CentOS-Vault.repo.bak
```

第三步：修改光盘 yum 源配置文件 CentOS-Media.repo，参照以下方法修改：


```javascript
[root@localhost yum.repos.d]# vim CentOS-Media.repo
[c6-media]
name=CentOS-$releasever - Media
baseurl=file:///mnt/cdrom
#地址为你自己的光盘挂载地址
# file:///media/cdrom/
# file:///media/cdrecorder/
#注释这两个不存在的地址
gpgcheck=1
enabled=1
#把 enabled=0 改为 enabled=1，让这个 yum 源配置文件生效
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6
```

# **3、yum命令**

yum 是前台使用的软件，其实后端 Linux 还是使用 rpm 来进行软件管理的任务。

```javascript
[root@server1 ~]# yum [options] [查询工作选项] [相关参数]
选项与参数：
[option]:
    -y:自动回应yes;
    --installroot=/some/path:将软件安装在/some/path这个目录中，而不是默认的目录
[查询工作选项]:(备注，以下选项不需要加“-”)
    search:查找某个软件名或者某个重要的关键字
    list:后面可以跟一个软件名，不跟就是列出目前yum所管理的所有的软件与版本，有点类似 rpm -qa
    info:同上，列出某个软件的信息，有点类似 rpm -qi
    provides:后面跟命令名，列出会提供此命令的软件。
[安装/更新/删除]:
    -install:安装某个软件
    -update:更新某个软件
    -remove [软件名]:卸载某个软件，但是这个命令，具有较大的风险，因为卸载时，也会
    			卸载掉某些关键的依赖，导致系统出错，所以尽量不要使用。
[其他]:
    repolist:列出目前yum正在使用的软件源；
    repolist all:列出所有的yum的软件源（只有少部分被启用了）
    clean all:清空缓存及其他文件
    makecache:重建缓存
    check-update:查看哪些软件可以升级
```

## **1）查询功能：yum [list|info|search|provides|whatprovides] 参数**

如果想要查询利用 yum 来查询原版 distribution 所提供的软件，或已知某软件的名称，想知道该软件的功能，直接使用 yum 搭配参数即可。

### ** yum list,查询 yum 源服务器上所有可安装的软件包列表。
**

```javascript
[root@localhost yum.repos.d]# yum list
#查询所有可用软件包列表
Installed Packages
#已经安装的软件包
ConsoleKit.i686 0.4.1-3.el6 @anaconda-CentOS-201207051201.i386/6.3
ConsoleKit-libs.i686 0.4.1-3.el6 @anaconda-CentOS-201207051201.i386/6.3
...省略部分输出...
Available Packages
#还可以安装的软件包
389-ds-base.i686 1.2.10.2-15.el6 c6-media
389-ds-base-devel.i686 1.2.10.2-15.el6 c6-media
#软件名 版本 所在位置（光盘）
```

...省略部分输出...


### ** yum list [包名]，查询 yum 源服务器中是否包含某个软件包。
**

```javascript
[root@localhost yum.repos.d]# yum list 包名
#查询单个软件包
例如：
[root@localhost yum.repos.d]# yum list samba
Available Packages
samba.i686 3.5.10-125.el6 c6-media
```

### ** yum search [关键字]，搜索 yum 源服务器上所有和关键字相关的软件包。
**

```javascript
[root@localhost yum.repos.d]# yum search 关键字
#搜索服务器上所有和关键字相关的软件包
例如：
[root@localhost ~]# yum search ifconfig
已加载插件：fastestmirror, langpacks
Loading mirror speeds from cached hostfile
=========================================================== 匹 配 ： ifconfig
===========================================================
net- - tools.x86_64 : Basic networking tools
yum search 搜索可以用于确定某个软件在哪个相关包当中。此例子可以确定“ifconfig”命令
需要安装“net-tools”包。
```

### ** yum info [软件包],查询指定软件包的信息。
**

```javascript
[root@localhost yum.repos.d]# yum info samba
#查询 samba 软件包的信息
Available Packages 还没有安装
Name : samba 包名
Arch : i686 适合的硬件平台
Version : 3.5.10 版本
Release : 125.el6 发布版本
Size : 4.9 M 大小
Repo : c6-media 在光盘上
…省略部分输出…
```

### **yum provides [命令名]，列出提供这个命令的软件有哪些：**

```javascript
[root@server1 ~]# yum provides passwd
已加载插件：fastestmirror
Repodata is over 2 weeks old. Install yum-cron? Or run: yum makecache fast
Loading mirror speeds from cached hostfile
 * base: mirrors.cn99.com
 * extras: mirrors.cn99.com
 * updates: mirrors.cn99.com
passwd-0.79-6.el7.x86_64 : An utility for setting or changing passwords
                         : using PAM
源    ：base



passwd-0.79-4.el7.x86_64 : An utility for setting or changing passwords
                         : using PAM
源    ：@anaconda



```

### **例题：**

```javascript
那一个软件提供了 ifconfig 这个指令	# yum provides ifconfig
显示并查阅该软件的描述 （Description） 尝试了解该软件的任务 # yum info net-tools.x86_64
列出所有以qemu为开头的软件名称	# yum list qemu*
有个名为qemu-kvm的软件功能为何？	# yum info qemu-kvm
```

## **2）安装（install)
**

```javascript
[root@localhost yum.repos.d]# yum -y install 包名
选项：
install 安装
-y 自动回答 yes。如果不加-y，那么每个安装的软件都需要手工回答 yes
例如：
[root@localhost yum.repos.d]# yum - - y install gcc
#使用 yum 自动安装 gcc
```

## **3）升级(update)
**

```javascript
[root@localhost yum.repos.d]# yum -y update 包名
#升级指定的软件包
选项：
update： 升级
-y： 自动回答 yes
注 意：在进行升级操作时，yum 源服务器中软件包的版本要比本机安装的软件包的版本高。
[root@localhost yum.repos.d]# yum -y update
#升级本机所有软件包
```

这条命令会升级系统中所有的软件包。不过我们的生产服务器是稳定优先的，所以这种全系统升


级的情况并不多见。


### **例题：**

```javascript
例题：基本的查询与安装任务
    1、用 rpm 本机查询有没有安装 pam-devel 这个软件？	# rpm -q pam-devel
    2、用 yum 查询是否有这个 pam-devel 的软件名称？	# yum list pam-devel
    3、用 yum 在线安装这个 pam-devel 的软件。	# yum install -y  pam-devel
    4、安装完毕后，透过 rpm 查询 pam-devel 的所属文件名有哪些？ # rpm -ql pam-devel 
例题：基本的升级任务
    1、先使用 yum check-update 尝试分析目前服务器上有比本机 Linux 还要新的软件群
    2、随意选择一个软件（例如sudo）来进行单一软件的升级
    3、进行一次全系统升级
    4、如果需要每天凌晨3点自动进行全系统升级，该如何处理？ 同时须注意到 yum 是否需要加上特别的参数？
例题：基本的移除任务
    1、将刚刚安装的 pam-devel 删除掉 （请自行 man yum 找出删除的选项）
    2、刚刚已经全系统安装完毕，请问是否需要重新开机？ 为什么？
```

## **4）卸载
**

**再次强调一下，除非你确定卸载的软件的依赖包不会对系统产生影响，否则不要执行 yum 的卸载，
**

**因为很有可能在卸载软件包的同时卸载的依赖包也是重要的系统文件，这就有**

载命令如下：


```javascript
[root@localhost yum.repos.d]# yum remove 包名
#卸载指定的软件包
```

例如：


```javascript
[root@localhost yum.repos.d]# yum remove samba
#卸载 samba 软件包
```

# **4、yum 配置文件**

yum 是通过配置文件的规范去找到安装/升级服务器的，因为经常有第三方的第三方推出 yum 兼容的安装服务器， 因此了解与设定 yum 配置文件，是有其必要的。

默认的CentOS 7的配置文件的文件名为： /etc/yum.repos.d/*.repo，重点在扩展名必须为 .repo 才行。 默认的 CentOS 配置文件内容如下：

```javascript
[root@localhost ~]# cat /etc/yum.repos.d/CentOS-Base.repo
[base]
name=CentOS-$releasever - Base
mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=os&infra=$infra
#baseurl=http://mirror.centos.org/centos/$releasever/os/$basearch/
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
```

相关的配置数据说明如下：

```javascript
[base]：代表软件库的名字！ 中括号一定要存在，里面的名称尽量取与软件有关的关键词。 此外，不能有两个相同的软件库名称，否则 yum 指令会误判
name：与 [] 类似，仅作为完整名称显示，通常可设定与 [] 内的文字相同即可。
mirrorlist=：使用CentOS官网记载的映射站分析，透过 yum 主动分析最靠近本地服务器来源
baseurl=：与 mirrorlist 不同，baseurl 为自行指定固定的 yum 服务器，第三方协力软件需要这个设定，管理员自己设定的 yum 服务器也通常使用 baseurl 来规范来源。
enabled=1：是否启动这个软件库，默认为启动，若只想要设定好这个软件库，但平时不想使用时，可将这个项目设定为 enabled=0
gpgcheck=1：指定是否需要查阅 RPM 文件内的数字签名！
gpgkey=：若指定需要数字签名 （gpgcheck=1），则需要在此填写数字签名的文件文件名。
```

## **范例一：yum repolist all列出目前yum所有的软件源**

```javascript
[root@server1 yum.repos.d]# yum repolist 
已加载插件：fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirrors.cn99.com
 * extras: mirrors.cn99.com
 * updates: mirrors.cn99.com
源标识                                                                     源名称                                                                        状态
base/7/x86_64                                                              CentOS-7 - Base                                                               10,072
extras/7/x86_64                                                            CentOS-7 - Extras                                                                500
updates/7/x86_64                                                           CentOS-7 - Updates                                                             3,411
repolist: 13,983
[root@server1 yum.repos.d]# yum repolist all
已加载插件：fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirrors.cn99.com
 * extras: mirrors.cn99.com
 * updates: mirrors.cn99.com
源标识                                                                   源名称  
。。。。。。                                                                  
CentOS-7.5.1804 - Extras                                                  禁用
C7.5.1804-fasttrack/x86_64                                               CentOS-7.5.1804 - Fasttrack                                               禁用
C7.5.1804-updates/x86_64                                                 CentOS-7.5.1804 - Updates                                                 禁用
base/7/x86_64                                                            CentOS-7 - Base                                                           启用: 10,072
base-debuginfo/x86_64                                                    CentOS-7 - Debuginfo                                                      禁用
base-source/7                                                            CentOS-7 - Base Sources                                                   禁用
c7-media                                                                 CentOS-7 - Media                                                          禁用
centosplus/7/x86_64                              
```

## **例题1、：**

```javascript
1、由于 mirrorlist 需要一段时间去测试最快的服务器，并且偶而会测试错误。 因此请自行手动找到最近的服务器， 将 mirrorlist 
    修改成 baseurl 的方式来设定好 [base]， [updates]， [extras] 三个软件库的内容。
2、修改完成后，由于修改过 yum 配置文件，为了担心清单缓存会有重复或者是其他问题，请 yum clean all 
    清除所有系统快取的信息。
3、再次的 yum update 测试一下是否正确的下载了清单信息。
```

**如果您想要列出所有的软件库，可使用 yum repolist all 的选项来处理：**

## **例题2、：Red Hat提供了 EPEL 的计划，由许多志愿者提供了很多针对 RHEL/CentOS 打包好的软件，提供给用户使用。 但这些软件并非官网提供， 因此其软件库并没有在默认的配置文件内，请依据下面的方式来处理 EPEL 的软件支持：**

```javascript
1、先上网查询 EPEL，分析到如下网页：https://dl.fedoraproject.org/pub/epel/7/x86_64/
2、在 /etc/yum.repos.d/ 新增一名为 epel.repo 的档案，内容填写 [epel]， name， baseurl， 
    gpgcheck = 0， enabled=0 五个项目即可
3、使用 yum repolist all 列出系统上所有的软件库，并查看 epel 是否在其中？
4、手动使用这个软件库时，指令列加上 yum --enablerepo epel 之后，填写需要的动作。 
    例如列出 （list） netcdf 这个软件
5、承上，若需要安装 netcdf 这个软件时，该如何下达指令？
```