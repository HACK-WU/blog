Apache 日志切割

需要关闭：跳转&虚拟主机&ssl 配置文件调用




我们每访问一次网站，那么就会记录若干条日志。如果日志不去管理，时间长了日志文件会越来越大，


如何避免产生大的日志文件？其实 apache 有相关的配置，使日志按照我们的需求进行归档，比如每


天一个新日志，或者每小时一个新的日志。


# 1、 首先简单设置日志的路径名称


```javascript
# vim /usr/local/apache2/etc/httpd.conf
```

编辑添加内容如下：


```javascript
ErrorLog "logs/error.log"
CustomLog "logs/access.log" combined
```

指定了日志存放在/usr/local/apache2/logs 目录下分别为 error.log 和 access.log，combined 为日


志显示的格式，日志格式可以参考配置文件 httpd.conf 中格式的指定，如下：


```javascript
LogFormat "%h %l %u %t \"%r\" %>s %b \"%{Referer}i\" \"%{User-Agent}i\"" combined
LogFormat "%h %l %u %t \"%r\" %>s %b" common
```

# 2、设置 apache 日志 分 割


同样编辑配置文件 httpd.conf


```javascript
ErrorLog "|/usr/local/apache2/bin/rotatelogs -l /usr/local/apache2/logs/error_%Y%m%d.log
86400"
CustomLog "|/usr/local/apache2/bin/rotatelogs -l
/usr/local/apache2/logs/access_%Y%m%d.log 86400" combined
```

注意 1： 以上 仅为两条命令 （ 一条错误日志 ，一条访问日志 ） ，路径太长 写 不开


注意 2 ：若 开启了 https ，则需要修改 http- - ssl .conf f 配置 文件中的日志记录条目


ErrorLog 是错误日志，CustomLog 是访问日志。|就是管道符，意思是把产生的日志交给 rotatelog


这个工具，而这个工具就是 apache 自带的切割日志的工具。-l 的作用是校准时区为 UTC，也就是北


京时间。86400，单位是秒，正好是一天，那么日志会每天切割一次。而最后面的 combined 为日志的


格式，在 httpd.conf 中有定义。