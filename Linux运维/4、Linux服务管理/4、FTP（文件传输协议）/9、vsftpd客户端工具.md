- 下载

```
yum install ftp lftp -y
```

# ftp工具（单个文件操作）

```
//使用：
[root@server1 ~]# ftp 10.1.1.10
Connected to 10.1.1.10（10.1.1.10）.
220（VSFTPd 3.0.2）
Name (10.1.1.10:root):             //输入FTP的账号
331 Please specify the password.
Password：                         //输入FTP账号对应的密码
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files .
ftp>
```

- 语法

```
ftp> help       //帮助
ftp> ls        //显示文件列表
Ctrl+shift+L    //清屏
ftp> put        //上传
ftp> mput       //批量上传
ftp> send       //上传（效率更高）
ftp> get        //下载
ftp> mget        //批量下载
ftp> quit        //退出
ftp> pwd        //显示所在路径
ftp> prompt    //屏蔽批量输出，提示信息
```

# lftp工具 (批量操作)

```
//使用：
[root@server1 ~]# lftp hack@192.168.23.12
口令:                //密码
```

- 语法：

```
lftp> help    //帮助
lftp> mirror -R    shop    //将shop文件夹连同其中的文件上传到服务器
lftp> mirror ./            //将ftp目录下的文件，都下载到本地
```