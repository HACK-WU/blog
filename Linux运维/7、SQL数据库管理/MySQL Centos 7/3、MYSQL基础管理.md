**基础管理**

# 1、密码相关设置

## 1）管理员密码的设定

```
[root@localhost ~]# mysqladmin -uroot -p password 1
Enter password: <首次设置直接回车，第二次修改密码时此处输入旧密码>
```

## 2）管理员密码忘记

关闭数据库

```
[root@localhost ~]# systemctl stop mariadb.service
```

跳过授权登录

```
mysqld_safe --skip-grant-tables --skip-networking & 
```

登录mysql并修改root密码

```
[root@localhost ~]# mysql
MariaDB [(none)]> use mysql
MariaDB [mysql]> grant all on *.* to root@'localhost' identified by '777';
ERROR 1290 (HY000): The MariaDB server is running with the --skip-grant-
tables option so it cannot execute this statement
MariaDB [mysql]> flush privileges;
Query OK, 0 rows affected (0.00 sec)
MariaDB [mysql]> grant all on *.* to root@'localhost' identified by '777';
Query OK, 0 rows affected (0.00 sec)
```

重启数据库

```
[root@localhost ~]# pkill mysqld
[root@localhost ~]# systemctl restart mariadb
```

# 2、客户端工具mysql

```
mysql可以用来连接数据库
    --user , -u
    --host , -h
    --password ,-p
    --port --protocol
    --database DATABASE , -D 登录指定数据库
```

- mysqladmin(可以通过客户端直接执⾏服务器上的命令)

```
[root@localhost ~]# mysqladmin -uroot -p1 create hellodb
[root@localhost ~]# mysqladmin -uroot -p1 ping 检查服务端存活状态的
[root@localhost ~]# mysqladmin -uroot -p1 status 服务器详细运行状态
[root@localhost ~]# mysqladmin -uroot -p1 status 服务器状态 --sleep 2 --count
10 每两秒钟显示
⼀次服务器实时状态⼀共显示10次
[root@localhost ~]# mysqladmin -uroot -p1 extended-status 显示状态变量
[root@localhost ~]# mysqladmin -uroot -p1 variables 显示服务器变量
[root@localhost ~]# mysqladmin -uroot -p1 flush-privileges 数据库重读授权表，等
同于reload
[root@localhost ~]# mysqladmin -uroot -p1 flush-tables 关闭所有已经打开的表
[root@localhost ~]# mysqladmin -uroot -p1 flush-threds 重置线程池缓存
[root@localhost ~]# mysqladmin -uroot -p1 flush-status 重置⼤多数服务器状态变量
[root@localhost ~]# mysqladmin -uroot -p1 flush-logs ⽇志滚动。主要实现⼆进制和中
继⽇志滚动
[root@localhost ~]# mysqladmin -uroot -p1 flush-hosts 清楚主机内部信息
[root@localhost ~]# mysqladmin -uroot -p1 kill 杀死线程
[root@localhost ~]# mysqladmin -uroot -p1 refresh 相当于同时执⾏flush-hosts
flush-logs
[root@localhost ~]# mysqladmin -uroot -p1 shutdown 关闭服务器进程
[root@localhost ~]# mysqladmin -uroot -p1 version 服务器版本以及当前状态信息
[root@localhost ~]# mysqladmin -uroot -p1 start-slave 启动复制，启动从服务器复制
线程
[root@localhost ~]# mysqladmin -uroot -p1 stop-slave 关闭复制线程
```

- 在数据库的语句中可以加的命令（最常见是\G，将结果以竖排方式显示）

- \C：提前终⽌语句执⾏

- \d： 修改默认结束符

- \g：⽆论语句结束符是什么，直接将此语句送⾄服务器执⾏

- \G：⽆论语句结束符是什么，直接将结果送⾄服务端执⾏，⽽且结果以竖排⽅式显示

- !：执⾏shell命令

- \W：显示语句结束后显示警告信息

- \w：不显示警告信息 默认输出的格式是表的格式。登录数据库时可以添加参数 --html -H 或者

- --xml -X

```
MariaDB [(none)]> show databases;
+--------------------+
| Database      |
+--------------------+
| information_schema |
| mysql       |
| performance_schema |
+--------------------+
3 rows in set (0.00 sec)
MariaDB [(none)]> show databases\G
*************************** 1. row ***************************
Database: information_schema
*************************** 2. row ***************************
Database: mysql
*************************** 3. row ***************************
Database: performance_schema
3 rows in set (0.00 sec)
```

# 3、基础SQL语句

## 1)操作文件夹（库）

```
增：create database db1 charset utf8;
查：show databases;
改：alter database db1 charset latin1;
删除: drop database db1;
```

## 2)操作文件（表）

```
先切换到文件夹下：use db1
增：create table t1(id int,name char);
查：show tables;
改：alter table t1 modify name char(3);
alter table t1 change name name1 char(2);
删：drop table t1;
```

## 3）操作文件中的内容（记录）

```
增：insert into t1 values(1,'xwz'),(2,'cs'),(3,'tj');
查：select * from t1;
改：update t1 set name='sb' where id=2;
删：delete from t1 where id=1;
清空表：
delete from t1; #如果有自增id，新增的数据，仍然是以删除前的最后一样作为起始。
truncate table t1;数据量大，删除速度比上一条快，且直接从零开始，
```