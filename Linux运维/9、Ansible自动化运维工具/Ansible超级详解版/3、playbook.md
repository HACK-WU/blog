# 三、playbook

playbook(剧本): 是ansible用于配置,部署,和管理被控节点的剧本。用于ansible操作的编排。

参考:<https://docs.ansible.com/ansible/latest/user_guide/playbooks_intro.html>

使用的格式为**yaml**格式（saltstack,elk,docker,docker-compose,kubernetes等也都会用到yaml格式)

## YMAL格式

*   以.yaml或.yml结尾

<!---->

*   文件的第一行以 "---"开始，表明YMAL文件的开始(可选的)
*   以#号开头为注释
*   列表中的所有成员都开始于相同的缩进级别, 并且使用一个 `"- "` 作为开头(一个横杠和一个空格)
*   一个字典是由一个简单的 `键: 值` 的形式组成(这个冒号后面必须是一个空格)
*   **==注意: 写这种文件不要使用tab键，都使用空格==**

参考: <https://docs.ansible.com/ansible/latest/reference_appendices/YAMLSyntax.html#yaml-syntax>

下面看一个官方的示例感受一下

```powershell
---
# 一位职工记录
name: Example Developer
job: Developer
skill: Elite
employed: True
foods:
    - Apple
    - Orange
    - Strawberry
    - Mango
languages:
    ruby: Elite
    python: Elite
    dotnet: Lame
```

## playbook实例

先直接来看一个实例

**第1步: 创建一个存放playbook的目录(路径自定义)**

```powershell
master# mkdir /etc/ansible/playbook
```

**第2步: 准备httpd配置文件,并修改成你想要的配置**

```powershell
master# yum install httpd -y

按需要修改你想要的配置(为了测试可以随意改动标记一下)
master# vim /etc/httpd/conf/httpd.conf
```

**第3步: 写一个playbook文件(后缀为.yml或.yaml)**

```powershell
# vim /etc/ansible/playbook/example.yaml
---
- hosts: group1
  remote_user: root
  tasks:  
  - name: ensure apache is at the latest version	
    yum: name=httpd,httpd-devel state=latest
    
  - name: write the apache config file		
    copy: src=/etc/httpd/conf/httpd.conf dest=/etc/httpd/conf/httpd.conf
    
    notify:
    - restart apache
    
  - name: ensure apache is running (and enable it at boot)
    service: name=httpd state=started enabled=yes
    
  handlers:	
    - name: restart apache
      service: name=httpd state=restarted
```

第4步: 执行写好的palybook

*   会显示出执行的过程，并且执行的每一步都有ok,changed,failed等标识
*   执行如果有错误(failed)会回滚，解决问题后，直接再执行这条命令即可,并会把failed改为changed（幂等性)

```powershell
# ansible-playbook /etc/ansible/playbook/example.yaml
```

## Playbook常见语法

**hosts:** 用于指定要执行任务的主机，其可以是一个或多个由冒号分隔主机组.

**remote\_user:** 用于指定远程主机上的执行任务的用户.

```powershell
- hosts: group1			
  remote_user: root	
```

**tasks:** 任务列表, 按顺序执行任务.

*   如果一个host执行task失败, 整个tasks都会回滚, 修正playbook 中的错误, 然后重新执行即可.

```powershell
  tasks:
  - name: ensure apache is at the latest version	
    yum: name=httpd,httpd-devel state=latest
    
  - name: write the apache config file		
    copy: src=/etc/httpd/conf/httpd.conf dest=/etc/httpd/conf/httpd.conf
```

**handlers:**  类似task，但需要使用notify通知调用。

*   不管有多少个通知者进行了notify，等到play中的所有task执行完成之后，handlers也只会被执行一次.
*   handlers最佳的应用场景是用来重启服务,或者触发系统重启操作.除此以外很少用到了.

```powershell
    notify:				  
    - restart apache
    
  - name: ensure apache is running (and enable it at boot)
    service: name=httpd state=started enabled=yes
    
  handlers:
    - name: restart apache
      service: name=httpd state=restarted
```

**练习:** 修改httpd的端口为8080,再执行playbook测试

**variables:** 变量

*   定义变量可以被多次方便调用

```powershell
master# vim /etc/ansible/playbook/example2.yaml
---
 - hosts: group1
   remote_user: root
   vars:
   - user: test1
   tasks:
   - name: create user
     user: name={{user}} state=present
```

```powershell
master# ansible-playbook /etc/ansible/playbook/example2.yaml
```

### 案例: playbook编排vsftpd

写一个playbook实现

1.  配置yum
2.  安装vsftpd包
3.  修改配置文件(要求拒绝匿名用户登录)
4.  启动服务并实现vsftpd服务开机自动启动

```powershell
---
- hosts: group1                 
  remote_user: root                     
  tasks:                                
  - name: rm yum repository      
    file: path=/etc/yum.repos.d/ state=absent
    
  - name: 同步master上的yum源到group1
    copy: src=/etc/yum.repos.d dest=/etc/
    
  - name: ensure vsftpd is at the latest version        
    yum: name=vsftpd state=latest
    
  - name: write the apache config file          
    copy: src=/etc/vsftpd/vsftpd.conf dest=/etc/vsftpd/vsftpd.conf 
    
    notify:                             
    - restart vsftpd
    
  - name: ensure vsftpd is running (and enable it at boot)
    service: name=vsftpd state=started enabled=yes
    
  handlers:                     
    - name: restart vsftpd              
      service: name=vsftpd state=restarted
```

## **playbook编排多个hosts任务**

```powershell
---			# ---代表开始(可选项,不写也可以)
- hosts: 10.1.1.12
  remote_user: root
  tasks:
  - name: 创建/test1/目录
    file: path=/test1/ state=directory
# 这里不能用---分隔,会报语法错误(后面课程玩k8s编排也写YAML文件,是可以用---来分隔段落的)
- hosts: 10.1.1.13
  remote_user: root
  tasks:
  - name: 创建/test2/目录
    file: path=/test2/ state=directory
...			# ...代表结束(可选项,不写也可以)
```

### 案例: 编排nfs搭建与客户端挂载

1, 在master上准备nfs配置文件

```powershell
# vim /etc/exports
/share  *(ro)
```

2, 编写yaml编排文件

```powershell
# vim /etc/ansible/playbook/nfs.yml
---
- hosts: 10.1.1.12
  remote_user: root
  tasks:
  - name: 安装nfs服务相关软件包
    yum: name=nfs-utils,rpcbind,setup  state=latest

  - name: 创建共享目录
    file: path=/share/ state=directory

  - name: 同步nfs配置文件
    copy: src=/etc/exports dest=/etc/exports

    notify: restart nfs

  - name: 启动rpcbind服务,并设置为开机自启动
    service: name=rpcbind state=started enabled=on

  - name: 启动nfs服务,并设置为开机自启动
    service: name=nfs state=started enabled=on

  handlers:
  - name: restart nfs
    service: name=nfs state=restarted

- hosts: 10.1.1.13
  remote_user: root
  tasks:
  - name: 安装nfs客户端软件包
    yum: name=nfs-utils state=latest

  - name: 挂载nfs服务器的共享
    shell: mount 10.1.1.12:/share /mnt
```

3, 执行playbook

```powershell
# ansible-playbook /etc/ansible/playbook/nfs.yaml
```

