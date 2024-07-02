# 1、scp ：安全的远程文件复制命令

scp是secure copy的简写，用于在Linux下进行远程拷贝文件的命令，类似于命令有cp，scp传输是加密的，所以可能

会稍微影响一点速度。另外，scp还非常不占资源，不会提高多少系统负荷

```javascript
格式：scp 本地文件 用户名@服务器IP:目录     #不能有空格
scp /root/atguigu.txt root@192.168.88.20:/tmp
    -P 端口 #若端口不是默认22，则需要使用此格式指定端口
```

```javascript
[root@client ~]# scp -P 22  /root/scp_test2  /root/scp_test4  root@129.168.1.16:/root/test/   
^C[root@client ~]# scp /root/scp_test2  /root/scp_test4  root@129.168.1.106:/root/test/
^C[root@client ~]# scp /root/scp_test2  /root/scp_test4  root@192.168.1.106:/root/test/
root@192.168.1.106's password: 
scp_test2                                                                              100%   42     6.2KB/s   00:00    
scp_test4                                                                              100%   32     7.4KB/s   00:00      #完成传输
[root@client ~]# ssh 192.168.1.106                      #远程服登录务器
root@192.168.1.106's password: 
Last login: Mon Aug 16 23:55:12 2021 from 192.168.1.103 #登录成功
[root@server ~]# ls /root/test/                         #查看文件，传输成功
scp_test2  scp_test4
[root@server ~]# 
```

# 2、sftp ：安全的文件传输协议（文件传输每次只能传一个文件，如果是目录加-r)

sftp是Secure FileTransferProtocol的缩写，安全文件传送协议。sftp与ftp有着几乎一样的语法和功能。由于这种

传输方式使用了加密/解密技术，所以sftp比ftp更安全一些，但传输效率比普通的FTP要低得多

```javascript
格式：sftp 用户名@服务器IP
    sftp
    -oPort=端口 #若端口不是默认22，则需要使用此格式指定端口
交互命令：
    help：查看在交互模式下支持哪些命令
    pwd/lpwd：pwd是查看服务器所在路径；lpwd是查看客户端所在路径
    ls/lls：ls是查看服务器当前目录下的文件列表；lls是查看客户机当前所在路径的所有文件列表
    put：将客户机中的指定文件上传到服务器端                          #put 客户机文件   服务器目录
    get：将服务器端的指定文件下载到客户机的当前所在目录               #get 服务器文件   客户机目录
    rm：删除掉服务器端的指定文件
    quit：退出sftp的交互模式，断开和服务器之间的连接
```

##  1）  put：将客户机中的指定文件上传到服务器端   （目录加-r)

```javascript
[root@client ~]# sftp root@192.168.1.106      #连接服务器
root@192.168.1.106's password: 
Connected to 192.168.1.106.                   #连接成功
sftp> lls                                     #查看客户端/root/下文件
anaconda-ks.cfg  scp_test2  scp_test3  scp_test4
sftp> put /root/scp_test4  /root/test         #上传服务器文件
Uploading /root/scp_test4 to /root/test/scp_test4
/root/scp_test4                                                                        100%   32    36.8KB/s   00:00        #上传完成
sftp> ls /root/test                           #查看服务器端/root/test/下文件
/root/test/scp_test2    /root/test/scp_test4  #传输成功
```

## 2）get:将服务器上的文件下载到客户机     (目录加-r)

```javascript
sftp> get /root/etc.tar.gz  /root/
Fetching /root/etc.tar.gz to /root/etc.tar.gz
/root/etc.tar.gz                                                                       100% 9560KB  44.6MB/s   00:00       #查看客户机文件
sftp> lls
anaconda-ks.cfg  apr-1.4.5  etc.tar.gz	scp_test2  scp_test3  scp_test4     #传输成功
sftp> 
```