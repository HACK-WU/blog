**Keepalived+HAProxy+mysql双主实验**

# 1、环境准备

```
node1（HAProxy1）：192.168.10.10
node2（HAProxy2）：192.168.10.20
node3（Mysql1）：192.168.10.30
node4（Mysql2）：192.168.10.40
VIP：192.168.10.99
```

# 2、mysql部署

```
在node3和node4执行以下脚本：
#!/bin/bash
Ip_addr="192.168.10.40" # 修改为对端的node地址
User_pwd="000000"
systemctl stop firewalld
setenforce 0
yum install mariadb-server -y
sed -i '/^\[mysqld\]$/a\binlog-ignore = information_schema'
/etc/my.cnf.d/server.cnf
sed -i '/^\[mysqld\]$/a\binlog-ignore = mysql' /etc/my.cnf.d/server.cnf
sed -i '/^\[mysqld\]$/a\skip-name-resolve' /etc/my.cnf.d/server.cnf
sed -i '/^\[mysqld\]$/a\auto-increment-increment = 1'
/etc/my.cnf.d/server.cnf # 注意node4节点上必须不同
sed -i '/^\[mysqld\]$/a\log-bin = mysql-bin' /etc/my.cnf.d/server.cnf
sed -i '/^\[mysqld\]$/a\auto_increment_offset = 1' /etc/my.cnf.d/server.cnf
# 注意node4节点上必须不同
sed -i '/^\[mysqld\]$/a\server-id = 1' /etc/my.cnf.d/server.cnf # 注意node4节
点上必须不同
systemctl restart mariadb
mysql -uroot -e "grant replication slave on *.* to 'repuser'@'$Ip_addr'
identified by '$User_pwd';"
查询node3节点master状态：
MariaDB [(none)]> show master status;
+------------------+----------+--------------+--------------------------+
| File       | Position | Binlog_Do_DB | Binlog_Ignore_DB     |
+------------------+----------+--------------+--------------------------+
| mysql-bin.000003 |    402 |       | mysql,information_schema |
+------------------+----------+--------------+--------------------------+
查询node4节点master状态
MariaDB [(none)]> show master status;
+------------------+----------+--------------+--------------------------+
| File       | Position | Binlog_Do_DB | Binlog_Ignore_DB     |
+------------------+----------+--------------+--------------------------+
| mysql-bin.000003 |    407 |       | mysql,information_schema |
+------------------+----------+--------------+--------------------------+
在node3节点执行连接命令：
MariaDB [(none)]> change master to
master_host='192.168.10.40',master_port=3306,master_user='repuser',master_pa
ssword='000000',master_log_file='mysql-bin.000003',master_log_pos=407;
MariaDB [mysql]> start slave;
在node4节点执行连接命令：
MariaDB [(none)]> change master to
master_host='192.168.10.30',master_port=3306,master_user='repuser',master_pa
ssword='000000',master_log_file='mysql-bin.000003',master_log_pos=402;
MariaDB [mysql]> start slave;
查看从节点状态： show slave status \G; 观察IO和SQL线程是否为YES
MariaDB [(none)]> show slave status \G;
 Slave_IO_Running: Yes
 Slave_SQL_Running: Yes
测试：
1. 在node3上创建mydb数据库
2. 在node4上在test数据库中创建test表
3. 最终要实现node3和node4上保持数据同步
```

# 3、HAProxy部署

```
在noproxy/haproxy.cfg{,.bak}
cat > /etc/haproxy/haproxy.cfg << EOF
global
 log 127.0.0.1 local2
  chroot /var/lib/haproxy
 pidfile /var/run/haproxy.pid
 maxconn 4000
 user haproxy
 group haproxy
 daemon
 stats socket /var/lib/haproxy/stats
listen mysql_proxy
 bind 0.0.0.0:3306
 mode tcp
 balance source
 server mysqldb1 192.168.10.30:3306 weight 1 check
 sede1和node2上执行以下脚本：
#!/bin/bash
yum install haproxy -y
mv /etc/harver mysqldb2 192.168.10.40:3306 weight 2 check
listen stats
 mode http
 bind 0.0.0.0:8080
 stats enable
 stats uri /dbs
 stats realm haproxy\ statistics
 stats auth admin:admin
EOF
systemctl start haproxy
```

# 4、keepalived部署

```
node1上执行以下脚本：
#!/bin/bash
yum install keepalived -y
mv /etc/keepalived/keepalived.conf{,.bak}
cat > etc/keepalived/keepalived.conf << EOF
! Configuration File for keepalived
global_defs {
 router_id node1
}
vrrp_script chk_http_port {
 script "/usr/local/src/check_proxy_pid.sh"
 interval 1
 weight -2
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
 track_script {
 chk_http_port
 }
 virtual_ipaddress {
  192.168.10.100
 }
}
EOF
systemctl start keepalived
node2上执行以下脚本：
#!/bin/bash
yum install keepalived -y
mv /etc/keepalived/keepalived.conf{,.bak}
cat > etc/keepalived/keepalived.conf << EOF
! Configuration File for keepalived
global_defs {
 router_id node2
}
vrrp_instance VI_1 {
 state MASTER
 interface ens33
 virtual_router_id 10
 priority 99
 advert_int 1
 authentication {
 auth_type PASS
 auth_pass 1111
 }
 virtual_ipaddress {
  192.168.10.100
 }
}
EOF
systemctl start keepalived
[root@node1 src]# cat check_proxy_pid.sh
#!/bin/bash
A=`ps -C haproxy --no-header | wc -l`
if [ $A -eq 0 ];then
    exit 1
else
    exit 0
fi
```