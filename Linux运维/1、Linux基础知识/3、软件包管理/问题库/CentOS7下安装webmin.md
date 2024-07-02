# CentOS7下安装webmin

webmin官网：http://www.webmin.com/rpm.html



# 1、下载rpm包

```javascript
wget http://prdownloads.sourceforge.net/webadmin/webmin-1.900-1.noarch.rpm
```

2、安装依赖包

```javascript
yum -y install perl perl-Net-SSLeay openssl perl-IO-Tty perl-Encode-Detect
```

3、安装webmin

```javascript
rpm -U webmin-1.900-1.noarch.rpm
```



4、访问说明

The rest of the install will be done automatically to the directory /usr/libexec/webmin,

the administration username set to root and the password to your current root password.

You should now be able to login to Webmin at the URL http://localhost:10000/.

Or if accessing it remotely, replace localhost with your system’s IP address.



其余的安装将自动完成到目录/usr/libexec/webmin，

管理用户名设置为root，密码设置为当前root密码。

现在您应该可以通过URL http://localhost:10000/登录到Webmin了。

或者如果远程访问它，用系统的IP地址替换localhost。