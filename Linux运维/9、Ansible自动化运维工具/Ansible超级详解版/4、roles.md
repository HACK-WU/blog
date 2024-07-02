# 四、roles(难点)

## roles介绍

roles(角色): 就是通过分别将variables, tasks及handlers等放置于单独的目录中,并可以便捷地调用它们的一种机制。

假设我们要写一个playbook来安装管理lamp环境，那么这个playbook就会写很长。所以我们希望把这个很大的文件分成多个功能拆分, 分成apache管理,php管理,mysql管理，然后在需要使用的时候直接调用就可以了，以免重复写。就类似编程里的模块化的概念，以达到代码复用的效果。

## **创建roles的目录结构**

```powershell
files：用来存放由copy模块或script模块调用的文件。
tasks：至少有一个main.yml文件，定义各tasks。
handlers:有一个main.yml文件，定义各handlers。
templates：用来存放jinjia2模板。
vars：有一个main.yml文件，定义变量。
meta：有一个main.yml文件，定义此角色的特殊设定及其依赖关系。
```

**注意:** 在每个角色的目录中分别创建files, tasks,handlers,templates,vars和meta目录，用不到的目录可以创建为空目录.

## 通过roles实现lamp

需定制三个角色: httpd,mysql,php

**第1步: 创建roles目录及文件,并确认目录结构**

```powershell
master# cd /etc/ansible/roles/
master# mkdir -p {httpd,mysql,php}/{files,tasks,handlers,templates,vars,meta}
master# touch {httpd,mysql,php}/{tasks,handlers,vars,meta}/main.yml

master# yum install tree -y
master# tree /etc/ansible/roles/
/etc/ansible/roles/
├── httpd
│   ├── files
│   ├── handlers
│   │   └── main.yml
│   ├── meta
│   │   └── main.yml
│   ├── tasks
│   │   └── main.yml
│   ├── templates
│   └── vars
│       └── main.yml
├── mysql
│   ├── files
│   ├── handlers
│   │   └── main.yml
│   ├── meta
│   │   └── main.yml
│   ├── tasks
│   │   └── main.yml
│   ├── templates
│   └── vars
│       └── main.yml
└── php
    ├── files
    ├── handlers
    │   └── main.yml
    ├── meta
    │   └── main.yml
    ├── tasks
    │   └── main.yml
    ├── templates
    └── vars
        └── main.yml
```

**第2步: 准备httpd服务器的主页文件,php测试页和配置文件等**

```powershell
master# echo "test main page" > /etc/ansible/roles/httpd/files/index.html


master# echo -e "<?php\n\tphpinfo();\n?>" > /etc/ansible/roles/httpd/files/test.php 


master# yum install httpd -y
按需求修改配置文件后,拷贝到httpd角色目录里的files子目录
master# vim /etc/httpd/conf/httpd.conf
master# cp /etc/httpd/conf/httpd.conf /etc/ansible/roles/httpd/files/
```

**第3步: 编写httpd角色的main.yml文件**

```powershell
---
 - name: 安装httpd
   yum: name=httpd,httpd-devel state=present

 - name: 同步httpd配置文件
   copy: src=/etc/ansible/roles/httpd/files/httpd.conf dest=/etc/httpd/conf/httpd.conf

   notify: restart httpd

 - name: 同步主页文件
   copy: src=/etc/ansible/roles/httpd/files/index.html dest=/var/www/html/index.html

 - name: 同步php测试页
   copy: src=/etc/ansible/roles/httpd/files/test.php dest=/var/www/html/test.php

 - name: 启动httpd并开机自启动
   service: name=httpd state=started enabled=yes
```

**第4步: 编写httpd角色里的handler**

```powershell
master# vim /etc/ansible/roles/httpd/handlers/main.yml
---
- name: restart httpd
  service: name=httpd state=restarted
```

**第5步: 编写mysql角色的main.yml文件**

```powershell
---
- name: 安装mysql
  yum: name=mariadb,mariadb-server,mariadb-devel state=present

- name: 启动mysql并开机自启动
  service: name=mariadb state=started enabled=yes
```

**第6步: 编写php角色的main.yml文件**

```powershell
master# vim /etc/ansible/roles/php/tasks/main.yml
---
- name: 安装php及依赖包
  yum: name=php,php-gd,php-ldap,php-odbc,php-pear,php-xml,php-xmlrpc,php-mbstring,php-snmp,php-soap,curl,curl-devel,php-bcmath,php-mysql state=present

  notify: restart httpd
```

**第7步:编写lamp的playbook文件调用前面定义好的三个角色**

```powershell
master# vim /etc/ansible/playbook/lamp.yaml
---
- hosts: group1
  remote_user: root
  roles:
    - httpd
    - mysql
    - php
```

**第8步: 执行lamp的playbook文件**

```powershell
master# ansible-playbook /etc/ansible/playbook/lamp.yaml
```

## 拓展案例: 通过roles实现lamp并安装discuz

**第1步: 创建roles目录及文件,并确认目录结构**

```powershell
master# cd /etc/ansible/roles/
master# mkdir -p {httpd,mysql,php}/{files,tasks,handlers,templates,vars,meta}
master# touch {httpd,mysql,php}/{tasks,handlers,vars,meta}/main.yml
```

**第2步: 准备httpd相关文件**

```powershell
master# ls /etc/ansible/roles/httpd/files/
Discuz_X3.2_SC_UTF8.zip  					Discuz相关软件包
httpd.conf 									配置好的httpd.conf配置文件
```

**第3步: 编写httpd角色的main.yml文件**

```powershell
master# vim /etc/ansible/roles/httpd/tasks/main.yml
- name: 安装httpd相关软件包
  yum: name=httpd,httpd-devel state=latest

- name: 同步配置文件
  copy: src=/etc/ansible/roles/httpd/files/httpd.conf dest=/etc/httpd/conf/httpd.conf

  notify: restart httpd

- name: 拷贝discuz压缩包
  copy: src=/etc/ansible/roles/httpd/files/Discuz_X3.2_SC_UTF8.zip dest=/tmp/

- name: 解压并mv网站文件到httpd家目录
  shell: rm -rf /var/www/html/*  && rm -rf /test/ && mkdir -p /test/ &&  unzip /tmp/Discuz_X3.2_SC_UTF8.zip -d /test/ &> /dev/null  && mv /test/upload/* /var/www/html/ && chown -R apache.apache /var/www/html/
# 上面的命令有点多,可以写成脚本,然后使用script模块来调用执行

- name: 启动httpd并开机自启动
  service: name=httpd state=started enabled=on

```

**第4步: 编写httpd角色里的handler**

```powershell
master# vim /etc/ansible/roles/httpd/handlers/main.yml
---
- name: restart httpd
  service: name=httpd state=restarted
```

**第5步: 编写mysql角色的main.yml文件**

```powershell
master# vim /etc/ansible/roles/mysql/tasks/main.yml
---
- name: 安装mariadb相关软件包
  yum: name=mariadb-server,mariadb-devel state=latest

- name: 启动mariadb服务并设置开机自启动
  service: name=mariadb state=started enabled=on

- name: 执行建库脚本
  script: /etc/ansible/roles/mysql/files/create.sh
```

**第6步: 编写mysql的建库脚本**

```powershell
master# vim /etc/ansible/roles/mysql/files/create.sh

#!/bin/bash

mysql << EOF
create database if not exists discuz default charset=utf8;
grant all on discuz.* to 'discuz'@'localhost' identified by '123';
flush privileges;
EOF
```

**第7步: 编写php角色的main.yml文件**

```powershell
master# vim /etc/ansible/roles/php/tasks/main.yml
---
- name: 安装php相关软件包
  yum: name=php,php-gd,php-ldap,php-odbc,php-pear,php-xml,php-xmlrpc,php-mbstring,php-snmp,php-soap,curl,curl-devel,php-bcmath,php-mysql state=present

  notify: restart httpd
```

**第8步:编写lamp的playbook文件调用前面定义好的三个角色**

```powershell
master# vim /etc/ansible/playbook/lamp.yaml
---
- hosts: group1
  remote_user: root
  roles:
    - httpd
    - mysql
    - php
```

**第9步: 执行lamp的playbook文件**

```powershell
master# ansible-playbook /etc/ansible/playbook/lamp.yaml
```

## **练习**

**请使用role来实现lnmp**
