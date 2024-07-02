# 1、cron.raw_cron

- cron.list_tab和cron.ls和跟其效果一样，格式也一样必须要指定某一个用户，都是显示指定用户crontab文件里面的定时任物

```shell
salt '*' cron.raw_cron root  
#必须指定用户，这里是显示root的crontab文件里面的内容，注释的行也会显示
```

# 2、cron_set_job

- 为指定用户设置一个定时任务

```shell
salt '*' cron.set_job root '0' '0' '*' '*' '*' '/bin/bash /opt/scripts/scp.sh >/dev/null 2>&1'  
#如果'/bin/bash /opt/scripts/scp.sh >/dev/null 2>&1'这一部分存在了，那么这一步操作就是update，也就是更新前面执行crontab的时间，如果不存在，这就相当于一条添加定时任务的操作返回内容为new。
```

# 3、cron.rm_job

- 删除指定用户指定的的定时任务

```shell
salt '*' cron.rm_job root '/bin/bash /opt/scripts/scp.sh >/dev/null 2>&1'   
#注意格式是用户 后面跟要删除的任务，不要加前面的时间，成功会返回removed，如果没有这条记录会返回absent。
```