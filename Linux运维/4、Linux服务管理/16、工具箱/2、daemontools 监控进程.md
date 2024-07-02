**daemontools 监控进程**

# 一、学习的原因：

      为了实现在tomcat服务异常停止运行后，有一个监控程序能监控到它，并自动重新启动这个tomcat。

# 二、工具supervise

      Daemontools是一个包含了很多管理Unix服务的工具的软件包。其中最核心的工具是supervise，它的功能是监控一个指定的服务，当该服务进程消亡，则重新启动该进程。而要添加让supervise监控的服务非常容易，

    其中svscan工具是为指定的工作目录(缺省是/service/目录)下的所有子目录中的每一个子目录都启动一个supervise进程，最多可以启动多达1000个supervise进程(也就是工作目录下可以有多达1000个子目录)。其中每个子目录下都会有一个名为run的用来启动对应服务的脚本程序。

    Svscan

    该软件包的所有工具的详细信息请参考在线文档。daemontools最经典的搭配是和lighttpd一起使用

# 三、安装

　　/pacakage目录（你可以创建任意目录，这里使用package只是为了保持与英文作者的一致）：　　

```
　　mkdir -p /package
　　chmod 1755 /package
　　cd /package
```

 

　　下载daemontools-0.76.tar.gz到/package目录，解压该包。

```
    wget   
　　tar xvzf daemontools-0.76.tar.gz
　　cd admin/daemontools-0.76
```

　　编译并安装daemontools程序

　　

　　package/install

 【

```
修改方法：在src下的conf-cc文件的第一行最后添加如下代码即可  -include /usr/include/errno.h
     # vi src/conf-cc
     在最后加上 -include /usr/include/errno.h
     
```

# 四、使用supervise程序进行程序管理监控

假如你有一个脚本test.sh，需要持续监控它，不能停止服务。

需要这样做

```
mkdir /porcess    #随便创建一个根目录
mkdir /process/test    #在里面创建一个与脚本同名的目录
cp test.sh /process/test/run    #拷贝到那个目录下，并改名为run
supervise /process/test/run     #这样你就可以监视这个脚本了
    #即是用kill杀死，也会自启。
#当开启supervise 监控某个进程后，就可以使用一下命令控制这个进程了。
 svc -d /process/test/run   # 停止执行
 svc -u /process/test/run   #继续执行
```