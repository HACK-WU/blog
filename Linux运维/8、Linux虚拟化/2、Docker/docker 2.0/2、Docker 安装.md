**二、Docker 安装**

# 1 、Docker 的安装方式

## 1）Script Install

```
yum update
$ curl -sSL 
systemctl start docker
systemctl enable docker
docker run hello-world
```

## 2）YUM Install

```
wget -O /etc/yum.repos.d/docker-ce.repo https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
yum install -y docker-ce
```

## 3）RPM Install

[https://download.docker.com/linux/centos/7/x86_64/stable/Packages/](https://download.docker.com/linux/centos/7/x86_64/stable/Packages/)

# 2、Docker daemon管理（远程连接）

**服务端配置过程（192.168.23.16）**

- 1,修改docker配置文件前，请先关闭docker守护进程

```
[root@daniel -]# systemctl stop docker
```

- 2,通过/etc/docker/daemon.json文件对docker守护进程文件进行配置

```
[root@danie7 -]# vim /etc/docker/daemon.json
{
    "hosts": ["tcp://0.0.0.0:2375","unix:///var/run/docker.sock"]
}
[root@daniel -]# netstat -ntlup | grep :2375
[root@danie1 -]# ls /var/run/docker.sock
```

PS: docker daemon默认侦听使用的是unix格式，侦听文件：UNIX:///run/docker.sock,添加tcp: //0.0.0.0:2375可实现远程管理。

- 3,添加/etc/docker/daemon.json后会导致docker daemon无法启动,请先修改如下文件内容:

```
修改前：
[root@danie1 -]# vim /usr/lib/systemd/system/docker .service
[Service]
Type=notify
# the default is not to use systemd for cgroups because the delegate issues still
# exists and systemd currently does not support the cgroup feature set required
# for containers run by docker
ExecStart=/usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock
修改后：
[root@danie1 -]# vim /usr/lib/systemd/system/docker .service
[service]
Type=notify
# the default is not to use systemd for cgroups because the delegate issues still
# exists and systemd currently does not support the cgroup feature set required
# for containers run by docker
Execstart=/usr/bin/dockerd
```

- 客户端远程连接 

```
[root@server2 ~]# docker -H 192.168.23.16 version
Client: Docker Engine - Community
 Version:           20.10.17
 API version:       1.41
 Go version:        go1.17.11
 Git commit:        100c701
 Built:             Mon Jun  6 23:05:12 2022
 OS/Arch:           linux/amd64
 Context:           default
 Experimental:      true

Server: Docker Engine - Community
 Engine:
  Version:          20.10.17
  API version:      1.41 (minimum version 1.12)
  Go version:       go1.17.11
  Git commit:       a89b842
  Built:            Mon Jun  6 23:03:33 2022
  OS/Arch:          linux/amd64
  Experimental:     false
 containerd:
  Version:          1.6.8
  GitCommit:        9cd3357b7fd7218e4aec3eae239db1f68a5a6ec6
 runc:
  Version:          1.1.4
  GitCommit:        v1.1.4-0-g5fd4c4d
 docker-init:
  Version:          0.19.0
  GitCommit:        de40ad0
```

> **连接成功！！！**


# 3、Docker 镜像加速配置

```
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://y6hegoqi.mirror.aliyuncs.com"]
}
EOF
 systemctl daemon-reload
 systemctl restart docker
```

阿里云 Docker 镜像加速：[https://cr.console.aliyun.com/cn-hangzhou/instances/mirrors](https://cr.console.aliyun.com/cn-hangzhou/instances/mirrors) 登录即可获得自己的专属加速网址[](https://dev.aliyun.com/search.html)

# 4、Docker 化应用体验

环境分析

WordPress 运行环境需要如下软件的支持：

-  PHP 5.6 或更新软件

-  MySQL 5.6 或 更新版本

-  Apache 和 mod_rewrite 模块

代码展现

```
docker pull wordpress
docker pull mariadb
docker images #可以查看到下载的镜像
docker run --name db --env MYSQL_ROOT_PASSWORD=example -d mariadb  #指定了root的密码为example
docker run --name MyWordPress --link db:mysql -p 8080:80 -d wordpress
```

在Windows中用浏览器输入localhost:8080 然后出现WordPress页面，连接数据库时，

数据库地址要使用镜像内的地址（比如：172.17.0.2），而不是虚拟机的地址。