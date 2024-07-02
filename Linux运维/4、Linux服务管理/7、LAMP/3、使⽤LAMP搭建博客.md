使⽤LAMP搭建博客


# 1、APACHE安装和防⽕墙放⾏


```javascript
yum -y install httpd wget unzip
systemctl start httpd
systemctl enable httpd
firewall-cmd --add-port=80/tcp --permanent
firewall-cmd --reload
```



![](images/WEBRESOURCEfc38c205db4bf3ab1a6ae505ba1ab273截图.png)

安装PHP


```javascript
yum -y install php-* --skip-broken php-mysqlnd
cd /var/www/html
wget http://mirrors.eagleslab.com:8889/tz.php
systemctl restart httpd
```



![](images/WEBRESOURCEd49ab01fa5257c2aad980629e708f77a截图.png)

安装mariadb


```javascript
wget http://repo.mysql.com/mysql-community-release-el7-5.noarch.rpm
 rpm -ivh mysql-community-release-el7-5.noarch.rpm
 
 
yum -y install mariadb mariadb-server
systemctl start mariadb
systemctl enable mariadb
mysql_secure_installation
```



![](images/WEBRESOURCE7bce93a29311ed9dad78b9d0f104dee1截图.png)

