**keepalived+nginx实验**

# 1、环境准备

```
node1（Nginx1）：192.168.10.10
node2（Nginx2）：192.168.10.20
node3（WEB1）：192.168.10.30
node4（WEB2）：192.168.10.40
VIP：192.168.10.100
```

# 2、web部署

```
在node3和node4执行下面的脚本：
#!/bin/bash
yum install net-tools httpd -y
systemctl stop firewalld
setenforce 0
echo "<h1>This is RS1</h1>" > /var/www/html/index.html # 修改不同的主页以便测试！
systemctl start httpd
```

# 3、nginx部署

```
node1和node2节点执行以下脚本：
#!/bin/bash
systemctl stop firewalld
setenforce 0
yum install nginx -y
cat > /etc/nginx/conf.d/proxy.conf << EOF
upstream websers{
 server 192.168.10.30;
 server 192.168.10.40;
}
server{
 listen 8080;
 server_name 192.168.10.10;
 location / {
   proxy_pass http://websers;
}
}
EOF
nginx -s reload
```

# 4、keepalived部署

```
在node1和node2节点执行以下脚本：
#!/bin/bash
yum install keepalived -y
mv /etc/keepalived/keepalived.conf{,.bak}
cat > /etc/keepalived/keepalived.conf << EOF
! Configuration File for keepalived
global_defs {
 router_id node1 # node2修改
}
vrrp_instance VI_1 {
 state MASTER # node2节点BACKUP
 interface ens33
 virtual_router_id 10
 priority 100 # node2节点小于100
 advert_int 1
 authentication {
 auth_type PASS
 auth_pass 1111
 }
 virtual_ipaddress {
  192.168.10.100
 }
}
```

# 5、要自定义脚本检测nginx服务是否正常

```
[root@node1 ~]# cat /etc/keepalived/keepalived.conf
! Configuration File for keepalived
global_defs {
 router_id node1
}
# 定义script
vrrp_script chk_http_port {
 script "/usr/local/src/check_nginx_pid.sh"
 interval 1
 weight -2 # 优先级-2
}
vrrp_instance VI_1 {
 state MASTER
 interface ens33
 virtual_router_id 10
 priority 100
 advert_int 1
 authentication {
 auth_type PASS
 auth_pass 1111
 }
  # 调用script脚本
 track_script {
 chk_http_port
 }
 virtual_ipaddress {
  192.168.10.100
 }
}
[root@node1 ~]# cat /usr/local/src/check_nginx_pid.sh
#!/bin/bash
# 当nginx正常的时候状态码为0
# 当nginx不正常时候状态码为1
# 因为当退出状态码为非0的时候会执行切换
nginx_process_number=`ps -C nginx --no-header | wc -l`
if [ $nginx_process_number -eq 0 ];then
#  systemctl restart nginx
  nginx_process_number=`ps -C nginx --no-header | wc -l`
  if [ $nginx_process_number -eq 0 ];then
    exit 1
  else
    exit 0
    fi
else
  exit 0
fi
```