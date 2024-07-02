 禁止解析 PHP


某个目录下禁止解析 PHP，这个很有作用，我们做网站安全的时候，这个用的很多，比如某些目录可


以上传文件，为了避免上传的文件有木马，所以我们禁止这个目录下面的访问解析 PHP。


析 配置禁止解析 php





```javascript
<Directory /usr/local/apache2/htdocs/data>
php_admin_flag engine off
<filesmatch "(.*)php">
Order deny,allow
Deny from all
</filesmatch>
</Directory>
```

