# Playbook示例  安装nginx服务

```
示例 nginx.yml
- hosts: all
  remote_user: root
  tasks:
    - name: add group nginx
      user: name=nginx state=present
    - name: add user nginx
      user: name=nginx state=present group=nginx
    - name: Install Nginx
      yum: name=nginx state=present
    - name: Start Nginx
      service: name=nginx state=started enabled=yes
```

# handlers和notify结合使用触发条件（handler:处理器，notify通知）

```
Handlers 实际上就是一个触发器
是task列表，这些task与前述的task并没有本质上的不同,用于当关注的资源发生变化时，才会采取一定的操作
Notify此action可用于在每个play的最后被触发，
这样可避免多次有改变发生时每次都执行指定的操作，仅在所有的变化发生完成后一次性地执行指定操作。
在notify中列出的操作称为handler，也即notify中调用handler中定义的操作
```

# Playbook中handlers使用

```
- hosts: websrvs
  remote_user: root
  tasks:
    - name: Install httpd
      yum: name=httpd state=present
    - name: Install configure file
      copy: src=files/httpd.conf dest=/etc/httpd/conf/ backup=yes
      notify: restart httpd
    - name: ensure apache is running
      service: name=httpd state=started enabled=yes
  
  handlers:
    - name: restart httpd
      service: name=httpd state=restarted
```

### 示例

```
- hosts: webnodes
  vars:
    http_port: 80
    max_clients: 256
  remote_user: root
  
  tasks:
    - name: ensure apache is at the latest version
      yum: name=httpd state=latest
    - name: ensure apache is running
      service: name=httpd state=started
    - name: Install configure file
      copy: src=files/httpd.conf dest=/etc/httpd/conf/
      notify: restart httpd
  
  handlers:
      - name: restart httpd 
        service: name=httpd state=restarted
```

### 示例

```
- hosts: websrvs
  remote_user: root
  
  tasks:
    - name: add group nginx
      tags: user
      user: name=nginx state=present
    - name: add user nginx
      user: name=nginx state=present group=nginx
    - name: Install Nginx
      yum: name=nginx state=present
    - name: config
      copy: src=/root/config.txt dest=/etc/nginx/nginx.conf
      notify:
        - Restart Nginx
        - Check Nginx Process
  
  handlers:
    - name: Restart Nginx
      service: name=nginx state=restarted enabled=yes
    - name: Check Nginx process
      shell: killall -0 nginx > /tmp/nginx.log
```

# Playbook中tags使用

```
tags: 添加标签 
可以指定某一个任务添加一个标签,添加标签以后,想执行某个动作可以做出挑选来执行
多个动作可以使用同一个标签
示例：httpd.yml
- hosts: websrvs
  remote_user: root
  
  tasks:
    - name: Install httpd
      yum: name=httpd state=present
      tage: install 
    - name: Install configure file
      copy: src=files/httpd.conf dest=/etc/httpd/conf/
      tags: conf
    - name: start httpd service
      tags: service
      service: name=httpd state=started enabled=yes
ansible-playbook –t install,conf httpd.yml   指定执行install,conf 两个标签
```

# 示例

```
//heartbeat.yaml
- hosts: hbhosts
  remote_user: root
  
  tasks:
    - name: ensure heartbeat latest version
      yum: name=heartbeat state=present
    - name: authkeys configure file
      copy: src=/root/hb_conf/authkeys dest=/etc/ha.d/authkeys
    - name: authkeys mode 600
      file: path=/etc/ha.d/authkeys mode=600
      notify:
        - restart heartbeat
    - name: ha.cf configure file
      copy: src=/root/hb_conf/ha.cf dest=/etc/ha.d/ha.cf
      notify:
        - restart heartbeat
  handlers:
    - name: restart heartbeat
      service: name=heartbeat state=restarted
```

# Playbook中tags使用

```
- hosts: testsrv
  remote_user: root
  tags: inshttpd   针对整个playbook添加tage
  tasks:
    - name: Install httpd
      yum: name=httpd state=present
    - name: Install configure file
      copy: src=files/httpd.conf dest=/etc/httpd/conf/
      tags: rshttpd
      notify: restart httpd
  handlers:
    - name: restart httpd
      service: name=httpd status=restarted
     
ansible-playbook –t rshttpd httpd2.yml
```

## 示例：

```
[root@server1 ~]# vim test.yml 

---
- hosts : all
  remote_user : root

  tasks :
        - name : install httpd
          yum : name=httpd
        - name : start httpd
          service : name=httpd state=started enabled=yes
        - name : copy httpd.conf
          copy : src=httpd.conf dest=/etc/httpd/conf/httpd.conf
          notify :
            - restart httpd
            - waring
        - name : waring one
          shell : /usr/bin/wall waring one
          tags : waring
        - name : waring two
          shell : /usr/bin/wall waring two
          tags : waring
  handlers :
        - name : restart httpd
          service : name=httpd state=restarted
        - name : waring
          shell : /usr/bin/wall walling

```

# Playbook中变量的使用

```
变量名：仅能由字母、数字和下划线组成，且只能以字母开头
变量来源：
    1> ansible setup facts 远程主机的所有变量都可直接调用 (系统自带变量)
       setup模块可以实现系统中很多系统信息的显示
                可以返回每个主机的系统信息包括:版本、主机名、cpu、内存
       ansible all -m setup -a 'filter="ansible_nodename"'     查询主机名
       ansible all -m setup -a 'filter="ansible_memtotal_mb"'  查询主机内存大小
       ansible all -m setup -a 'filter="ansible_distribution_major_version"'  查询系统版本
       ansible all -m setup -a 'filter="ansible_processor_vcpus"' 查询主机cpu个数
    
    2> 在/etc/ansible/hosts(主机清单)中定义变量
        普通变量：主机组中主机单独定义，优先级高于公共变量(单个主机 )
        公共(组)变量：针对主机组中所有主机定义统一变量(一组主机的同一类别)
    
    3> 通过命令行指定变量，优先级最高
       ansible-playbook –e varname=value
    
    4> 在playbook中定义
       vars:
        - var1: value1
        - var2: value2
    
    5> 在独立的变量YAML文件中定义
    
    6> 在role中定义
变量命名:
    变量名仅能由字母、数字和下划线组成，且只能以字母开头
变量定义：key=value
    示例：http_port=80
变量调用方式：
    1> 通过{{ variable_name }} 调用变量，且变量名前后必须有空格，有时用“{{ variable_name }}”才生效
    2> ansible-playbook –e 选项指定
       ansible-playbook test.yml -e "hosts=www user=magedu"
```

## 示例：

```
[root@server1 ~]# vim test.yml 

---
 - hosts : all
   remote_user: root

   tasks :
        - name : install pkname1
          yum : name={{pkname1}}    #定义变量
        - name : install pkname1    
          yum : name={{pkname2}}    #定义变量
    
[root@server1 ~]# ansible-playbook -e 'pkname1=vsftpd pk=httpd' test.yml 
#-e ,给变量赋值
```

```
[root@server1 ~]# vim test.yml 

---
 - hosts : all
   remote_user: root

   vars :
        - pkname1 : httpd    #定义变量
        - pkname2 : vsftpd
   tasks :
        - name : install pkname1
          yum : name={{pkname1}}    #引用变量
        - name : install pkname1
          yum : name={{pkname2}}
```

```
在主机清单中定义变量,在ansible中使用变量
vim /etc/ansible/hosts
[appsrvs]
192.168.38.17 http_port=817 name=www
192.168.38.27 http_port=827 name=web
调用变量
ansible appsrvs -m hostname -a'name={{name}}'  更改主机名为各自被定义的变量 
针对一组设置变量
[appsrvs:vars]
make="-"
ansible appsrvs -m hostname -a 'name={{name}}{{mark}}{{http_port}}'  ansible调用变量
```

```
将变量写进单独的配置文件中引用
vim vars.yml
pack: vsftpd
service: vsftpd
引用变量文件
vars_files:
  - vars.yml
```

# Ansible基础元素

```
Facts：是由正在通信的远程目标主机发回的信息，这些信息被保存在ansible变量中。
       要获取指定的远程主机所支持的所有facts，可使用如下命令进行
       ansible websrvs -m setup
通过命令行传递变量
    在运行playbook的时候也可以传递一些变量供playbook使用
    示例：
        ansible-playbook test.yml -e "hosts=www user=magedu"
        
register
把任务的输出定义为变量，然后用于其他任务
示例:
tasks:
- shell: /usr/bin/foo
  register: foo_result
  ignore_errors: True
```

### 示例：使用setup变量

```
示例：var.yml
- hosts: websrvs
  remote_user: root
  tasks:
    - name: create log file
      file: name=/var/log/ {{ ansible_fqdn }} state=touch
ansible-playbook var.yml
```

### 示例：变量

```
示例：var.yml
- hosts: websrvs
  remote_user: root
  tasks:
    - name: install package
      yum: name={{ pkname }} state=present
      
ansible-playbook –e pkname=httpd var.yml
```

### 示例：变量

```
示例：var.yml
- hosts: websrvs
  remote_user: root
vars:
  - username: user1
  - groupname: group1
tasks:
  - name: create group
    group: name={{ groupname }} state=present
  - name: create user
    user: name={{ username }} state=present
ansible-playbook var.yml
ansible-playbook -e "username=user2 groupname=group2” var2.yml
```

### 变量

```
主机变量
可以在inventory中定义主机时为其添加主机变量以便于在playbook中使用

示例：
[websrvs]
www1.magedu.com http_port=80 maxRequestsPerChild=808
www2.magedu.com http_port=8080 maxRequestsPerChild=909

组变量
组变量是指赋予给指定组内所有主机上的在playbook中可用的变量

示例：
    [websrvs]
    www1.magedu.com
    www2.magedu.com

    [websrvs:vars]
    ntp_server=ntp.magedu.com
    nfs_server=nfs.magedu.com
```

### 示例：变量

```
普通变量
    [websrvs]
    192.168.99.101 http_port=8080 hname=www1
    192.168.99.102 http_port=80 hname=www2
公共（组）变量
    [websvrs:vars]
    http_port=808
    mark="_"
    [websrvs]
    192.168.99.101 http_port=8080 hname=www1
    192.168.99.102 http_port=80 hname=www2
    ansible websvrs –m hostname –a ‘name={{ hname }}{{ mark }}{{ http_port }}’
命令行指定变量：
    ansible websvrs –e http_port=8000 –m hostname –a'name={{ hname }}{{ mark }}{{ http_port }}'
```

### 使用变量文件

```
cat vars.yml
var1: httpd
var2: nginx
cat var.yml
- hosts: web
  remote_user: root
  vars_files:
    - vars.yml
  tasks:
    - name: create httpd log
      file: name=/app/{{ var1 }}.log state=touch
    - name: create nginx log
      file: name=/app/{{ var2 }}.log state=touch
      
hostname app_81.magedu.com  hostname 不支持"_",认为"_"是非法字符
hostnamectl set-hostname app_80.magedu.com  可以更改主机名
```

### 变量

```
组嵌套
inventory中，组还可以包含其它的组，并且也可以向组中的主机指定变量。
这些变量只能在ansible-playbook中使用，而ansible命令不支持
示例：
    [apache]
    httpd1.magedu.com
    httpd2.magedu.com
    
    [nginx]
    ngx1.magedu.com
    ngx2.magedu.com
    
    [websrvs:children]
    apache
    nginx
    
    [webservers:vars]
    ntp_server=ntp.magedu.com
```

### invertory参数

```
invertory参数：用于定义ansible远程连接目标主机时使用的参数，而非传递给playbook的变量
    ansible_ssh_host
    ansible_ssh_port
    ansible_ssh_user
    ansible_ssh_pass
    ansbile_sudo_pass
示例：
    cat /etc/ansible/hosts
    [websrvs]
    192.168.0.1 ansible_ssh_user=root ansible_ssh_pass=magedu
    192.168.0.2 ansible_ssh_user=root ansible_ssh_pass=magedu
```

### invertory参数

```
inventory参数
ansible基于ssh连接inventory中指定的远程主机时，还可以通过参数指定其交互方式；
这些参数如下所示：
ansible_ssh_host
The name of the host to connect to, if different from the alias you wishto give to it.
ansible_ssh_port
The ssh port number, if not 22
ansible_ssh_user
The default ssh user name to use.
ansible_ssh_pass
The ssh password to use (this is insecure, we strongly recommendusing --ask-pass or SSH keys)
ansible_sudo_pass
The sudo password to use (this is insecure, we strongly recommendusing --ask-sudo-pass)
ansible_connection
Connection type of the host. Candidates are local, ssh or paramiko.
The default is paramiko before Ansible 1.2, and 'smart' afterwards which
detects whether usage of 'ssh' would be feasible based on whether
ControlPersist is supported.
ansible_ssh_private_key_file
Private key file used by ssh. Useful if using multiple keys and you don't want to use SSH agent.
ansible_shell_type
The shell type of the target system. By default commands are formatted
using 'sh'-style syntax by default. Setting this to 'csh' or 'fish' will cause
commands executed on target systems to follow those shell's syntax instead.
ansible_python_interpreter
The target host python path. This is useful for systems with more
than one Python or not located at "/usr/bin/python" such as \*BSD, or where /usr/bin/python
is not a 2.X series Python. We do not use the "/usr/bin/env" mechanism as that requires the remote user's
path to be set right and also assumes the "python" executable is named python,where the executable might
be named something like "python26".
ansible\_\*\_interpreter
Works for anything such as ruby or perl and works just like ansible_python_interpreter.
This replaces shebang of modules which will run on that host.
```