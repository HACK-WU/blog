**Centos7 使用 Iptables 为 netfilter 应用层工具**

```
rpm -e --nodeps firewalld
yum -y install iptables-services
systemctl start iptables
systemctl enable iptables
```