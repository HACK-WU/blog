## ansible模块

ansible是基于模块工作的，本身没有批量部署的能力。真正具有批量部署的是ansible所运行的模块，ansible只是提供一种框架。

ansible支持的模块非常的多，我们并不需要把每个模块都记住，而只需要熟悉一些常见的模块，其它的模块在需要用到时再查询即可。

**查看所有支持的模块**

~~~powershell
# ansible-doc -l		
a10_server                                           Manage A10 Networks AX/SoftAX...
a10_server_axapi3                                    Manage A10 Networks AX/SoftAX...
a10_service_group                                    Manage A10 Networks AX/SoftAX...
a10_virtual_server                                   Manage A10 Networks AX/SoftAX...
aci_aaa_user                                         Manage AAA users (aaa:User)
。。。。。。

如果要查看ping模块的用法，使用下面命令（其它模块以此类推)
# ansible-doc ping
~~~

官网模块文档地址: https://docs.ansible.com/ansible/latest/modules/list_of_all_modules.html



### hostname模块

hostname模块用于修改主机名（**注意**: ==它不能修改/etc/hosts文件==)

https://docs.ansible.com/ansible/latest/modules/hostname_module.html#hostname-module

将其中一远程机器主机名修改为agent1.cluster.com

~~~powershell
master# ansible 10.1.1.12  -m hostname -a 'name=agent1.cluster.com'
基本格式为: ansible 操作的机器名或组名 -m 模块名 -a "参数1=值1 参数2=值2"
~~~



### file模块(重点)

file模块用于对文件相关的操作(创建, 删除, 软硬链接等)

https://docs.ansible.com/ansible/latest/modules/file_module.html#file-module

创建一个目录

~~~powershell
master# ansible group1 -m file -a 'path=/test state=directory'
~~~

创建一个文件

~~~powershell
master# ansible group1 -m file -a 'path=/test/111 state=touch'
~~~

递归修改owner,group,mode

~~~powershell
master# ansible group1 -m file -a 'path=/test recurse=yes owner=bin group=daemon mode=1777'
~~~

删除目录（连同目录里的所有文件)

~~~powershell
master# ansible group1 -m file -a 'path=/test state=absent'
~~~

创建文件并指定owner,group,mode等

~~~powershell
master# ansible group1 -m file -a 'path=/tmp/111 state=touch owner=bin group=daemon mode=1777'
~~~

删除文件

~~~powershell
master# ansible group1 -m file -a 'path=/tmp/111 state=absent'
~~~

创建软链接文件

~~~powershell
master# ansible group1 -m file -a 'src=/etc/fstab path=/tmp/fstab state=link'
~~~

创建硬链接文件

~~~powershell
master# ansible group1 -m file -a 'src=/etc/fstab path=/tmp/fstab2 state=hard'
~~~



### stat模块(了解)

stat模块类似linux的stat命令，用于获取文件的状态信息。

https://docs.ansible.com/ansible/latest/modules/stat_module.html#stat-module

获取/etc/fstab文件的状态信息

~~~powershell
master# ansible group1 -m stat -a 'path=/etc/fstab'
~~~



### copy模块(重点)

copy模块用于对文件的远程拷贝操作（如把本地的文件拷贝到远程的机器上)

https://docs.ansible.com/ansible/latest/modules/copy_module.html#copy-module

在master上准备一个文件，拷贝此文件到group1的所有机器上

~~~powershell
master# echo master > /tmp/222
master# ansible group1 -m copy -a 'src=/tmp/222 dest=/tmp/333'
~~~

使用content参数直接往远程文件里写内容（会覆盖原内容）

~~~powershell
master# ansible group1 -m copy -a 'content="ha ha\n" dest=/tmp/333'
注意:ansible中-a后面的参数里也有引号时，记得要单引双引交叉使用，如果都为双引会出现问题
~~~

使用force参数控制是否强制覆盖

~~~powershell
如果目标文件已经存在，则不覆盖
master# ansible group1 -m copy -a 'src=/tmp/222 dest=/tmp/333 force=no'
如果目标文件已经存在，则会强制覆盖
master# ansible group1 -m copy -a 'src=/tmp/222 dest=/tmp/333 force=yes'
~~~

使用backup参数控制是否备份文件

~~~powershell
backup=yes表示如果拷贝的文件内容与原内容不一样，则会备份一份
group1的机器上会将/tmp/333备份一份（备份文件命名加上时间），再远程拷贝新的文件为/tmp/333
master# ansible group1 -m copy -a 'src=/etc/fstab dest=/tmp/333 backup=yes owner=daemon group=daemon mode=1777'
~~~

copy模块拷贝时要注意拷贝目录后面是否带"/"符号

~~~powershell
/etc/yum.repos.d后面不带/符号，则表示把/etc/yum.repos.d整个目录拷贝到/tmp/目录下
master# ansible group1 -m copy -a 'src=/etc/yum.repos.d dest=/tmp/'
/etc/yum.repos.d/后面带/符号，则表示把/etc/yum.repos.d/目录里的所有文件拷贝到/tmp/目录下
master# ansible group1 -m copy -a 'src=/etc/yum.repos.d/ dest=/tmp/'
~~~

**练习: 在master上配置好所有的yum源，然后拷贝到group1的远程机器上（要求目录内的内容完全一致)**

~~~powershell
master# ansible group1 -m file -a "path=/etc/yum.repos.d/ state=absent"
master# ansible group1 -m copy -a "src=/etc/yum.repos.d dest=/etc/"
~~~

**练习: 使用hostname模块修改过主机名后.在master上修改/etc/hosts文件，并拷贝到group1的远程机器上**

~~~powershell
先在master上修改好/etc/hosts文件，然后使用下面命令拷贝过去覆盖
master# ansible group1 -m copy -a "src=/etc/hosts dest=/etc/hosts"
~~~

 

**关于DNS的补充:**

* 域名为公网的唯一名字,主机名为内网的名字(可以重名,但最好不要这么做)

* 目前自建DNS做域名解析已经很少了, 但可以通过DNS解析主机名来实现内网多台服务器的解析
* 现在学了ansible的hostname与copy模块,轻松实现N多台服务器的主机名管理,DNS也不需要再搭建了





### template模块(拓展)

与copy模块功能几乎一样.

template模块首先使用变量渲染jinja2模板文件成普通文件,然后再复制过去.而copy模块不支持.(jinja2是一个基于python的模板引擎)

https://docs.ansible.com/ansible/latest/modules/template_module.html#template-module

```powershell
master# ansible -m template group1 -a "src=/etc/hosts dest=/tmp/hosts"
```

template模块不能拷贝目录

~~~powershell
master# ansible -m template group1 -a "src=/etc/yum.repos.d/ dest=/etc/yum.repos.d/"
~~~



### fetch模块

fetch模块与copy模块类似，但作用相反。用于把远程机器的文件拷贝到本地。

https://docs.ansible.com/ansible/latest/modules/fetch_module.html#fetch-module

第1步: 在两台被管理机上分别创建一个同名文件（但内容不同)

~~~powershell
agent1# echo agent1 > /tmp/1.txt
agent2# echo agent2 > /tmp/1.txt
~~~

第2步: 从master上fecth文件(因为group1里有2台机器,为了避免同名文件文件冲突，它使用了不同的目录)

~~~powershell
master# ansible group1  -m fetch -a 'src=/tmp/1.txt dest=/tmp/'
10.1.1.12 | CHANGED => {
    "changed": true, 
    "checksum": "d2911a028d3fcdf775a4e26c0b9c9d981551ae41", 
    "dest": "/tmp/10.1.1.12/tmp/1.txt", 	10.1.1.12的在这里
    "md5sum": "0d59da0b2723eb03ecfbb0d779e6eca5", 
    "remote_checksum": "d2911a028d3fcdf775a4e26c0b9c9d981551ae41", 
    "remote_md5sum": null
}
10.1.1.13 | CHANGED => {
    "changed": true, 
    "checksum": "b27fb3c4285612643593d53045035bd8d972c995", 
    "dest": "/tmp/10.1.1.13/tmp/1.txt", 	10.1.1.13的在这里
    "md5sum": "cd0bd22f33d6324908dbadf6bc128f52", 
    "remote_checksum": "b27fb3c4285612643593d53045035bd8d972c995", 
    "remote_md5sum": null
}

~~~

第3步: 先删除上面fetch过来的, 然后尝试只fetch其中一台机器的，也会使用名称来做子目录区分

~~~powershell
master# rm /tmp/10.1.1.* -rf


master# ansible 10.1.1.12  -m fetch -a 'src=/tmp/1.txt dest=/tmp/'
10.1.1.12 | CHANGED => {
    "changed": true, 
    "checksum": "d2911a028d3fcdf775a4e26c0b9c9d981551ae41", 
    "dest": "/tmp/10.1.1.12/tmp/1.txt", 	只fetch一个，也会这样命名
    "md5sum": "0d59da0b2723eb03ecfbb0d779e6eca5", 
    "remote_checksum": "d2911a028d3fcdf775a4e26c0b9c9d981551ae41", 
    "remote_md5sum": null
}
~~~

**注意**: fetch模块不能从远程拷贝目录到本地



### user模块

user模块用于管理用户账号和用户属性。

https://docs.ansible.com/ansible/latest/modules/user_module.html#user-module

创建aaa用户,默认为普通用户,创建家目录

~~~powershell
master# ansible group1 -m user -a ‘name=aaa state=present’
~~~

创建bbb系统用户,并且登录shell环境为/sbin/nologin

~~~powershell
master# ansible group1 -m user -a ‘name=bbb state=present system=yes  shell="/sbin/nologin"’
~~~

创建ccc用户, 使用uid参数指定uid, 使用password参数传密码

~~~powershell
master# echo 123456 | openssl passwd -1 -stdin
$1$DpcyhW2G$Kb/y1f.lyLI4MpRlHU9oq0

下一句命令注意一下格式，密码要用双引号引起来，单引号的话验证时会密码不正确
master# ansible group1 -m user -a 'name=ccc uid=2000 state=present password="$1$DpcyhW2G$Kb/y1f.lyLI4MpRlHU9oq0"'
~~~

创建一个普通用户叫hadoop,并产生空密码密钥对

~~~powershell
master# ansible group1 -m user -a 'name=hadoop generate_ssh_key=yes'
~~~

删除aaa用户,但家目录默认没有删除

~~~powershell
master# ansible group1 -m user -a 'name=aaa state=absent'
~~~

删除bbb用户,使用remove=yes参数让其删除用户的同时也删除家目录

~~~powershell
master# ansible group1 -m user -a 'name=bbb state=absent remove=yes'
~~~




### group模块

group模块用于管理用户组和用户组属性。

https://docs.ansible.com/ansible/latest/modules/group_module.html#group-module

创建组

~~~powershell
master# ansible group1 -m group -a 'name=groupa gid=3000 state=present'
~~~

删除组（如果有用户的gid为此组，则删除不了)

~~~powershell
master# ansible group1 -m group -a 'name=groupa state=absent'
~~~



### cron模块

cron模块用于管理周期性时间任务

https://docs.ansible.com/ansible/latest/modules/cron_module.html#cron-module

创建一个cron任务,不指定user的话,默认就是root（因为我这里是用root操作的)。
如果minute,hour,day,month,week不指定的话，默认都为*

~~~powershell
master# ansible group1 -m cron -a 'name="test cron1" user=root job="touch /tmp/111" minute=*/2' 
~~~

删除cron任务

~~~powershell
master# ansible group1 -m cron -a 'name="test cron1" state=absent'
~~~



### yum_repository模块

yum_repository模块用于配置yum仓库。

https://docs.ansible.com/ansible/latest/modules/yum_repository_module.html

增加一个/etc/yum.repos.d/local.repo配置文件

~~~powershell
master# ansible group1 -m yum_repository -a "name=local description=localyum baseurl=file:///mnt/ enabled=yes gpgcheck=no"
~~~

~~~powershell
注意：此模块只帮助配置yum仓库,但如果仓库里没有软件包，安装一样会失败。所以可以手动去挂载光驱到/mnt目录
# mount /dev/cdrom /mnt
~~~

删除/etc/yum.repos.d/local.repo配置文件

~~~powershell
master# ansible group1 -m yum_repository -a "name=local state=absent" 
~~~



### yum模块(重点)

yum模块用于使用yum命令来实现软件包的安装与卸载。

https://docs.ansible.com/ansible/latest/modules/yum_module.html#yum-module

使用yum安装一个软件（前提:group1的机器上的yum配置都已经OK）

~~~powershell
master# ansible group1 -m yum -a 'name=vsftpd state=present'
~~~

使用yum安装httpd,httpd-devel软件,state=latest表示安装最新版本

~~~powershell
master# ansible group1 -m yum -a 'name=httpd,httpd-devel state=latest' 
~~~

使用yum卸载httpd,httpd-devel软件

~~~powershell
master# ansible group1 -m yum -a 'name=httpd,httpd-devel state=absent' 
~~~



### service模块(重点)

service模块用于控制服务的启动,关闭,开机自启动等。

https://docs.ansible.com/ansible/latest/modules/service_module.html#service-module

启动vsftpd服务，并设为开机自动启动

~~~powershell
master# ansible group1 -m service -a 'name=vsftpd state=started enabled=on'
~~~

关闭vsftpd服务，并设为开机不自动启动

~~~powershell
master# ansible group1 -m service -a 'name=vsftpd state=stopped enabled=false'
~~~



**练习: 在group1的被管理机里的mariadb里创建一个abc库**

~~~powershell

~~~

**练习:**

**假设我主机清单里定义的group1里有多台机器，它们现在要做一个集群。此集群要求实现一个名为hadoop的普通用户之间的两两免密登录，如何实现(要求只在master上进行操作)?**

~~~powershell

~~~



### script模块

script模块用于在远程机器上执行**本地**脚本。

https://docs.ansible.com/ansible/latest/modules/script_module.html#script-module

```powershell
在master上准备一个脚本
master# vim /tmp/1.sh
#!/bin/bash
mkdir /tmp/haha
touch /tmp/haha/{1..10}

在group1的远程机器里都执行master上的/tmp/1.sh脚本（此脚本不用给执行权限)
master# ansible group1 -m script -a '/tmp/1.sh'
```



**扩展:** 使用shell脚本实现在group1的被管理机里的mariadb里创建一个abc库

~~~powershell
#!/bin/bash

yum install mariadb-server -y  &> /dev/null

systemctl start mariadb
systemctl enable mariadb

mysql << EOF
create database abc;
quit
EOF

把上面的脚本使用script模块在group1被管理机里执行即可
~~~



### command与shell模块

两个模块都是用于执行linux命令的,这对于命令熟悉的工程师来说，用起来非常high。

shell模块与command模块差不多（command模块不能执行一些类似$HOME,>,<,|等符号，但shell可以)

https://docs.ansible.com/ansible/latest/modules/command_module.html

https://docs.ansible.com/ansible/latest/modules/shell_module.html

```powershell
master# ansible -m command group1 -a "useradd user2"
master# ansible -m command group1 -a "id user2"

master# ansible -m command group1 -a "cat /etc/passwd |wc -l"		--报错
master# ansible -m shell group1 -a "cat /etc/passwd |wc -l"		--成功

master# ansible -m command group1 -a "cd $HOME;pwd"	　　--报错
master# ansible -m shell 　group1 -a "cd $HOME;pwd"	　　--成功
```

**注意:** shell模块并不是百分之百任何命令都可以,比如vim或ll别名就不可以。不建议大家去记忆哪些命令不可以，大家只要养成任何在生产环境里的命令都要先在测试环境里测试一下的习惯就好。


