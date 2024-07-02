**对NFS服务器进行实时备份如果**

**以下操作基本在NFS服务器中进行**

- 1、下载inotify-tools

```
yum install -y inotify-tools
```

- 2、 编写监控脚本

```
vim rsync.sh
    #!/bin/bash
    /usr/bin/inotifywait -mrq -e modify,delete,create,attrib,move /share/nfs |while read events
    do
        rsync -a --delete  /share/nfs  192.168.23.13:/backup/
        echo "`date +%F\ %T`出现事件$events">>/var/log/rsync.log 2>&1
    done
chmod a+x rsync.sh
```

- 3、免密登录

```
ssh-keygen
ssh-copy-id
```

- 4、在Backup服务器中创建对应的备份目录**（在Backup服务器中进行）**

```
mkdir /backup/nfs -p
```

- 5、在NFS服务器中，执行监控脚本

```
nohup rsync.sh &
```