除了个别的软件之外，许多大型项目的软件群会集合成为一个'软件群组'。 举例来说，开发者工具经常需要编译器、环境检查确认模块等等， 这些工具则可以整合成为一个软件群组。

```javascript
[root@server1 ~]# yum [群组功能] [软件群组名]
    grouplist:列出所有可使用的[软件群组组],如果显示的是中文，可以使用这个LANG=C yum grouplist，则显示的是中文。
    groupinfo [group name]:可了解group name中都有哪些软件
    groupremove [group name]:可卸载某个软件群组
```

# 1、yum grouplist  查询可以安装的软件组

```javascript

[root@server1 ~]# LANG=C yum grouplist
Loaded plugins: fastestmirror
There is no installed groups file.
Maybe run: yum groups mark convert (see man yum)
Loading mirror speeds from cached hostfile
 * base: mirrors.cn99.com
 * extras: mirrors.cn99.com
 * updates: mirrors.cn99.com
 Installed environment groups:	  	#已经安装的系统环境软件群组
 .........
Available Environment Groups:	  	#还可以安装的系统环境软件群组
   Minimal Install
   Compute Node
   Infrastructure Server
   File and Print Server
   Basic Web Server
   Virtualization Host
   Server with GUI
   GNOME Desktop
   KDE Plasma Workspaces
   Development and Creative Workstation
Installed groups:					#已经安装的软件群组
。。。。。。。       
Available Groups:					#还可以安装的软件群组
   Compatibility Libraries
   Console Internet Tools
   Development Tools
   Graphical Administration Tools
   Legacy UNIX Compatibility
   Scientific Support
   Security Tools
   Smart Card Support
   System Administration Tools
   System Management
Done
[root@server1 ~]# 

```

# 2、查询软件组内部包含的软件

```javascript
[root@localhost ~]# yum groupinfo 软件组名
#列出软件组中包含的软件
例如：
[root@localhost ~]# yum groupinfo "Web Server"
#查询软件组"Web Server"中包含的软件
```

# 3、安装软件组

```javascript
[root@localhost ~]# yum groupinstall 软件组名
#安装指定软件组，组名可以由 grouplist 查询出来
例如：
[root@localhost ~]# yum groupinstall "Web Server"
#安装网页服务软件组
```

4、卸载软件组

```javascript
[root@localhost ~]# yum groupremove 软件组名
#卸载指定软件组
```

