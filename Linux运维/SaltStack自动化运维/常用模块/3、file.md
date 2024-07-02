# 1、file.file_exists

- 判断某个文件是否存在

```shell
salt '*' file.file_exists /opt/check.sh    
```

# 2、file.access

- 判断文件是否有某种属性或者权限

```shell
salt '*' file.access /opt/check.sh f  
#查看/opt/check.sh文件是否存在,这个挺有用的，我们查看某个脚本或者某个文件是否存在。
#等价于salt '*' file.file_exists /opt/check.sh    
salt '*' file.access /opt/check.sh x 
#如果有此脚本之后，我们还可以查看此脚本是否具有执行权限，f、r、w、x,只能写一种，真就返回true，否则false。
```

# 3、file.get_mode

- 以数字的格式获取文件的权限

```shell
salt '*' file.get_mode /etc/passwd
#file.get_mode后面指定目录或者文件，可以查看其授权情况，如文件一般是0644，如果文件或目录不存在无信息
```

# 4、file.stats

- 返回文件的详细信息

```shell
salt '*' file.stats /etc/passwd 
#file.stats返回一个文件或目录的统计信息，这里是返回/etc/passwd文件的统计信息（类型，时间，属组，权限等）
```

# 5、file.append和file.write

- 前者将内容追加到文件的末尾，后者是直接覆盖类似于echo >，但是格式跟前者一样。

- **使用麻烦，不推荐使用。使用cmd.run 代替比较好。**

```shell
第一组：单引号和双引号的区别，还有！需要注意的地方：
salt '*' file.append /tmp/1 "`hostname` This is a good day\!"  #用双引号，就是里面可以接变量，但是这个！需要注意，不用\转义的话会报错。
salt '*' file.append /tmp/1 '`hostname` This is a good day!'  #所以如果出现！最好放到单引号里面来引用，因为上面就算转义了也显示的不对。
 
第二组：换行符的使用
salt '*' file.append /tmp/1 "Two""Two Two"  #这表示两组字符串在一行，中间默认加个空格隔开。
salt '*' file.append /tmp/1 "Two" "Two Two"  #这表示两组字符串是换行的。
 
第三组：！是不知道怎么解决了，要么就放单引号。还有个=是需要注意的，有个args用法，以及[]外面加不加双引号的区别。
salt '*' file.append /tmp/1 args='Hostname=`hostname`'   #如果字符串里面有等号要用这种args的用法。
salt '*' file.append /tmp/1 args="Hostname=`hostname`"  #atgs用单引号和双引号的区别就在于里面的变量是否解析为变量的值还是字符串。
salt '*' file.append /tmp/1 args=['Hostname=`hostname`''rel=`cat /etc/redhat-release`']  #这里就是定义一个并排的两个字符串
salt '*' file.append /tmp/1 args=['Hostname=`hostname`','rel=`cat /etc/redhat-release`']  #可见args默认是单引号的形式，两组字符串中间加逗号。
salt '*' file.append /tmp/1 args="['Hostname=`hostname`','rel=`cat /etc/redhat-release`']" #双引号不是默认的，所以要单独的加上。
```

# 6、file.chgrp、file.chown和file.set_mode

- 前者是更改文件的属组，中者是更改文件数的属主属组，后者是更改文件或目录的权限

```shell
salt '*' file.chgrp /tmp/1 test9  
#将minion端的/tmp/1文件更改用户组为test9，如果客户端有此用户组则返回None，没有此用户组则返回用户组不存在

salt '*' file.chown /tmp/1 test7 test9  
#第一个是用户，第二个是用户组，固定格式必须存在，将/tmp/1的用户组设置为test7,用户组设置为test9.

salt '*' file.set_mode /opt/cs 0550  #设置/opt/cs目录权限为0550，如果授权成功会显示授权后的权限，如果没有此文件或目录会提示
```

# 7、file.comment和file.comment_line

- 注释指定内容的行，每次操作前都会更新文件名命令的.bak备份文件

```shell
salt '*' file.comment /tmp/passwd ftp  
#这就是将以/tmp/passwd文件以ftp开头的行注释掉，如果注释会显示注释行的信息，如果没注释则返回False

salt '*' file.comment /tmp/passwd ftp.*nologin$  
#另外还支持正则表达式，这里就表示以ftp开头以nologin结尾的行。多行注释

salt '*' file.comment /tmp/passwd ftp.*nologin$ '-' 
#后面可以指定在行开头加什么字符，当然默认是#，前面也可以这样后面指定要行头添加的字符。
```

# 8、file.copy

- 复制文件或目录到指定的目录下面，成功返回True，失败会有提示的。另外还有file.move，移动文件的用法。

```shell
salt '*' file.copy /path/to/src /path/to/dst   #这里是文件拷贝，将文件/path/to/src拷贝到/path/to/目录下面，其名称为dst,切记这里一定要是文件名。
salt '*' file.copy /opt/file2  /tmp/haha/ recurse=True   #这种不管时/tmp/haha/还是/tmp/haha，如果这个haha存在的话，就是将/opt/file2下面的内容cp -r拷贝到/tmp/haha目录下面，如果haha目录不存在的话，就是将/opt/file2目录变为/tmp/haha目录。
salt '*' file.copy /opt/file2  /tmp/haha/file2 recurse=True  #这种才是正确的将/opt/file2目录复制到/tmp/haha/目录下方。如haha目录不存在会创建
salt '*' file.copy /path/to/src_dir /path/to/dst_dir recurse=True remove_existing=True  #remove_existing=True 这种就是完全覆盖的形式。
```

# 9、file.directory_exists和file.dirname

```shell
salt '*' file.directory_exists /tmp/haha/file2  #/tmp/haha/file目录存在就会返回True，不存在就会返回False
salt '*' file.dirname '/opt/file2/'  #取出来的结果是/opt/file2，这就是末尾加/，认为这两个都是目录，当然不管是否有这个目录
salt '*' file.dirname '/opt/file2/test1' #取出来的结果是/opt/file2
```

# 10、file.find

- 类似于linux 下的find命令

```shell
salt '*' file.find / type=f name=\*.bak size=+10m  
#查找/目录下，文件类型为文件的（a：所有文件类型，b：块设备 ，c：字符设备，d：目录， p：FIFO（命名管道）， f：普通文件 ，l：符号链接 ，s：套接字），名称为.bak结尾的(这里支持正则表达式)，大小大于10MB的文件（b：字节，k：千字节，m：兆字节，g：GB，t：太字节也是TB）。
 
salt '*' file.find /var mtime=+30d size=+10m print=path,size,mtime  
#这里是查找/var目录下，最后一次更改时间是30天以前（w：周,d：天,h：小时,m：分钟,s：秒），大小大于10MB的文件，并打印文件的路径，大小，更改时间(可打印的内容有：group:组名,md5：文件内容的MD5摘要,mode：文件权限（以整数形式）,mtime：最后修改时间,name：文件基础名称,path：文件绝对路径,size：文件大小（以字节为单位）,type：文件类型,user：用户名)。
 
salt '*' file.find /var/log name=\*.[0-9] mtime=+30d size=+10m delete   
#find的匹配条件有（name区分大小写，iname不区分大小写，type类型，user用户，group用户组，size[+-]大小，mtime修改时间，grep搜索文件内容），最后执行的动作除了delete和print，还有exec command。
```

# 11、file.get_gid、file.get_uid 和file.get_group、file.get_user的用法

- 前一组返回文件或目录的gid号和uid号，后一组返回文件或目录group和user

```shell
salt '*' file.get_user /etc  #查看/etc目录的属组，如果文件或目录不存在返回false。
salt '*' file.get_uid /etc  #查看/etc目录的属组的uid号。如果目录或者文件不存在返回-1.
```

# 12、file.grep

- 类似grep命令

```shell
salt '*' file.grep /etc/passwd nobody  
#过滤/ect/passwd文件中包含nobody的行。（会输出：pid:是grep运行的pid号，retcode:为状态码，0是成功过滤1为非成功过滤，stderr:错误输出，stdout:正常输出也就是我们要过滤的内容。）
 
salt '*' file.grep /etc/sysconfig/network-scripts/ifcfg-eth0 ipaddr " -i"  
#-i的目的是不区分大小写,注意-i前面有空格，额外的参数之间都有空格。
 
salt '*' file.grep /etc/sysconfig/network-scripts/ifcfg-eth0 ipaddr " -i -B2 -A2" 
#-B2就是连上面两行也过滤出来,-A2就是连下两行也过滤出来。
```

# 13 file.mkdir

- 递归创建目录，相当于mkdir -p  

```shell
salt "node" file.mkdir /tmp/test/test
```