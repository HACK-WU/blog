## **调试SELinux**

配置httpd在82端口上提供Web服务，满足以下要求：

1）在/var/www/html/下添加index.html文件

2）此Web服务器在系统启动时自动启动

3）确保SELinux保护机制运行在Enforcing模式

```
# 准备工作，安装httpd服务，修改监听的端口号为80，考试不需要做。
[root@servera ~]# yum -y install httpd
[root@servera ~]# systemctl enable --now httpd
[root@servera ~]# echo hello yutianedu > /var/www/html/index.html
[root@servera ~]# vim /etc/httpd/conf/httpd.conf # 修改监听的端口号
Listen 82

# 实际操作
[root@servera ~]# systemctl restart httpd #重启服务报错，原因是selinux
# 考试需要查看监听的端口号为多少，再将对应的端口号（82端口）设置为httpd的标准端口即可，
man semanage-port
[root@servera html]# semanage port -l | grep http #搜索80对应的端口号
[root@servera ~]# semanage port -a -t http_port_t -p tcp 82
#重启服务
[root@servera ~]# systemctl restart httpd
#查看端口号82已经监听
[root@servera ~]# netstat -tulnp | grep httpd
tcp6    0    0 :::82          :::*          LISTEN 
  25747/httpd
[root@servera ~]# curl http://servera:82
hello yutianedu
# 说明：考试环境http已经配置好了，考生只需要将82号端口设置为httpd的标准端口即可
```