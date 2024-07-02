# ping_hosts.sh

```
#!/usr/bin/bash
# encoding:utf-8
# 批量主机ping探测
# author: hackwu
# 2022/7/1

#探测网段中，有多少活跃的主机

set -u
>ip.txt		#重定向，这里相当于创建一个ip.txt文件

for item in {1..254}	
do
{	
	ip=192.168.23.$item
	ping -c1 -W1 $ip &>/dev/null
	if [ $? -eq 0 ];then
		echo $ip | tee -a ip.txt
	fi		
}&
done
wait
echo "finish....."
```