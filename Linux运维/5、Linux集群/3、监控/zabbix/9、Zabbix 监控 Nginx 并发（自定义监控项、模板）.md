**Zabbix 监控 Nginx 并发（自定义监控项、模板）**

- 1 ）源码编译安装 Nginx 服务器并开启状态统计模块

```
vim /etc/nginx/ngxin.conf
    location /nginx-status{
        stub_status on;
        }
        
        
nginx -t
重启
```

- 2 ）Zabbix 客户端配置

# -1 、编写 Nginx 监控脚本，在被监控端

```auto
#!/bin/bash
HOST="127.0.0.1"
PORT="80"
#检测 nginx 进程是否存在
function ping {
/sbin/pidof nginx | wc -l
}
#检测 nginx 性能
function active {
/usr/bin/curl "http://$HOST:$PORT/ngx_status/" 2>/dev/null| grep 'Active' | awk '{print $NF}'
}
function reading {
/usr/bin/curl "http://$HOST:$PORT/ngx_status/" 2>/dev/null| grep 'Reading' | awk '{print $2}'
}
function writing {
/usr/bin/curl "http://$HOST:$PORT/ngx_status/" 2>/dev/null| grep 'Writing' | awk '{print $4}'
}
function waiting {
/usr/bin/curl "http://$HOST:$PORT/ngx_status/" 2>/dev/null| grep 'Waiting' | awk '{print $6}'
}
function accepts {
/usr/bin/curl "http://$HOST:$PORT/ngx_status/" 2>/dev/null| awk NR==3 | awk '{print $1}'
}
function handled {
/usr/bin/curl "http://$HOST:$PORT/ngx_status/" 2>/dev/null| awk NR==3 | awk '{print $2}'
}
function requests {
/usr/bin/curl "http://$HOST:$PORT/ngx_status/" 2>/dev/null| awk NR==3 | awk '{print $3}'
}
#执行 function
$1
```

# .-2 、将自定义的 UserParameter 加入配置文件，然后重启 agentd

```auto
#cat /usr/local/zabbix-3.0.0/etc/zabbix_agentd.conf | grep nginx
UserParameter=nginx.status[*],/usr/local/zabbix-3.0.0/scripts/ngx-status.sh $1
#killall zabbix_agentd
#/usr/local/zabbix-3.0.0/sbin/zabbix_agentd
```

# -3 、zabbix_get 获取数据

```auto
#/usr/local/zabbix-3.0.0/bin/zabbix_get -s 10.10.1.121 -k 'nginx.status[accepts]'
9570756
#/usr/local/zabbix-3.0.0/bin/zabbix_get -s 10.10.1.121 -k 'nginx.status[ping]'
```