# 1、如果是client使用了虚拟网卡，而nfs-Server使用真实网卡，那么nfs服务器中/etc/exports  文件不能填写虚拟网卡的ip，而是填写共享的真实网卡的ip；

# 2、可以查看日志文件/var/log/messages中寻求办法