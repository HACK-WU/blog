# 1、内存相关

```
#内存使用率
free -m | grep "Mem" | awk '{print $3/$2}'
#可使用内存
free -h  | grep "Mem" | awk '{print $NF}'
```

# 2、网络相关

```
#检查某个端口号是否被占用
netstat  -ntul  | grep ":[port]"
    或者
ss  -ntul  | grep ":[port]"        #推荐，这个效率更高
```

# 3、进程相关

```
#检查某个进程是否在运行
ps -ef  | grep '[service_name]' | grep -vE  "\sgrep\s"
```