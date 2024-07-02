**三、Docker 容器管理**

# 1 、Docker 基础概念

**Docker 三个重要概念：**

- 仓库 (Repository) 

- 镜像 (image) 

-  容器 (Container)

> **1、从远程仓库中下载镜像**
> **2、然后运行进行，就变成了容器（实例化）**
> **3、镜像可以自己制作，也可以从仓库中下载**


```
docker run --name MyWordPress --link db:mysql -p 8080:80 -d wordpress
Docker 指令的基本用法：
docker + 命令关键字(COMMAND) + 一系列的参数
```

# 2 、Docker 基础命令

```shell
docker info             守护进程的系统资源设置
docker search Docker     仓库的查询
docker pull Docker         仓库的下载
docker images Docker     镜像的查询
docker rmi Docker         #rmi(rm-image)镜像的删除
docker rm docker        #删除容器
    -f                    #强制删除
docker ps                # 容器的查询，查看正在运行的镜像
    -a          #查看所有的容器
docker run           #  容器的创建启动
    -d               #后台运行
docker start/stop     #容器启动停止
docke restart        #重启容器
docker save nginx:latest -o /root/nginx_latest     #另存镜像到本地
docker load <  /root/nginx_latest     #导入本地镜像   
docker top  查看各个容器的运行情况。
```

**Docker 指令除了单条使用外，还支持赋值、解析变量、嵌套使用**

# 3 、单一容器管理命令

每个容器被创建后，都会分配一个 CONTAINER ID 作为容器的唯一标示，后续对容器的启动、停

止、修改、删除等所有操作，都是通过 CONTAINER ID 来完成，偏向于数据库概念中的主键

```shell
docker ps                        #查看正在运行的容器
    -q                           #仅显示容器的id
    -a                           #查看所有的容器
    --no-trunc                   #查看，显示完成的ID号
docker rm [容器名|容器id]        #删除容器
    docker rm $(docker ps -qa)   #删除所有的容器 
docker stop/start CONTAINERID          停止
docker start/stop MywordPress          通过容器别名启动/ 停止
docker inspect MywordPress             （inspect,检查）查看容器所有基本信息
docker logs MywordPress                查看容器日志
docker stats MywordPress               查看容器所占用的系统资源
docker exec 容器名 容器内执行的命令      容器执行命令
docker exec -it 容器名 /bin/bash      登入容器的 bash
docker commit [容器名]  [镜像名：v1]    #将一个容器，提交成为一个新的镜像
docker cp /www/runoob 96f7f14e99ab:/www/    #将主机/www/runoob目录拷贝到容器96f7f14e99ab的/www目录下。
```

# 4、容器的交互模式

## **4.1运行容器时，指定为交互模式**

```shell
#使用下面命令启动容器；-i指交互；-t指tty终端；--name是用来指定容器名称
[root@danie1 -]# docker run -i -t --name=c1 centos:1atest /bin/bash
root@f736fe36002c /]# cat /etc/redhat-release
Centos Linux release 7.6.1810（Core）        #可以看到我们下载的centos是7.6版本
[root@f736fe36002c /]# uname -r
3.10.0-862.e17.x86_64    #查看的内核舍机centos7.5一样，说明是共享宿主机的内核在容器内操作（我这里创建一个文件，然后退出）
[root@f736fe36002c /]]# touch /root/daniel
[root@f736fe36002c /]# exit
exit
```

> **1、使用这种方式，当容器运行时，用户会直接进入到容器内部**
> **2、但是当用户退出容器时，容器同时也会停止运行。**
> **3、所以不推荐这种方式。**


## 4.2 运行容器后，在指定为交互模式

```
docker exec -it [容器名] /bin/bash        #退出后，容器依然正常运行
docker attach [容器名]        #退出后，容器停止运行。
```

 

# 5 、Run 常用的一些参数

```shell
docker run --name httpd -p 80:80 -d httpd_image /usr/sbin/httpd    #启动容器时，运行/usr/sbin/httpd命令
--restart=always        #容器的自动启动
--link httpd2:serer1    #给httpd2容器起个别名为，server1.并加入到hosts文件中
-h x.xx.xx           #设置容器主机名
-dns xx.xx.xx.xx     #设置容器使用的 DNS 服务器
--dns-search DNS     #搜索设置
--add-host hostname:IP   #注入 hostname <> IP 解析
--rm                 #服务停止时自动删除
    #docker rm $(docker ps -qa)    删除所有的容器
-d                 #后台运行
-c "date"    #启动后执行date 这个linux命令
-e        #设置环境变量
```

# 6 、Docker-Compose

除了使用docker commit把自定义容器提交成镜像外，还可以使用Dockerfile来构建自定义镜像。什么是Dockerfile?

答：Dockerfile把构建镜像的步骤都写出来，然后按顺序执行实现自动构建镜像。就类似于脚本文件，ansiblel的playbook,saltstack的sls文件等。

> **docker-compose命令可以用于管理和执行docker-compose.yaml 文件，这个文件就类似于一个脚本文件，**
> **可以自动的去下载一些脚本，并设定它的规则，比如是否自启等等。。。**


## 1）Docker-compose installl

```
wget https://github.com/docker/compose/releases/download/v2.2.3/docker-compose-linux-x86_64
mv docker-compose-linux-x86_64 /usr/bin/docker-compose 
chmod a+x /usr/bin/docker-compose    
docker-compose --version   #查看版本信息
```

## 2）Docker-compose 用法

**（compose:撰写）**

```
-f             #指定使用的 yaml 文件位置 
ps             #显示所有容器信息
restart        #重新启动容器 
logs           #查看日志信息 
config -q      #验证 yaml 配置文件是否正确
stop           #停止容器
start          #启动容器
up -d          #启动容器项目
pause          #暂停容器
unpause        #复暂停
rm             #删除容器
```

## 3）演示代码记录

```
[root@server1 ~]# vim docker-compose.yaml 

version: '2'

services:
  db:
    image: mysql:5.7
    restart: always
    environment:
        MYSQL_ROOT_PASSWORD: somewordpress
        MYSQL_DATABASE: wordpress
        MYSQL_USER: wordpress
        MYSQL_PASSWORD: wordpress

  wordpress:
    depends_on:
        - db
    image: wordpress:latest
    restart: always
    ports:
        - "8080:80"
    environment:
        WORDPRESS_DB_HOST: db:3306
        WORDPRESS_DB_USER: wordpress
        WORDPRESS_DB_PASSWORD: wordpress
```

```
docker-compose up -d  #执行docker-compose.yaml文件
```