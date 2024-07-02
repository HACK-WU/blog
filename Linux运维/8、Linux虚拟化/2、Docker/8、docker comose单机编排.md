# 1、简介

当在宿主机启动较多的容器时候，如果都是手动操作会觉得比较麻烦而且容器出错，这个推荐使用docker单机编排工具docker-compose，docker-compose是docker容器的一种单机编排服务，docker-compose是一个管理多个容器的工具。比如可以解决容器之间的依赖关系，就像启动一个nginx前端服务的时候会调用后端的tomcat，那就得先启动tomcat，但是启动tomcat容器还需要依赖数据库，那就还得先启动数据库，docker-compose就可以解决这样的嵌套依赖关系，其完全可以替代docker run对容器进行创建、启动、和停止。

docker-compose项目是docker官方的开源项目，负责实现对docker容器集群的快速编排，docker-compose将所管理的容器分为三层，分别是工程（project），服务（service），以及容器（container）。

# 2、基础环境准备

- yum安装docker-compese

```
[root@docker-server1 ~]# yum install epel-release.noarch -y
[root@docker-server1 ~]# yum install docker-compose.noarch -y
[root@docker-server1 ~]# docker-compose version
docker-compose version 1.18.0, build 8dd22a9
docker-py version: 2.6.1
CPython version: 3.6.8
OpenSSL version: OpenSSL 1.0.2k-fips  26 Jan 2017
```

或者使用二进制安装，进入官网下载对应版本即可

官方下载网址：[https://github.com/docker/compose/releases](https://github.com/docker/compose/releases)

- 相关参数

```
docker-compose --help
Define and run multi-container applications with Docker.
Usage:
docker-compose [-f <arg>...] [options] [COMMAND] [ARGS...]
docker-compose -h|--help
#选项说明：
-f，–file FILE #指定Compose 模板文件，默认为docker-compose.yml。
-p，–project-name NAME #指定项目名称，默认将使用当前所在目录名称作为项目名。
--verbose  #显示更多输出信息
--log-level LEVEL   #定义日志级别 (DEBUG, INFO, WARNING, ERROR, CRITICAL)
--no-ansi #不显示ANSI 控制字符
-v, --version #显示版本

#以下为命令选项，需要在docker-compose.yml|yaml 文件所在在目录里执行
build  #构建镜像
bundle #从当前docker compose 文件生成一个以<当前目录>为名称的json格式的Docker
Bundle 备份文件
config  -q #查看当前配置，没有错误不输出任何信息
create #创建服务，较少使用
down #停止和删除所有容器、网络、镜像和卷
#events #从容器接收实时事件，可以指定json 日志格式，较少使用
exec #进入指定容器进行操作
help #显示帮助细信息
images #显示镜像信息，较少使用
kill #强制终止运行中的容器
logs #查看容器的日志
pause #暂停服务
port #查看端口
ps #列出容器，较少使用
pull #重新拉取镜像，镜像发生变化后，需要重新拉取镜像，较少使用
push #上传镜像
restart #重启服务，较少使用
rm #删除已经停止的服务
run #一次性运行容器
scale  #设置指定服务运行的容器个数
start #启动服务 ，较少使用
stop #停止服务，较少使用
top #显示容器运行状态
unpause #取消暂定
up #创建并启动容器 ，较少使用
```

# 3、启动单个容器

- 编写docker-compose文件

```
[root@docker-server1 ~]# mkdir /opt/docker
[root@docker-server1 ~]# cd /opt/docker/
[root@docker-server1 docker]# vim docker-compose.yml
service-nginx:
image: nginx
container_name: nginx_web1
ports:
  - "80:80"
```

- 启动容器

```
[root@docker-server1 docker]# docker-compose up -d
Creating docker_service-nginx_1 ... done
[root@docker-server1 docker]# docker-compose ps
    Name           Command      State       Ports
    
--------------------------------------------------------------------------------------
nginx_web1  /docker-entrypoint.sh    Up    0.0.0.0:80->80/tcp,:::80           ngin ...              >80/tcp 
```

# 4、启动多个容器

- 编辑docker-compose文件

```
[root@docker-server1 docker]# cat docker-compose.yml
service-nginx:
image: nginx
container_name: nginx_web1
ports:
     - "80:80"
service-tomcat:
image: tomcat
container_name: tomcat_web1
ports:
  - "8080:8080"
[root@docker-server1 docker]# docker-compose up -d
nginx_web1  /docker-entrypoint.sh ngin ...  Up    0.0.0.0:80->80/tcp,:::80->80/tcp               
tomcat_web1  catalina.sh run         Up    0.0.0.0:8080->8080/tcp,:::8080->8080/tcp
```

## 1）定义数据卷挂载

- 创建数据卷目录和文件

```
[root@docker-server1 docker]# mkdir -p /data/nginx
[root@docker-server1 docker]# echo 'docker nginx' > /data/nginx/index.html
```

- 编辑配置文件

```
[root@docker-server1 docker]# cat docker-compose.yml
service-nginx:
image: nginx
container_name: nginx_web1
volumes:
  - /data/nginx/:/usr/share/nginx/html
ports:
  - "80:80"
service-tomcat:
image: tomcat
container_name: tomcat_web1
ports:
  - "8080:8080"
```

- 访问测试

```
[root@docker-server1 docker]# curl localhost
docker nginx
```

## 2）相关操作演示

- 重启单个容器

```
[root@docker-server1 docker]# docker-compose restart service-nginx 
```

- 重启所有容器

```
[root@docker-server1 docker]# docker-compose restart 
```

- 停止单个容器

```
[root@docker-server1 docker]# docker-compose stop service-nginx 
```

- 启动单个容器

```
[root@docker-server1 docker]# docker-compose start service-nginx 
```

- 停止所有容器

```
[root@docker-server1 docker]# docker-compose stop 
```

- 启动所有容器

```
[root@docker-server1 docker]# docker-compose start 
```