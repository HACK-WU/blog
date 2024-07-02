# 1、cp.get_dir和cp.get_file

```shell
salt '*' cp.get_dir salt://path/to/dir/ /minion/dest    
#从salt master递归复制目录到minion客户端的/minion/dest目录下面。

salt '*' cp.get_file salt://path/to/file /minion/dest     
#从服务端拷贝单个文件到minion端的/minion/dest目录下面。

salt '*' cp.get_file "salt://{{grains.os}}/vimrc" /etc/vimrc template=jinja  
#所有Salt minions从与其os粒度相同名称的目录下载vimrc，并将其复制到/etc/vimrc
```

# 2、cp.push和cp.push_dir

- 就是把客户端的文件或者目录推送到master端的cachedir，默认为/var/cache/salt/master/minions/minion-id/files，但是这种用法是

禁用

# **3、cp.get_url**

- 用于从url上获取文件内容，只能是文本文件

```shell
salt '*' cp.get_url salt://cptest1/cptest1file /tmp/test  
#将salt://cptest1/cptest1file文件里面的内容写入到/tmp/test文件里面，每次都会覆盖里面的内容。这种就跟cp.get_file一样。
 
salt '*' cp.get_url http://www.51niux.com/?id=116 /tmp/test  
#如这种就把一个页面的html信息写入到了客户端的/tmp/test文件，切记只能是这种文本形式的文件，不要是压缩包啥的。
 
salt '*' cp.get_url http://www.51niux.com/zb_users/upload/2017/03/201703091489030442220789.txt  /tmp/load_one_check.sh  
#最主要的用法还是这种，我们可以以httpd的形式去下载一个sh脚本啊，config文件啊等。
salt '*' cmd.run " cat /tmp/load_one_check.sh"  
```

# 4、cp.list_master和list_master_dirs

- 这个就是查看salt master本地的file服务器又哪些文件或者目录

```shell
salt 'salt1' cp.list_master   #这种就不要指定所有机器了，匹配一台机器就可以了，列出存储在主机上的所有文件。
salt 'salt1' cp.list_master_dirs  #列出存储在master主机上面的所有目录。
```