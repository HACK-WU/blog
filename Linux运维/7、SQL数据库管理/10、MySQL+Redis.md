MySQL+ NoSQL(Redis)


# 1.安装 gcc*

![](images/WEBRESOURCE7eab676eda725d2f5bcf15639f747c79截图.png)

# 


2.安装所需要的包

![](images/WEBRESOURCE26d84ea7f975753999deca1f16514883截图.png)






![](images/WEBRESOURCE09db4ecdab9bb627f847f5e4277ed88e截图.png)



# 3.配置网站 nginx 并启动 nginx


```javascript
vim /etc/nginx/nginx.conf
```



![](images/WEBRESOURCE6a131fbca85ef585628d800011c0e34c截图.png)



```javascript
vim /etc/nginx/conf.d/default.conf
```



![](images/WEBRESOURCE0507dbde70e8b0dd589cbe0fbf262916截图.png)



![](images/WEBRESOURCEdccb44a2485df5d4dd2aeaec3e9c33ac截图.png)



## 启动 nginx


```javascript
vim /etc/php-fpm.d/www.conf
```



![](images/WEBRESOURCE45d3be677c790e59a5382f684bab83d1截图.png)



# 4.启动 php 和数据库

![](images/WEBRESOURCEac1ed8ed4af68e548a97879c528ef97d截图.png)

# 




# 5.授权，使登录数据库时使用‘123456’密码

![](images/WEBRESOURCE6479d76ce6a7847dc4338254d0cdd12e截图.png)

# 


6.测试网站和 php 的连通性

![](images/WEBRESOURCE47114fdb2cdb9056b66764d02ae84645截图.png)






![](images/WEBRESOURCE63812e2580a5185207bce042d1ac11e5截图.png)

以上搭建了一个 p lnmp 环境. .




# 7.安装 redis

![](images/WEBRESOURCE078af9ee3338e03d7d98659815adddde截图.png)

# 




![](images/WEBRESOURCE94863e4a0743197fb7004ec4a38f4b3b截图.png)



# 8.安装提供 php 和 redis 联系的软件


a. 解压

![](images/WEBRESOURCE844a9008d7921b76d07bf17dd32f7cff截图.png)




b. 安装

![](images/WEBRESOURCE23ffea47223968f7f7b8a0515b342b94截图.png)






![](images/WEBRESOURCEd48ee63b1a3e33461ebe20375d4f3f67截图.png)

c. 让 php 支持 redis

![](images/WEBRESOURCE9f6f09828e5ef514e11a8edd857f68f3截图.png)






![](images/WEBRESOURCEeb6e844c824a6c5be41eb664a1e62a2e截图.png)



![](images/WEBRESOURCEb3d40362c1e929ece7238aad6f3280b6截图.png)

9.进入 mysql 插数据

![](images/WEBRESOURCE8f810df3f8bf964471cfe2a66d7d43c2截图.png)






![](images/WEBRESOURCE9e5863dbca70b228a20ca815d14ec597截图.png)

10.开启 redis,并编写脚本

![](images/WEBRESOURCE58a9305f46b6bf8384ca6b4642477fa8截图.png)






![](images/WEBRESOURCEb3f0495c6309cec4318743ef6f801c44截图.png)



![](images/WEBRESOURCE774cfb0665469488c5380d7fe354d2c9截图.png)

11.验证 php 访问 redis 和 mysql

![](images/WEBRESOURCEe279ae1a84c0f5737e60c1872ca8ca39截图.png)






![](images/WEBRESOURCE59f3f826453c48dfbccc30c9812bd179截图.png)

