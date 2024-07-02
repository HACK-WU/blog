**SQL语句**

# 1、用户管理

## 1)用户名形式，用户名@'白名单'

```
eagle@'192.168.80.%'
eagle@'%'
eagle@'192.168.80.1'
eagle@'localhost'
eagle@'server1'
eagle@'192.168.80.5%'
eagle@'192.168.80.0/255.255.254.0'
```

## 2）创建用户

```
MariaDB [(none)]> create user eagle@192.168.33.128 identified by '1';
Query OK, 0 rows affected (0
.00 sec)
```

## 3)查询用户

```
MariaDB [(none)]> select user,host,password from mysql.user;
+-------+----------------+-------------------------------------------+
| user | host      | password                 |
+-------+----------------+-------------------------------------------+
| root | localhost   | *E8D868B7DA46FC9F996DC761C1AE01754A4447D5 |
| root | 127.0.0.1   | *7FB1F1B8AD1B4CFD578E76ABC1B6ADFF70D04FA0 |
| root | ::1      | *7FB1F1B8AD1B4CFD578E76ABC1B6ADFF70D04FA0 |
| eagle | 192.168.33.128 | *E6CC90B878B948C35E92B003C792C46C58C4AF40 |
+-------+----------------+-------------------------------------------+
```

## 4)修改用户密码

```
MariaDB [(none)]> use mysql
MariaDB [mysql]> update user set password=password('2') where user='eagle'
and host='192.168.33.128';
Query OK, 1 row affected (0.00 sec)
Rows matched: 1 Changed: 1 Warnings: 0
```

## 5)删除用户

```
MariaDB [(none)]> drop user eagle@192.168.33.128;
Query OK, 0 rows affected (0.00 sec)
```

# 2、权限管理

## 1）授权

```
MariaDB [(none)]> grant all on *.* to eagle@'%' identified by '123'; 
```

## 2）常用权限介绍

```
ALL:
SELECT,INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, SHUTDOWN, PROCESS, FILE,
REFERENCES, INDEX, ALTER, SHOW DATABASES, SUPER, CREATE TEMPORARY TABLES,
LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW,
SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER, CREATE
TABLESPACE
ALL : 以上所有权限，一般是普通管理员拥有的
with grant option：超级管理员才具备的，给别的用户授权的功能
```

## 3）权限作用范围

```
*.*                         # 针对所有数据库里的所有表
db1.*                      # 针对db1数据库里的所有表
db1.tb1                     # 针对db1数据库里的表tb1
```

## 4）查看授权

```
MariaDB [(none)]> show grants for root@'localhost'\G;
*************************** 1. row ***************************
Grants for root@localhost: GRANT ALL PRIVILEGES ON *.* TO 'root'@'localhost'
IDENTIFIED BY PASSWORD '*E8D868B7DA46FC9F996DC761C1AE01754A4447D5' WITH GRANT
OPTION
*************************** 2. row ***************************
Grants for root@localhost: GRANT PROXY ON ''@'' TO 'root'@'localhost' WITH
GRANT OPTION
2 rows in set (0.00 sec)
```

## 5）回收权限

```
MariaDB [(none)]> revoke delete on *.* from eagle@'%'； 
```

- 案例1，在本地Windows主机上使用navicat登录到虚拟机里的MySQL，通常会由于没有授权而被拒绝

```
MariaDB [(none)]> grant all on *.* to root@'192.168.33.%' identified by
'777';
```

- 为开发人员开用户需要考虑哪些问题

- 需要哪些权限

- 针对哪些库、表

- 从什么地址进行连接

- 密码有什么要求

- 提示，在MySQL8.0中grant命令添加新特性

```
建用户和授权分开了
grant 不再支持自动创建用户了，不支持改密码
授权之前，必须要提前创建用户。
```

# 3、表管理

## 1）创建表的语法

```
#语法：
create table 表名(
字段名1 类型[(宽度) 约束条件],
字段名2 类型[(宽度) 约束条件],
字段名3 类型[(宽度) 约束条件]
);
#注意：
1. 在同一张表中，字段名是不能相同
2. 宽度和约束条件可选
3. 字段名和类型是必须的
```

## 2）建表

```
mysql> create table staff_info (
id int,name varchar(50),
age int(3),
sex enum('male','female'),
phone bigint(11),
job varchar(11)
);
Query OK, 0 rows affected (0.02 sec)
```

## 4）插入数据

```
mysql> insert into staff_info (id,name,age,sex,phone,job) values
(1,'xwz',83,'female',13651054608,'IT');
Query OK, 1 row affected (0.00 sec)
mysql> insert into staff_info values
(2,'cs',26,'male',13304320533,'Teacher');
Query OK, 1 row affected (0.00 sec)
mysql> insert into staff_info values (3,'tj',25,'male',13332353222,'IT'),
(4,'fcy',40,'male',13332353333,'IT');
Query OK, 2 rows affected (0.00 sec)
```

## 5）查看表结构

```
mysql> describe staff_info;
+-------+-----------------------+------+-----+---------+-------+
| Field | Type | Null | Key | Default | Extra |
+-------+-----------------------+------+-----+---------+-------+
| id | int(11) | YES | | NULL | |
| name | varchar(50) | YES | | NULL | |
| age | int(3) | YES | | NULL | |
| sex | enum('male','female') | YES | | NULL | |
| phone | bigint(11) | YES | | NULL | |
| job | varchar(11) | YES | | NULL | |
+-------+-----------------------+------+-----+---------+-------+
6 rows in set (0.00 sec)
# 更全面的表定义信息
mysql> show create table staff_info\G;
*************************** 1. row ***************************
Table: staff_info
Create Table: CREATE TABLE `staff_info` (
`id` int(11) DEFAULT NULL,
`name` varchar(50) DEFAULT NULL,
`age` int(3) DEFAULT NULL,
`sex` enum('male','female') DEFAULT NULL,
`phone` bigint(11) DEFAULT NULL,
`job` varchar(11) DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8
1 row in set (0.01 sec)
```

## 6）修改表结构的语法

```
1. 修改表名
ALTER TABLE 表名
RENAME 新表名;
2. 增加字段
ALTER TABLE 表名
ADD 字段名 数据类型 [完整性约束条件…],
ADD 字段名 数据类型 [完整性约束条件…];
3. 删除字段
ALTER TABLE 表名
DROP 字段名;
4. 修改字段
ALTER TABLE 表名
MODIFY 字段名 数据类型 [完整性约束条件…];
ALTER TABLE 表名
CHANGE 旧字段名 新字段名 旧数据类型 [完整性约束条件…];
ALTER TABLE 表名
CHANGE 旧字段名 新字段名 新数据类型 [完整性约束条件…];
5.修改字段排列顺序/在增加的时候指定字段位置
ALTER TABLE 表名
ADD 字段名 数据类型 [完整性约束条件…] FIRST;
ALTER TABLE 表名
ADD 字段名 数据类型 [完整性约束条件…] AFTER 字段名;
ALTER TABLE 表名
CHANGE 字段名 旧字段名 新字段名 新数据类型 [完整性约束条件…]
```

## 7）修改表的各个实例

```
mysql> desc staff_info;
+-------+-----------------------+------+-----+---------+-------+
| Field | Type | Null | Key | Default | Extra |
+-------+-----------------------+------+-----+---------+-------+
| id | int(11) | YES | | NULL | |
| name | varchar(50) | YES | | NULL | |
| age | int(3) | YES | | NULL | |
| sex | enum('male','female') | YES | | NULL | |
| phone | bigint(11) | YES | | NULL | |
| job | varchar(11) | YES | | NULL | |
+-------+-----------------------+------+-----+---------+-------+
6 rows in set (0.00 sec)
# 表重命名
mysql> alter table staff_info rename staff;
Query OK, 0 rows affected (0.00 sec)
mysql> desc staff;
+-------+-----------------------+------+-----+---------+-------+
| Field | Type | Null | Key | Default | Extra |
+-------+-----------------------+------+-----+---------+-------+
| id | int(11) | YES | | NULL | |
| name | varchar(50) | YES | | NULL | |
| age | int(3) | YES | | NULL | |
| sex | enum('male','female') | YES | | NULL | |
| phone | bigint(11) | YES | | NULL | |
| job | varchar(11) | YES | | NULL | |
+-------+-----------------------+------+-----+---------+-------+
6 rows in set (0.00 sec)
# 删除sex列
mysql> alter table staff drop sex;
Query OK, 0 rows affected (0.02 sec)
Records: 0 Duplicates: 0 Warnings: 0
mysql> desc staff;
+-------+-------------+------+-----+---------+-------+
| Field | Type | Null | Key | Default | Extra |
+-------+-------------+------+-----+---------+-------+
| id | int(11) | YES | | NULL | |
| name | varchar(50) | YES | | NULL | |
| age | int(3) | YES | | NULL | |
| phone | bigint(11) | YES | | NULL | |
| job | varchar(11) | YES | | NULL | |
+-------+-------------+------+-----+---------+-------+
5 rows in set (0.01 sec)
# 添加列
mysql> alter table staff add sex enum('male','female');
Query OK, 0 rows affected (0.03 sec)
Records: 0 Duplicates: 0 Warnings: 0
# 修改id的宽度
mysql> alter table staff modify id int(4);
Query OK, 0 rows affected (0.02 sec)
Records: 0 Duplicates: 0 Warnings: 0
mysql> desc staff;
+-------+-----------------------+------+-----+---------+-------+
| Field | Type | Null | Key | Default | Extra |
+-------+-----------------------+------+-----+---------+-------+
| id | int(4) | YES | | NULL | |
| name | varchar(50) | YES | | NULL | |
| age | int(3) | YES | | NULL | |
| phone | bigint(11) | YES | | NULL | |
| job | varchar(11) | YES | | NULL | |
| sex | enum('male','female') | YES | | NULL | |
+-------+-----------------------+------+-----+---------+-------+
6 rows in set (0.01 sec)
# 修改name列的字段名
mysql> alter table staff change name sname varchar(20);
Query OK, 4 rows affected (0.03 sec)
Records: 4 Duplicates: 0 Warnings: 0
mysql> desc staff;
+-------+-----------------------+------+-----+---------+-------+
| Field | Type | Null | Key | Default | Extra |
+-------+-----------------------+------+-----+---------+-------+
| id | int(4) | YES | | NULL | |
| sname | varchar(20) | YES | | NULL | |
| age | int(3) | YES | | NULL | |
| phone | bigint(11) | YES | | NULL | |
| job | varchar(11) | YES | | NULL | |
| sex | enum('male','female') | YES | | NULL | |
+-------+-----------------------+------+-----+---------+-------+
6 rows in set (0.00 sec)
# 修改sex列的位置
mysql> alter table staff modify sex enum('male','female') after sname;
Query OK, 0 rows affected (0.02 sec)
Records: 0 Duplicates: 0 Warnings: 0
mysql> desc staff;
+-------+-----------------------+------+-----+---------+-------+
| Field | Type | Null | Key | Default | Extra |
+-------+-----------------------+------+-----+---------+-------+
| id | int(4) | YES | | NULL | |
| sname | varchar(20) | YES | | NULL | |
| sex | enum('male','female') | YES | | NULL | |
| age | int(3) | YES | | NULL | |
| phone | bigint(11) | YES | | NULL | |
| job | varchar(11) | YES | | NULL | |
+-------+-----------------------+------+-----+---------+-------+
6 rows in set (0.00 sec)
# 创建自增id主键
mysql> alter table staff modify id int(4) primary key auto_increment;
Query OK, 4 rows affected (0.02 sec)
Records: 4 Duplicates: 0 Warnings: 0
mysql> desc staff;
+-------+-----------------------+------+-----+---------+----------------+
| Field | Type | Null | Key | Default | Extra |
+-------+-----------------------+------+-----+---------+----------------+
| id | int(4) | NO | PRI | NULL | auto_increment |
| sname | varchar(20) | YES | | NULL | |
| sex | enum('male','female') | YES | | NULL | |
| age | int(3) | YES | | NULL | |
| phone | bigint(11) | YES | | NULL | |
| job | varchar(11) | YES | | NULL | |
+-------+-----------------------+------+-----+---------+----------------+
6 rows in set (0.00 sec)
# 删除主键，可以看到删除一个自增主键会报错
mysql> alter table staff drop primary key;
ERROR 1075 (42000): Incorrect table definition; there can be only one auto
column and it
must be defined as a key
# 需要先去掉主键的自增约束，然后再删除主键约束
mysql> alter table staff modify id int(11);
Query OK, 4 rows affected (0.02 sec)
Records: 4 Duplicates: 0 Warnings: 0
mysql> desc staff;
+-------+-----------------------+------+-----+---------+-------+
| Field | Type | Null | Key | Default | Extra |
+-------+-----------------------+------+-----+---------+-------+
| id | int(11) | NO | PRI | 0 | |
| sname | varchar(20) | YES | | NULL | |
| sex | enum('male','female') | YES | | NULL | |
| age | int(3) | YES | | NULL | |
| phone | bigint(11) | YES | | NULL | |
| job | varchar(11) | YES | | NULL | |
+-------+-----------------------+------+-----+---------+-------+
6 rows in set (0.01 sec)
mysql> alter table staff drop primary key;
Query OK, 4 rows affected (0.06 sec)
Records: 4 Duplicates: 0 Warnings: 0
# 添加联合主键
mysql> alter table staff add primary key (sname,age);
Query OK, 0 rows affected (0.02 sec)
Records: 0 Duplicates: 0 Warnings: 0
# 删除主键
mysql> alter table staff drop primary key;
Query OK, 4 rows affected (0.02 sec)
Records: 4 Duplicates: 0 Warnings: 0
# 创建主键id
mysql> alter table staff add primary key (id);
Query OK, 0 rows affected (0.02 sec)
Records: 0 Duplicates: 0 Warnings: 0
mysql> desc staff;
+-------+-----------------------+------+-----+---------+-------+
| Field | Type | Null | Key | Default | Extra |
+-------+-----------------------+------+-----+---------+-------+
| id | int(11) | NO | PRI | 0 | |
| sname | varchar(20) | NO | | | |
| sex | enum('male','female') | YES | | NULL | |
| age | int(3) | NO | | 0 | |
| phone | bigint(11) | YES | | NULL | |
| job | varchar(11) | YES | | NULL | |
+-------+-----------------------+------+-----+---------+-------+
6 rows in set (0.00 sec)
# 为主键添加自增属性
mysql> alter table staff modify id int(4) auto_increment;
Query OK, 4 rows affected (0.02 sec)
Records: 4 Duplicates: 0 Warnings: 0
mysql> desc staff;
+-------+-----------------------+------+-----+---------+----------------+
| Field | Type | Null | Key | Default | Extra |
+-------+-----------------------+------+-----+---------+----------------+
| id | int(4) | NO | PRI | NULL | auto_increment |
| sname | varchar(20) | NO | | | |
| sex | enum('male','female') | YES | | NULL | |
| age | int(3) | NO | | 0 | |
| phone | bigint(11) | YES | | NULL | |
| job | varchar(11) | YES | | NULL | |
+-------+-----------------------+------+-----+---------+----------------+
6 rows in set (0.00 sec)
```

## 8）删除表语法

```
DROP TABLE 表名; 
```

# 4、表的约束规则

- 防止不规范的数据存放在数据库中，DBMS自动按照一定的约束条件对数据进行检测，确保数据库中存储的数据正 确有效

- 分类：

- NOT NULL：非空约束

- Default：当表中某一列有重复内容，避免频繁操作，可以为其设置默认值

- UNIQUE：唯一约束

- PRIMARY KEY:主键，唯一标识一条记录

- FOREIGN KEY:外键，从属于主表的一条记录

## 1）NOT NULL示例

```
mysql> create table t12 (id int not null);
Query OK, 0 rows affected (0.02 sec)
mysql> select * from t12;
Empty set (0.00 sec)
mysql> desc t12;
+-------+---------+------+-----+---------+-------+
| Field | Type | Null | Key | Default | Extra |
+-------+---------+------+-----+---------+-------+
| id | int(11) | NO | | NULL | |
+-------+---------+------+-----+---------+-------+
row in set (0.00 sec)
#不能向id列插入空元素。
mysql> insert into t12 values (null);
ERROR 1048 (23000): Column 'id' cannot be null
mysql> insert into t12 values (1);
Query OK, 1 row affected (0.01 sec)
```

## 2）NOT NULL + Default示例

```
mysql> create table t13 (id1 int not null,id2 int not null default 222);
Query OK, 0 rows affected (0.01 sec)
mysql> desc t13;
+-------+---------+------+-----+---------+-------+
| Field | Type | Null | Key | Default | Extra |
+-------+---------+------+-----+---------+-------+
| id1 | int(11) | NO | | NULL | |
| id2 | int(11) | NO | | 222 | |
+-------+---------+------+-----+---------+-------+
rows in set (0.01 sec)
# 只向id1字段添加值，会发现id2字段会使用默认值填充
mysql> insert into t13 (id1) values (111);
Query OK, 1 row affected (0.00 sec)
mysql> select * from t13;
+-----+-----+
| id1 | id2 |
+-----+-----+
| 111 | 222 |
+-----+-----+
row in set (0.00 sec)
# id1字段不能为空，所以不能单独向id2字段填充值；
mysql> insert into t13 (id2) values (223);
ERROR:......
v
# 向id1，id2中分别填充数据，id2的填充数据会覆盖默认值
mysql> insert into t13 (id1,id2) values (112,223);
Query OK, 1 row affected (0.00 sec)
mysql> select * from t13;
+-----+-----+
| id1 | id2 |
+-----+-----+
| 111 | 222 |
| 112 | 223 |
+-----+-----+
rows in set (0.00 sec)
```

## 3）UNIQUE

```
方法一：
create table department1(
id int,
name varchar(20) unique,
comment varchar(100)
);
方法二：
create table department2(
id int,
name varchar(20),
comment varchar(100),
unique(name)
);
mysql> insert into department1 values(1,'IT','技术');
Query OK, 1 row affected (0.00 sec)
mysql> insert into department1 values(1,'IT','技术');
ERROR 1062 (23000): Duplicate entry 'IT' for key 'name'
```

## 4）not null和unique结合

```
mysql> create table t1(id int not null unique);
Query OK, 0 rows affected (0.02 sec)
mysql> desc t1;
+-------+---------+------+-----+---------+-------+
| Field | Type | Null | Key | Default | Extra |
+-------+---------+------+-----+---------+-------+
| id | int(11) | NO | PRI | NULL | |
+-------+---------+------+-----+---------+-------+
row in set (0.00 sec)
```

## 5）联合唯一

```
create table service(
id int primary key auto_increment,
name varchar(20),
host varchar(15) not null,
port int not null,
unique(host,port) #联合唯一
);
mysql> insert into service values
-> (1,'nginx','192.168.0.10',80),
-> (2,'haproxy','192.168.0.20',80),
-> (3,'mysql','192.168.0.30',3306)
-> ;
Query OK, 3 rows affected (0.01 sec)
Records: 3 Duplicates: 0 Warnings: 0
mysql> insert into service(name,host,port)
values('nginx','192.168.0.10',80);
ERROR 1062 (23000): Duplicate entry '192.168.0.10-80' for key 'host'
```

## 6）PRIMARY KEY

- 主键为了保证表中的每一条数据的该字段都是表格中的唯一值

- 主键必须唯一，主键值非空；

- 可以是单一字段，也可以是多字段组合 单字段主键：

```
============单列做主键===============
#方法一：not null+unique
create table department1(
id int not null unique, #主键
name varchar(20) not null unique,
comment varchar(100)
);
mysql> desc department1;
+---------+--------------+------+-----+---------+-------+
| Field | Type | Null | Key | Default | Extra |
+---------+--------------+------+-----+---------+-------+
| id | int(11) | NO | PRI | NULL | |
| name | varchar(20) | NO | UNI | NULL | |
| comment | varchar(100) | YES | | NULL | |
+---------+--------------+------+-----+---------+-------+
rows in set (0.01 sec)
#方法二：在某一个字段后用primary key
create table department2(
id int primary key, #主键
name varchar(20),
comment varchar(100)
);
mysql> desc department2;
+---------+--------------+------+-----+---------+-------+
| Field | Type | Null | Key | Default | Extra |
+---------+--------------+------+-----+---------+-------+
| id | int(11) | NO | PRI | NULL | |
| name | varchar(20) | YES | | NULL | |
| comment | varchar(100) | YES | | NULL | |
+---------+--------------+------+-----+---------+-------+
rows in set (0.00 sec)
#方法三：在所有字段后单独定义primary key
create table department3(
id int,
name varchar(20),
comment varchar(100),
primary key(id); #创建主键并为其命名pk_name
mysql> desc department3;
+---------+--------------+------+-----+---------+-------+
| Field | Type | Null | Key | Default | Extra |
+---------+--------------+------+-----+---------+-------+
| id | int(11) | NO | PRI | NULL | |
| name | varchar(20) | YES | | NULL | |
| comment | varchar(100) | YES | | NULL | |
+---------+--------------+------+-----+---------+-------+
rows in set (0.01 sec)
# 方法四：给已经建成的表添加主键约束
mysql> create table department4(
-> id int,
-> name varchar(20),
-> comment varchar(100));
Query OK, 0 rows affected (0.01 sec)
mysql> desc department4;
+---------+--------------+------+-----+---------+-------+
| Field | Type | Null | Key | Default | Extra |
+---------+--------------+------+-----+---------+-------+
| id | int(11) | YES | | NULL | |
| name | varchar(20) | YES | | NULL | |
| comment | varchar(100) | YES | | NULL | |
+---------+--------------+------+-----+---------+-------+
rows in set (0.01 sec)
mysql> alter table department4 modify id int primary key;
Query OK, 0 rows affected (0.02 sec)
Records: 0 Duplicates: 0 Warnings: 0
mysql> desc department4;
+---------+--------------+------+-----+---------+-------+
| Field | Type | Null | Key | Default | Extra |
+---------+--------------+------+-----+---------+-------+
| id | int(11) | NO | PRI | NULL | |
| name | varchar(20) | YES | | NULL | |
| comment | varchar(100) | YES | | NULL | |
+---------+--------------+------+-----+---------+-------+
rows in set (0.01 sec)
```

## 7）ATUO_INCREMENT

```
#不指定id，则自动增长
create table student(
id int primary key auto_increment,
name varchar(20),
sex enum('male','female') default 'male'
);
mysql> desc student;
+-------+-----------------------+------+-----+---------+----------------+
| Field | Type | Null | Key | Default | Extra |
+-------+-----------------------+------+-----+---------+----------------+
| id | int(11) | NO | PRI | NULL | auto_increment |
| name | varchar(20) | YES | | NULL | |
| sex | enum('male','female') | YES | | male | |
+-------+-----------------------+------+-----+---------+----------------+
mysql> insert into student(name) values
-> ('egon'),
-> ('alex')
-> ;
mysql> select * from student;
+----+------+------+
| id | name | sex |
+----+------+------+
| 1 | egon | male |
| 2 | alex | male |
+----+------+------+
#也可以指定id
mysql> insert into student values(4,'asb','female');
Query OK, 1 row affected (0.00 sec)
mysql> insert into student values(7,'wsb','female');
Query OK, 1 row affected (0.00 sec)
mysql> select * from student;
+----+------+--------+
| id | name | sex |
+----+------+--------+
| 1 | egon | male |
| 2 | alex | male |
| 4 | asb | female |
| 7 | wsb | female |
+----+------+--------+
#对于自增的字段，在用delete删除后，再插入值，该字段仍按照删除前的位置继续增长
mysql> delete from student;
Query OK, 4 rows affected (0.00 sec)
mysql> select * from student;
Empty set (0.00 sec)
mysql> insert into student(name) values('ysb');
mysql> select * from student;
+----+------+------+
| id | name | sex |
+----+------+------+
| 8 | ysb | male |
+----+------+------+
#应该用truncate清空表，比起delete一条一条地删除记录，truncate是直接清空表，在删除大表时
用它
mysql> truncate student;
Query OK, 0 rows affected (0.01 sec)
mysql> insert into student(name) values('egon');
Query OK, 1 row affected (0.01 sec)
mysql> select * from student;
+----+------+------+
| id | name | sex |
+----+------+------+
| 1 | egon | male |
+----+------+------+
row in set (0.00 sec)
```

## 8）increment和offset

```
#在创建完表后，修改自增字段的起始值
mysql> create table student(
-> id int primary key auto_increment,
-> name varchar(20),
-> sex enum('male','female') default 'male'
-> );
mysql> alter table student auto_increment=3;
mysql> show create table student;
.......
ENGINE=InnoDB AUTO_INCREMENT=3 DEFAULT CHARSET=utf8
mysql> insert into student(name) values('egon');
Query OK, 1 row affected (0.01 sec)
mysql> select * from student;
+----+------+------+
| id | name | sex |
+----+------+------+
| 3 | egon | male |
+----+------+------+
row in set (0.00 sec)
mysql> show create table student;
.......
ENGINE=InnoDB AUTO_INCREMENT=4 DEFAULT CHARSET=utf8
#也可以创建表时指定auto_increment的初始值，注意初始值的设置为表选项，应该放到括号外
create table student(
id int primary key auto_increment,
name varchar(20),
sex enum('male','female') default 'male'
)auto_increment=3;
#设置步长
sqlserver：自增步长
基于表级别
create table t1（
id int。。。
）engine=innodb,auto_increment=2 步长=2 default charset=utf8
mysql自增的步长：
show session variables like 'auto_inc%';
#基于会话级别
set session auth_increment_increment=2 #修改会话级别的步长
#基于全局级别的
set global auth_increment_increment=2 #修改全局级别的步长（所有会话都生效）
#！！！注意了注意了注意了！！！
If the value of auto_increment_offset is greater than that of
auto_increment_increment, the
value of auto_increment_offset is ignored.
翻译：如果auto_increment_offset的值大于auto_increment_increment的值，则
auto_increment_offset的
值会被忽略 ，这相当于第一步步子就迈大了，扯着了蛋
比如：设置auto_increment_offset=3，auto_increment_increment=2
mysql> set global auto_increment_increment=5;
Query OK, 0 rows affected (0.00 sec)
mysql> set global auto_increment_offset=3;
Query OK, 0 rows affected (0.00 sec)
mysql> show variables like 'auto_incre%'; #需要退出重新登录
+--------------------------+-------+
| Variable_name | Value |
+--------------------------+-------+
| auto_increment_increment | 1 |
| auto_increment_offset | 1 |
+--------------------------+-------+
create table student(
id int primary key auto_increment,
name varchar(20),
sex enum('male','female') default 'male'
);
mysql> insert into student(name) values('egon1'),('egon2'),('egon3');
mysql> select * from student;
+----+-------+------+
| id | name | sex |
+----+-------+------+
| 3 | egon1 | male |
| 8 | egon2 | male |
| 13 | egon3 | male |
+----+-------+------+
步长:auto_increment_increment,起始偏移量:auto_increment_offset
```

## 9）FOREGIN KEY

- 多表

- 示例：员工信息表中的部分字段，重复并且浪费资源

```
mysql> create table departments (dep_id int(4),dep_name varchar(11));
Query OK, 0 rows affected (0.02 sec)
mysql> desc departments;
+----------+-------------+------+-----+---------+-------+
| Field | Type | Null | Key | Default | Extra |
+----------+-------------+------+-----+---------+-------+
| dep_id | int(4) | YES | | NULL | |
| dep_name | varchar(11) | YES | | NULL | |
+----------+-------------+------+-----+---------+-------+
rows in set (0.00 sec)
# 创建外键不成功
mysql> create table staff_info (s_id int,name varchar(20),dep_id int,foreign
key(dep_id)
references departments(dep_id));
ERROR 1215 (HY000): Cannot add foreign key
# 设置dep_id非空，仍然不能成功创建外键
mysql> alter table departments modify dep_id int(4) not null;
Query OK, 0 rows affected (0.02 sec)
Records: 0 Duplicates: 0 Warnings: 0
mysql> desc departments;
+----------+-------------+------+-----+---------+-------+
| Field | Type | Null | Key | Default | Extra |
+----------+-------------+------+-----+---------+-------+
| dep_id | int(4) | NO | | NULL | |
| dep_name | varchar(11) | YES | | NULL | |
+----------+-------------+------+-----+---------+-------+
rows in set (0.00 sec)
mysql> create table staff_info (s_id int,name varchar(20),dep_id int,foreign
key(dep_id)
references departments(dep_id));
ERROR 1215 (HY000): Cannot add foreign key constraint
# 当设置字段为unique唯一字段时，设置该字段为外键成功
mysql> alter table departments modify dep_id int(4) unique;
Query OK, 0 rows affected (0.01 sec)
Records: 0 Duplicates: 0 Warnings: 0
mysql> desc departments;
+----------+-------------+------+-----+---------+------+
| Field | Type | Null | Key | Default | Extra |
+----------+-------------+------+-----+---------+-------+
| dep_id | int(4) | YES | UNI | NULL | |
| dep_name | varchar(11) | YES | | NULL | |
+----------+-------------+------+-----+---------+-------+
rows in set (0.01 sec)
mysql> create table staff_info (s_id int,name varchar(20),dep_id int,foreign
key(dep_id)
references departments(dep_id));
Query OK, 0 rows affected (0.02 sec)
```

## 10）综合示例

```
#表类型必须是innodb存储引擎，且被关联的字段，即references指定的另外一个表的字段，必须保
证唯一
create table department(
id int primary key,
name varchar(20) not null
)engine=innodb;
#dpt_id外键，关联父表（department主键id），同步更新，同步删除
create table employee(
id int primary key,
name varchar(20) not null,
dpt_id int,
foreign key(dpt_id)
references department(id)
on delete cascade # 级连删除
on update cascade # 级连更新
)engine=innodb;
#先往父表department中插入记录
insert into department values
(1,'教质部'),
(2,'技术部'),
(3,'人力资源部');
#再往子表employee中插入记录
insert into employee values
(1,'yuan',1),
(2,'nezha',2),
(3,'egon',2),
(4,'alex',2),
(5,'wusir',3),
(6,'李沁洋',3),
(7,'皮卡丘',3),
(8,'程咬金',3),
(9,'程咬银',3)
;
#删父表department，子表employee中对应的记录跟着删
mysql> delete from department where id=2;
Query OK, 1 row affected (0.00 sec)
mysql> select * from employee;
+----+-----------+--------+
| id | name | dpt_id |
+----+-----------+--------+
| 1 | yuan | 1 |
| 5 | wusir | 3 |
| 6 | 李沁洋 | 3 |
| 7 | 皮卡丘 | 3 |
| 8 | 程咬金 | 3 |
| 9 | 程咬银 | 3 |
+----+-----------+--------+
rows in set (0.00 sec)
#更新父表department，子表employee中对应的记录跟着改
mysql> update department set id=2 where id=3;
Query OK, 1 row affected (0.01 sec)
Rows matched: 1 Changed: 1 Warnings: 0
mysql> select * from employee;
+----+-----------+--------+
| id | name | dpt_id |
+----+-----------+--------+
| 1 | yuan | 1 |
| 5 | wusir | 2 |
| 6 | 李沁洋 | 2 |
| 7 | 皮卡丘 | 2 |
| 8 | 程咬金 | 2 |
| 9 | 程咬银 | 2 |
+----+-----------+--------+
rows in set (0.00 sec)
```

# 5、记录操作

## 1）插入数据

```
1. 插入完整数据（顺序插入）
语法一：
INSERT INTO 表名(字段1,字段2,字段3…字段n) VALUES(值1,值2,值3…值n);
语法二：
INSERT INTO 表名 VALUES (值1,值2,值3…值n);
2. 指定字段插入数据
语法：
INSERT INTO 表名(字段1,字段2,字段3…) VALUES (值1,值2,值3…);
3. 插入多条记录
语法：
INSERT INTO 表名 VALUES
(值1,值2,值3…值n),
(值1,值2,值3…值n),
(值1,值2,值3…值n);
4. 插入查询结果
语法：
INSERT INTO 表名(字段1,字段2,字段3…字段n)
SELECT (字段1,字段2,字段3…字段n) FROM 表2
WHERE …;
```

## 2）更新数据

```
语法：
UPDATE 表名 SET
字段1=值1,
字段2=值2,
WHERE CONDITION;
示例：
UPDATE mysql.user SET password=password(‘123’)
where user=’root’ and host=’localhost’;
```

## 3）删除数据

```
语法：
DELETE FROM 表名
WHERE CONITION;
示例：
DELETE FROM mysql.user
WHERE password=’’;
```

# 6、查询数据

## 1）语法

```
SELECT DISTINCT 字段1,字段2... FROM 表名
WHERE 条件
GROUP BY field
HAVING 筛选
ORDER BY field
LIMIT 限制条数
```

## 2）优先级

```
from
where
group by
select
distinct
having
order by
limit
1.找到表:from
2.拿着where指定的约束条件，去文件/表中取出一条条记录
3.将取出的一条条记录进行分组group by，如果没有group by，则整体作为一组
4.执行select（去重）
5.将分组的结果进行having过滤
6.将结果按条件排序：order by
7.限制结果的显示条数
```

## 3）简单查询

```
company.employee
员工id id int
姓名 emp_name varchar
性别 sex enum
年龄 age int
入职日期 hire_date date
岗位 post varchar
职位描述 post_comment varchar
薪水 salary double
办公室 office int
部门编号 depart_id int
#建表
create table employee(
id int not null unique auto_increment,
emp_name varchar(20) not null,
sex enum('male','female') not null default 'male',
age int(3) unsigned not null default 28,
hire_date date not null,
post varchar(50),
post_comment varchar(100),
salary double(15,2),
office int,
depart_id int
);
#插入数据
insert into
employee(emp_name,sex,age,hire_date,post,salary,office,depart_id) values
('dapeng','male',18,'20170301','boss',7300.33,401,1),
('alex','male',78,'20150302','teacher',1000000.31,401,1),
('wupeiqi','male',81,'20130305','teacher',8300,401,1),
('yuanhao','male',73,'20140701','teacher',3500,401,1),
('liwenzhou','male',28,'20121101','teacher',2100,401,1),
('jingliyang','female',18,'20110211','teacher',9000,401,1),
('jinxin','male',18,'19000301','teacher',30000,401,1),
('成龙','male',48,'20101111','teacher',10000,401,1),
('歪歪','female',48,'20150311','sale',3000.13,402,2),
('丫丫','female',38,'20101101','sale',2000.35,402,2),
('丁丁','female',18,'20110312','sale',1000.37,402,2),
('星星','female',18,'20160513','sale',3000.29,402,2),
('格格','female',28,'20170127','sale',4000.33,402,2),
('张野','male',28,'20160311','operation',10000.13,403,3),
('程咬金','male',18,'19970312','operation',20000,403,3),
('程咬银','female',18,'20130311','operation',19000,403,3),
('程咬铜','male',18,'20150411','operation',18000,403,3),
('程咬铁','female',18,'20140512','operation',17000,403,3);
#简单查询
SELECT * FROM employee;
SELECT emp_name,salary FROM employee;
#避免重复
SELECT DISTINCT post FROM employee;
#四则运算查询
SELECT emp_name, salary*12 AS Annual_salary FROM employee;
```

## 4）简单查询

```
company.employee
员工id id int
姓名 emp_name varchar
性别 sex enum
年龄 age int
入职日期 hire_date date
岗位 post varchar
职位描述 post_comment varchar
薪水 salary double
办公室 office int
部门编号 depart_id int
#建表
create table employee(
id int not null unique auto_increment,
emp_name varchar(20) not null,
sex enum('male','female') not null default 'male',
age int(3) unsigned not null default 28,
hire_date date not null,
post varchar(50),
post_comment varchar(100),
salary double(15,2),
office int,
depart_id int
);
#插入数据
insert into
employee(emp_name,sex,age,hire_date,post,salary,office,depart_id) values
('dapeng','male',18,'20170301','boss',7300.33,401,1),
('alex','male',78,'20150302','teacher',1000000.31,401,1),
('wupeiqi','male',81,'20130305','teacher',8300,401,1),
('yuanhao','male',73,'20140701','teacher',3500,401,1),
('liwenzhou','male',28,'20121101','teacher',2100,401,1),
('jingliyang','female',18,'20110211','teacher',9000,401,1),
('jinxin','male',18,'19000301','teacher',30000,401,1),
('成龙','male',48,'20101111','teacher',10000,401,1),
('歪歪','female',48,'20150311','sale',3000.13,402,2),
('丫丫','female',38,'20101101','sale',2000.35,402,2),
('丁丁','female',18,'20110312','sale',1000.37,402,2),
('星星','female',18,'20160513','sale',3000.29,402,2),
('格格','female',28,'20170127','sale',4000.33,402,2),
('张野','male',28,'20160311','operation',10000.13,403,3),
('程咬金','male',18,'19970312','operation',20000,403,3),
('程咬银','female',18,'20130311','operation',19000,403,3),
('程咬铜','male',18,'20150411','operation',18000,403,3),
('程咬铁','female',18,'20140512','operation',17000,403,3);
#简单查询
SELECT * FROM employee;
SELECT emp_name,salary FROM employee;
#避免重复
SELECT DISTINCT post FROM employee;
#四则运算查询
SELECT emp_name, salary*12 AS Annual_salary FROM employee;
```

## 5）where约束

```
1. 比较运算符：> < >= <= <> !=
2. between 80 and 100 值在80到100之间
3. in(80,90,100) 值是80或90或100
4. like 'e%'
通配符可以是%或_，
%表示任意多字符
_表示一个字符
5. 逻辑运算符：在多个条件直接可以使用逻辑运算符 and or not
#1:单条件查询
SELECT emp_name FROM employee
WHERE post='sale';
#2:多条件查询
SELECT emp_name,salary FROM employee
WHERE post='teacher' AND salary>10000;
#3:关键字BETWEEN AND
SELECT emp_name,salary FROM employee
WHERE salary BETWEEN 10000 AND 20000;
SELECT emp_name,salary FROM employee
WHERE salary NOT BETWEEN 10000 AND 20000;
#4:关键字IS NULL(判断某个字段是否为NULL不能用等号，需要用IS)
SELECT emp_name,post_comment FROM employee
WHERE post_comment IS NULL;
SELECT emp_name,post_comment FROM employee
WHERE post_comment IS NOT NULL;
SELECT emp_name,post_comment FROM employee
WHERE post_comment=''; 注意''是空字符串，不是null
ps：
执行
update employee set post_comment='' where id=2;
再用上条查看，就会有结果了
#5:关键字IN集合查询
SELECT emp_name,salary FROM employee
WHERE salary=3000 OR salary=3500 OR salary=4000 OR salary=9000 ;
SELECT emp_name,salary FROM employee
WHERE salary IN (3000,3500,4000,9000) ;
SELECT emp_name,salary FROM employee
WHERE salary NOT IN (3000,3500,4000,9000) ;
#6:关键字LIKE模糊查询
通配符’%’
SELECT * FROM employee
WHERE emp_name LIKE 'eg%';
通配符’_’
SELECT * FROM employee
WHERE emp_name LIKE 'al__';
```

## 6）group by

```
单独使用GROUP BY关键字分组
SELECT post FROM employee GROUP BY post;
注意：我们按照post字段分组，那么select查询的字段只能是post，想要获取组内的其他相关信息，
需要借
助函数
GROUP BY关键字和GROUP_CONCAT()函数一起使用
SELECT post,GROUP_CONCAT(emp_name) FROM employee GROUP BY post;#按照岗位分组，
并查看组内成
员名
SELECT post,GROUP_CONCAT(emp_name) as emp_members FROM employee GROUP BY
post;
GROUP BY与聚合函数一起使用
select post,count(id) as count from employee group by post;#按照岗位分组，并查
看每个组有多
少人
PS:
1.如果我们用unique的字段作为分组的依据，则每一条记录自成一组，这种分组没有意义
2.多条记录之间的某个字段值相同，该字段通常用来作为分组的依据
```

## 7）聚合函数

```
#强调：聚合函数聚合的是组的内容，若是没有分组，则默认一组
示例：
SELECT COUNT(*) FROM employee;
SELECT COUNT(*) FROM employee WHERE depart_id=1;
SELECT MAX(salary) FROM employee;
SELECT MIN(salary) FROM employee;
SELECT AVG(salary) FROM employee;
SELECT SUM(salary) FROM employee;
SELECT SUM(salary) FROM employee WHERE depart_id=3;
```

## 8）having过滤

```
！！！执行优先级从高到低：where > group by > having
#1. Where 发生在分组group by之前，因而Where中可以有任意字段，但是绝对不能使用聚合函数。
#2. Having发生在分组group by之后，因而Having中可以使用分组的字段，无法直接取到其他字
段,可以使用聚合
函数
mysql> select @@sql_mode;
+--------------------+
| @@sql_mode |
+--------------------+
| ONLY_FULL_GROUP_BY |
+--------------------+
row in set (0.00 sec)
mysql> select * from emp where salary > 100000;
+----+------+------+-----+------------+---------+--------------+------------
+--------+------
-----+
| id | emp_name | sex | age | hire_date | post | post_comment | salary |
office |
depart_id |
+----+------+------+-----+------------+---------+--------------+------------
+--------+------
-----+
| 2 | alex | male | 78 | 2015-03-02 | teacher | NULL | 1000000.31 | 401 |
1 |
+----+------+------+-----+------------+---------+--------------+------------
+--------+------
-----+
row in set (0.00 sec)
mysql> select post,group_concat(emp_name) from emp group by post having
salary > 10000;#错
误，分组后无法直接取到salary字段
ERROR 1054 (42S22): Unknown column 'salary' in 'having clause'
mysql> select post,group_concat(emp_name) from emp group by post having
avg(salary) > 10000;
+-----------+-------------------------------------------------------+
| post | group_concat(emp_name) |
+-----------+-------------------------------------------------------+
| operation | 程咬铁,程咬铜,程咬银,程咬金,张野 |
| teacher | 成龙,jinxin,jingliyang,liwenzhou,yuanhao,wupeiqi,alex |
```

## 9）order by

```
按单列排序
SELECT * FROM employee ORDER BY salary;
SELECT * FROM employee ORDER BY salary ASC;
SELECT * FROM employee ORDER BY salary DESC;
按多列排序:先按照age排序，如果年纪相同，则按照薪资排序
SELECT * from employee
ORDER BY age,
salary DESC;
```

## 10）limit

```
示例：
SELECT * FROM employee ORDER BY salary DESC
LIMIT 3; #默认初始位置为0
SELECT * FROM employee ORDER BY salary DESC
LIMIT 0,5; #从第0开始，即先查询出第一条，然后包含这一条在内往后查5条
SELECT * FROM employee ORDER BY salary DESC
LIMIT 5,5; #从第5开始，即先查询出第6条，然后包含这一条在内往后查5条
```

## 11）正则表达式

```
SELECT * FROM employee WHERE emp_name REGEXP '^ale';
SELECT * FROM employee WHERE emp_name REGEXP 'on$';
SELECT * FROM employee WHERE emp_name REGEXP 'm{2}';
小结：对字符串匹配的方式
WHERE emp_name = 'egon';
WHERE emp_name LIKE 'yua%';
WHERE emp_name REGEXP 'on$';
```

# 7、多表连接查询

## 1）建表和准备数据

```
#建表
create table department(
id int,
name varchar(20)
);
create table employee2(
id int primary key auto_increment,
name varchar(20),
sex enum('male','female') not null default 'male',
age int,
dep_id int
);
#插入数据
insert into department values
(200,'技术'),
(201,'人力资源'),
(202,'销售'),
(203,'运营');
insert into employee2(name,sex,age,dep_id) values
('egon','male',18,200),
('alex','female',48,201),
('wupeiqi','male',38,201),
('yuanhao','female',28,202),
('liwenzhou','male',18,200),
('jingliyang','female',18,204)
```

## 2）语法

```
#重点：外链接语法
SELECT 字段列表
FROM 表1 INNER|LEFT|RIGHT JOIN 表2
ON 表1.字段 = 表2.字段;
```

## 3）交叉连接

```
ariaDB [test]> select * from employee2,department; 
```

## 4）内连接，内连接，交叉运算后只保留两张表都存在的记录

```
MariaDB [test]> select
employee2.id,employee2.name,employee2.age,employee2.sex,department.name from
employee2 inner
join department on employee2.dep_id=department.id;
MariaDB [test]> select
employee2.id,employee2.name,employee2.age,employee2.sex,department.name from
employee2,department where employee2.dep_id=department.id;
+----+-----------+------+--------+--------------+
| id | name | age | sex | name |
+----+-----------+------+--------+--------------+
| 1 | egon | 18 | male | 技术 |
| 2 | alex | 48 | female | 人力资源 |
| 3 | wupeiqi | 38 | male | 人力资源 |
| 4 | yuanhao | 28 | female | 销售 |
| 5 | liwenzhou | 18 | male | 技术 |
+----+-----------+------+--------+--------------+
```

## 5）左连接，交叉运算后优先显示左表全部记录

```
MariaDB [test]> select employee2.id,employee2.name,department.name as
depart_name from
employee2 left join department on employee2.dep_id=department.id;
+----+------------+--------------+
| id | name | depart_name |
+----+------------+--------------+
| 1 | egon | 技术 |
| 5 | liwenzhou | 技术 |
| 2 | alex | 人力资源 |
| 3 | wupeiqi | 人力资源 |
| 4 | yuanhao | 销售 |
| 6 | jingliyang | NULL |
+----+------------+--------------+
```

## 6）右连接，交叉运算后优先显示右表全部记录

```
MariaDB [test]> select employee2.id,employee2.name,department.name as
depart_name from
employee2 right join department on employee2.dep_id=department.id;
+------+-----------+--------------+
| id | name | depart_name |
+------+-----------+--------------+
| 1 | egon | 技术 |
| 2 | alex | 人力资源 |
| 3 | wupeiqi | 人力资源 |
| 4 | yuanhao | 销售 |
| 5 | liwenzhou | 技术 |
| NULL | NULL | 运营 |
+------+-----------+--------------+
```

## 7）全外连接，显示左右两个表全部记录

```
MariaDB [test]> select * from employee2 left join department on
employee2.dep_id =
department.id
-> union
-> select * from employee2 right join department on employee2.dep_id =
department.id;
+------+------------+--------+------+--------+------+--------------+
| id | name | sex | age | dep_id | id | name |
+------+------------+--------+------+--------+------+--------------+
| 1 | egon | male | 18 | 200 | 200 | 技术 |
| 5 | liwenzhou | male | 18 | 200 | 200 | 技术 |
| 2 | alex | female | 48 | 201 | 201 | 人力资源 |
| 3 | wupeiqi | male | 38 | 201 | 201 | 人力资源 |
| 4 | yuanhao | female | 28 | 202 | 202 | 销售 |
| 6 | jingliyang | female | 18 | 204 | NULL | NULL |
| NULL | NULL | NULL | NULL | NULL | 203 | 运营 |
+------+------------+--------+------+--------+------+--------------+
#注意 union与union all的区别：union会去掉相同的纪录
```

### 示例1：

以内连接的方式查询employee和department表，并且employee表中的age字段值必须大

于25,即找出年龄大于25岁的员工以及员工所在的部门

```
select employee.name,department.name from employee inner join department
on employee.dep_id = department.id
where age > 25;
```

### 示例2：以内连接的方式查询employee和department表，并且以age字段的升序方式显示

```
select employee.id,employee.name,employee.age,department.name from
employee,department
where employee.dep_id = department.id
and age > 25
order by age asc;
```

# 8、子查询

## 1）解释

```
#1：子查询是将一个查询语句嵌套在另一个查询语句中。
#2：内层查询语句的查询结果，可以为外层查询语句提供查询条件。
#3：子查询中可以包含：IN、NOT IN、ANY、ALL、EXISTS 和 NOT EXISTS等关键字
#4：还可以包含比较运算符：= 、 !=、> 、<等
```

## 2）带IN关键字的子查询

```
#查询平均年龄在25岁以上的部门名
select id,name from department
where id in
(select dep_id from employee group by dep_id having avg(age) > 25);
#查看技术部员工姓名
select name from employee
where dep_id in
(select id from department where name='技术');
#查看不足1人的部门名(子查询得到的是有人的部门id)
select name from department where id not in (select distinct dep_id from
employee);
```

## 3）带比较运算符的子查询

```
#比较运算符：=、!=、>、>=、<、<=、<>
#查询大于所有人平均年龄的员工名与年龄
mysql> select name,age from emp where age > (select avg(age) from emp);
+---------+------+
| name | age |
+---------+------+
| alex | 48 |
| wupeiqi | 38 |
+---------+------+
2 rows in set (0.00 sec)
#查询大于部门内平均年龄的员工名、年龄
select t1.name,t1.age from emp t1
inner join
(select dep_id,avg(age) avg_age from emp group by dep_id) t2
on t1.dep_id = t2.dep_id
where t1.age > t2.avg_age;
```

## 4)带EXISTS关键字的子查询

```
#department表中存在dept_id=203，Ture
mysql> select * from employee
-> where exists
-> (select id from department where id=200);
+----+------------+--------+------+--------+
| id | name | sex | age | dep_id |
+----+------------+--------+------+--------+
| 1 | egon | male | 18 | 200 |
| 2 | alex | female | 48 | 201 |
| 3 | wupeiqi | male | 38 | 201 |
| 4 | yuanhao | female | 28 | 202 |
| 5 | liwenzhou | male | 18 | 200 |
| 6 | jingliyang | female | 18 | 204 |
+----+------------+--------+------+--------+
#department表中存在dept_id=205，False
```