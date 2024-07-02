# **实时同步rsync（结合INOTIFY）**

- 第一步：早Code服务器端，安装inotify-tools工具（监控器）

```
[root@server1 ~]# yum install -y inotify-tools
[root@server1 ~]# rpm -ql inotify-tools 
/usr/bin/inotifywait             #等待
/usr/bin/inotifywatch            #看守
```

- 第二步，编写inotify脚本：inotify脚本

```
#!/bin/bash
/usr/bin/inotifywait -mrq -e modify,delete,create,attrib,move /root/dir1 |while read events
do
    rsync -a --delete /dir1 /dir2/
    echo"`date+%F\%T`出现事件$events">>/var/log/rsync.log 2>&1
done
```

- 第三步：添加可执行权限

```
chmod +x inotify.sh
```

- 第四步：让inotify.sh一直执行下去

```
# nohup ./inotify.sh    &        #让这个脚本一直在后台运行。即使关闭终端，也会继续运行。
```

### **inotifywait命令参数**

```
-m    是要持续监视变化。
-r    使用递归形式监视目录。
-q    减少冗余信息，只打印出需要的信息。
-e    指定要监视的事件列表。
--timefmt是    指定时间的输出格式。
--format    指定文件变化的详细信息。
```

| 事件 | 描述 | 
| -- | -- |
| access | 访问 | 
| modify | 修改 | 
| attrib | 属性 | 
| move | 移动 | 
| create | 创建 | 
| open | 打开 | 
| close | 关闭 | 
| delete | 删除 | 
