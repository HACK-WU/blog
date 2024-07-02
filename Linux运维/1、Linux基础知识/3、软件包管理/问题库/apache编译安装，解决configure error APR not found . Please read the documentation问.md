apache编译安装，解决configure: error: APR not found . Please read the documentation问题！



获得一个httpd-2.4.23.tar.gz

tar -zxf httpd-2.4.23.tar.gz 

cd  httpd-2.4.23

./configure --prefix /usr/local/apache

make&&make install

可是这机器不是我的，各种报错 ‘onfigure: error: APR not found . Please read the documentation’

为了保守起见，把相关关联包都安装一下：



# 1、下载相关的包



```javascript
wget http://archive.apache.org/dist/apr/apr-1.4.5.tar.gz  
wget http://archive.apache.org/dist/apr/apr-util-1.3.12.tar.gz  
wget https://nchc.dl.sourceforge.net/project/pcre/pcre/8.45/pcre-8.45.zip 
```



# 2、 解决apr not found



```javascript
[root@lme test]# tar -zxf apr-1.4.5.tar.gz  
[root@lme test]# cd  apr-1.4.5  
[root@lme apr-1.4.5]# ./configure --prefix=/usr/local/apr  
[root@lme apr-1.4.5]# make && make install  
```



# 3、 解决APR-util not found



```javascript
[root@lme test]# tar -zxf apr-util-1.3.12.tar.gz  
[root@lme test]# cd apr-util-1.3.12  
[root@lme apr-util-1.3.12]# ./configure --prefix=/usr/local/apr-util --with-apr=/usr/local/apr/bin/apr-1-config --enable-utf8   
[root@lme apr-util-1.3.12]# make && make install 
```



# 4、 解决pcre问题



```javascript
[root@lme test]#unzip -o pcre-8.10.zip  
[root@lme test]#cd pcre-8.10  
[root@lme pcre-8.10]#./configure --prefix=/usr/local/pcre  
[root@lme pcre-8.10]#make && make install
```



# 大功告成，开始倒腾apache：



```javascript
./configure --prefix=/usr/local/apache --with-apr=/usr/local/apr --with-apr-util=/usr/local/apr-util --with-pcre=/usr/local/pcre
make && make install
```

