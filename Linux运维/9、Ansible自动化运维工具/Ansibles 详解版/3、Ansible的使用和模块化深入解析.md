**Ansible的使用和模块化深入解析**

# **安装**

```
rpm包安装: EPEL源
    yum install ansible
编译安装:
    yum -y install python-jinja2 PyYAML python-paramiko python-babel
    python-crypto
    tar xf ansible-1.5.4.tar.gz
    cd ansible-1.5.4
    python setup.py build
    python setup.py install
    mkdir /etc/ansible
    cp -r examples/* /etc/ansible
Git方式:
    git clone git://github.com/ansible/ansible.git --recursive
    cd ./ansible
    source ./hacking/env-setup
pip安装： pip是安装Python包的管理器，类似yum
    yum install python-pip python-devel
    yum install gcc glibc-devel zibl-devel rpm-bulid openssl-devel
    pip install --upgrade pip
    pip install ansible --upgrade
确认安装：
    ansible --version
```

# **相关文件**

```
配置文件
    /etc/ansible/ansible.cfg  主配置文件,配置ansible工作特性(一般无需修改)
    /etc/ansible/hosts        主机清单(将被管理的主机放到此文件)
    /etc/ansible/roles/       存放角色的目录
程序
    /usr/bin/ansible          主程序，临时命令执行工具
    /usr/bin/ansible-doc      查看配置文档，模块功能查看工具
    /usr/bin/ansible-galaxy   下载/上传优秀代码或Roles模块的官网平台
    /usr/bin/ansible-playbook 定制自动化任务，编排剧本工具
    /usr/bin/ansible-pull     远程执行命令的工具
    /usr/bin/ansible-vault    文件加密工具
    /usr/bin/ansible-console  基于Console界面与用户交互的执行工具
```

# **主机清单inventory**

```
Inventory 主机清单
1> ansible的主要功用在于批量主机操作，为了便捷地使用其中的部分主机，可以在inventory file中将其分组命名 
2> 默认的inventory file为/etc/ansible/hosts
3> inventory file可以有多个，且也可以通过Dynamic Inventory来动态生成
/etc/ansible/hosts文件格式
inventory文件遵循INI文件风格，中括号中的字符为组名。
可以将同一个主机同时归并到多个不同的组中；
此外，当如若目标主机使用了非默认的SSH端口，还可以在主机名称之后使用冒号加端口号来标明
  
      ntp.magedu.com   不分组,直接加
    
    [webservers]     webservers组
    www1.magedu.com:2222  可以指定端口
    www2.magedu.com
    
    [dbservers]
    db1.magedu.com
    db2.magedu.com
    db3.magedu.com
如果主机名称遵循相似的命名模式，还可以使用列表的方式标识各主机
示例：
    [websrvs]
    www[1:100].example.com   ip: 1-100
    
    [dbsrvs]
    db-[a:f].example.com     dba-dbff
```

## **示例：准备三台主机，在ansible主机上，使用-m ping 通另外两台主机****（底层连接协议使用的是ssh)**

```
1、将server1和server2的ip写入Ansible程序的主机清单
vim /etc/ansible/hosts
192.168.23.17
192.168.23.24
2、使用ansible 192.168.23.17 -m ping -k  
[root@server1 ~]# ansible 192.168.23.17 -m ping -k
SSH password: 
192.168.23.17 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    }, 
    "changed": false, 
    "ping": "pong"
}
3、也可以多台主机同时 -m ping ,但是需要这些主机的password都是相同的，因为它
    只需要用户输入一次密码，然后拿这个密码去验证所有的主机
        -但是所有的主机同一个密码，非常危险，所以可以使用密钥对登录，这样就可以不用输入密码了；
        可以使用：ansible all -m ping ,但不可用于首次连接
[root@server1 ~]# ansible 192.168.23.24,192.168.23.17 -m ping -k
4、如果想-m ping  主机清单中所有的主机，可以使用all
[root@server1 ~]# ansible all -m ping -k

5、如果多台主机在同一个组中，直接写组名，也可以：
[root@server1 ~]# vim /etc/ansible/hosts
    [server]
    192.168.23.23
    192/168.23.17
#结束
[root@server1 ~]# ansible server -m ping -k
```

# **ansible 配置文件**

```
Ansible 配置文件/etc/ansible/ansible.cfg （一般保持默认）
vim /etc/ansible/ansible.cfg
[defaults]
#inventory     = /etc/ansible/hosts      # 主机列表配置文件
#library       = /usr/share/my_modules/  # 库文件存放目录
#remote_tmp    = $HOME/.ansible/tmp      # 临时py命令文件存放在远程主机目录
#local_tmp     = $HOME/.ansible/tmp      # 本机的临时命令执行目录  
#forks         = 5                       # 默认并发数,同时可以执行5次
#sudo_user     = root                    # 默认sudo 用户
#ask_sudo_pass = True                    # 每次执行ansible命令是否询问ssh密码
#ask_pass      = True                    # 每次执行ansible命令是否询问ssh口令
#remote_port   = 22                      # 远程主机的端口号(默认22)
建议优化项： 
host_key_checking = False               # 检查对应服务器的host_key，建议取消注释
log_path=/var/log/ansible.log           # 日志文件,建议取消注释
module_name   = command                 # 默认模块
```

# **ansible系列命令**

```
Ansible系列命令
    ansible ansible-doc ansible-playbook ansible-vault ansible-console
    ansible-galaxy ansible-pull
ansible-doc: 显示模块帮助
    ansible-doc [options] [module...]
        -a            显示所有模块的文档
        -l, --list    列出可用模块
        -s, --snippet 显示指定模块的playbook片段(简化版,便于查找语法)
示例：
    ansible-doc -l      列出所有模块
    ansible-doc ping    查看指定模块帮助用法
    ansible-doc -s ping 查看指定模块帮助用法
```

# **ansible命令**

**ansible +被管理的主机(ALL) +模块  +参数**

```
ansible通过ssh实现配置管理、应用部署、任务执行等功能，
建议配置ansible端能基于密钥认证的方式联系各被管理节点
ansible <host-pattern> [-m module_name] [-a args]
ansible +被管理的主机(ALL) +模块  +参数
    --version              显示版本
    -m module              指定模块，默认为command
    -v                     详细过程 –vv -vvv更详细
    --list-hosts           显示主机列表，可简写 --list
    -k, --ask-pass         提示输入ssh连接密码,默认Key验证
    -C, --check            检查，并不执行
    -T, --timeout=TIMEOUT  执行命令的超时时间,默认10s
    -u, --user=REMOTE_USER 执行远程执行的用户
    -b, --become           代替旧版的sudo切换
        --become-user=USERNAME 指定sudo的runas用户,默认为root
    -K, --ask-become-pass  提示输入sudo时的口令
```

```
ansible all --list  列出所有主机
ping模块: 探测网络中被管理主机是否能够正常使用  走ssh协议
          如果对方主机网络正常,返回pong
ansible-doc -s ping   查看ping模块的语法 
检测所有主机的网络状态
1>  默认情况下连接被管理的主机是ssh基于key验证,如果没有配置key,权限将会被拒绝
    因此需要指定以谁的身份连接,输入用户密码,必须保证被管理主机用户密码一致
    ansible all -m ping -k
2> 或者实现基于key验证 将公钥ssh-copy-id到被管理的主机上 , 实现免密登录
   ansible all -m ping
```

# **ansible的Host-pattern**

```
ansible的Host-pattern
匹配主机的列表
    All ：表示所有Inventory中的所有主机
        ansible all –m ping
    * :通配符
        ansible "*" -m ping  (*表示所有主机)
        ansible 192.168.1.* -m ping
        ansible "*srvs" -m ping
    或关系 ":"
        ansible "websrvs:appsrvs" -m ping
        ansible “192.168.1.10:192.168.1.20” -m ping
    逻辑与 ":&"
        ansible "websrvs:&dbsrvs" –m ping
        在websrvs组并且在dbsrvs组中的主机
    逻辑非 ":!"
        ansible 'websrvs:!dbsrvs' –m ping
        在websrvs组，但不在dbsrvs组中的主机
        注意：此处为单引号
    综合逻辑
        ansible 'websrvs:dbsrvs:&appsrvs:!ftpsrvs' –m ping
    正则表达式
        ansible "websrvs:&dbsrvs" –m ping
        ansible "~(web|db).*\.magedu\.com" –m ping
```

# **ansible命令执行过程**

# ansible使用示例

```
示例
    以wang用户执行ping存活检测
        ansible all -m ping -u wang -k
    以wang sudo至root执行ping存活检测
        ansible all -m ping -u wang -k -b
    以wang sudo至mage用户执行ping存活检测
        ansible all -m ping -u wang -k -b --become-user=mage
    以wang sudo至root用户执行ls
        ansible all -m command -u wang -a 'ls /root' -b --become-user=root -k -K
ansible ping模块测试连接
    ansible 192.168.38.126,192.168.38.127 -m ping -k
```

# ansible常用模块

```
模块文档：https://docs.ansible.com/ansible/latest/modules/modules_by_category.html
Command：在远程主机执行命令，默认模块，可忽略-m选项
    > ansible srvs -m command -a 'service vsftpd start'
    > ansible srvs -m command -a 'echo adong |passwd --stdin 123456'
此命令不支持 $VARNAME < > | ; & 等,用shell模块实现
    chdir:   进入到被管理主机目录
    creates: 如果有一个目录是存在的,步骤将不会运行Command命令
    ansible websrvs -a 'chdir=/data/ ls'
Shell：和command相似，用shell执行命令
    > ansible all -m shell  -a 'getenforce'  查看SELINUX状态
    >  ansible all -m shell  -a "sed -i 's/SELINUX=.*/SELINUX=disabled' /etc/selinux/config"
    > ansible srv -m shell -a 'echo magedu |passwd –stdin wang'
      
    调用bash执行命令 类似 cat /tmp/stanley.md | awk -F'|' '{print $1,$2}' &> /tmp/example.txt     
    这些复杂命令，即使使用shell也可能会失败，
    解决办法：写到脚本时，copy到远程执行，再把需要的结果拉回执行命令的机器
    修改配置文件,使shell作为默认模块    
        vim /etc/ansible/ansible.cfg
        module_name = shell
Script：在远程主机上运行ansible服务器上的脚本
    > -a "/PATH/TO/SCRIPT_FILE"
    > ansible websrvs -m script -a /data/test.sh
Copy：从主控端复制文件到远程主机
      src : 源文件  指定拷贝文件的本地路径  (如果有/ 则拷贝目录内容,比拷贝目录本身)
      dest: 指定目标路径
      mode: 设置权限
      backup: 备份源文件
      content: 代替src  指定本机文件内容,生成目标主机文件
      
      > ansible websrvs -m copy -a "src=/root/test1.sh dest=/tmp/test2.sh owner=wang mode=600 backup=yes"
        如果目标存在，默认覆盖，此处指定先备份
      > ansible websrvs -m copy -a "content='test content\nxxx' dest=/tmp/test.txt"
        指定内容，直接生成目标文件
Fetch：从远程主机提取文件至主控端，copy相反，目前不支持目录,可以先打包,再提取文件 （fetch：拿来）
     > ansible websrvs -m fetch -a 'src=/root/test.sh dest=/data/scripts'
     会生成每个被管理主机不同编号的目录,不会发生文件名冲突
     
     > ansible all -m shell -a 'tar jxvf test.tar.gz /root/test.sh'
     > ansible all -m fetch -a 'src=/root/test.tar.gz dest=/data/'
File：设置文件属性
    path: 要管理的文件路径 (强制添加)
    recurse: 递归,文件夹要用递归
    src:  创建硬链接,软链接时,指定源目标,配合'state=link' 'state=hard' 设置软链接,硬链接
    state: 状态
          absent 缺席,删除
          
    > ansible websrvs -m file -a 'path=/app/test.txt state=touch'       创建文件
    > ansible websrvs -m file -a "path=/data/testdir state=directory"   创建目录    
    > ansible websrvs -m file -a "path=/root/test.sh owner=wang mode=755"  设置权限755
    > ansible websrvs -m file -a 'src=/data/testfile dest=/data/testfile-link state=link' 创建软链接
    
    
unarchive：解包解压缩，有两种用法：
    1、将ansible主机上的压缩包传到远程主机后解压缩至特定目录，设置copy=yes.
    2、将远程主机上的某个压缩包解压缩到指定路径下，设置copy=no
    常见参数：
        copy：默认为yes，当copy=yes，拷贝的文件是从ansible主机复制到远程主机上，
              如果设置为copy=no，会在远程主机上寻找src源文件
        src： 源路径，可以是ansible主机上的路径，也可以是远程主机上的路径，
              如果是远程主机上的路径，则需要设置copy=no
        dest：远程主机上的目标路径
        mode：设置解压缩后的文件权限
    
    示例：
        ansible websrvs -m unarchive -a 'src=foo.tgz dest=/var/lib/foo'  
          #默认copy为yes ,将本机目录文件解压到目标主机对应目录下
        ansible websrvs -m unarchive -a 'src=/tmp/foo.zip dest=/data copy=no mode=0777'
          # 解压被管理主机的foo.zip到data目录下, 并设置权限777
        ansible websrvs -m unarchive -a 'src=https://example.com/example.zip dest=/data copy=no'
Archive：打包压缩  （archive：档案）
    > ansible all -m archive -a 'path=/etc/sysconfig dest=/data/sysconfig.tar.bz2 format=bz2 owner=wang mode=0777'
    将远程主机目录打包 
        path:   指定路径
        dest:   指定目标文件
        format: 指定打包格式
        owner:  指定所属者
        mode:   设置权限
Hostname：管理主机名
    ansible appsrvs -m hostname -a "name=app.adong.com"  更改一组的主机名
    ansible 192.168.38.103 -m hostname -a "name=app2.adong.com" 更改单个主机名
Cron：计划任务
    支持时间：minute,hour,day,month,weekday
    >ansible all -m cron  -a "minute=* job='/usr/bin/wall FBI WARING' name=waring" 
    #创建计划任务，每分钟警告一次
    >ansible all -m cron  -a "disabled=true  job='/usr/bin/wall FBI WARING' name=waring"
    #禁用这个任务，只是在crontab -e 中加入注释
    > ansible websrvs -m cron -a "minute=*/5 job='/usr/sbin/ntpdate 172.16.0.1 &>/dev/null' name=Synctime" 
    创建任务
    > ansible websrvs -m cron -a 'state=absent name=Synctime' 
    删除任务
    > ansible websrvs -m cron -a 'minute=*/10 job='/usr/sbin/ntpdate 172.30.0.100" name=synctime disabled=yes'
    注释任务,不在生效
Yum：管理包
    ansible websrvs -m yum -a 'list=httpd'  查看程序列表
    
    ansible websrvs -m yum -a 'name=httpd state=present' 安装
    ansible websrvs -m yum -a 'name=httpd state=absent'  删除
    可以同时安装多个程序包
    
Service：管理服务
    ansible srv -m service -a 'name=httpd state=stopped'  停止服务
    ansible srv -m service -a 'name=httpd state=started enabled=yes' 启动服务,并设为开机自启
    ansible srv -m service -a 'name=httpd state=reloaded'  重新加载
    ansible srv -m service -a 'name=httpd state=restarted' 重启服务
User：管理用户
    home   指定家目录路径
    system 指定系统账号
    group  指定组
    remove 清除账户
    shell  指定shell类型
    
    ansible websrvs -m user -a 'name=user1 comment="test user" uid=2048 home=/app/user1 group=root'
    ansible websrvs -m user -a 'name=sysuser1 system=yes home=/app/sysuser1'
    ansible websrvs -m user -a 'name=user1 state=absent remove=yes'  清空用户所有数据
    ansible websrvs -m user -a 'name=app uid=88 system=yes home=/app groups=root shell=/sbin/nologin password="$1$zfVojmPy$ZILcvxnXljvTI2PhP2Iqv1"'  创建用户
    ansible websrvs -m user -a 'name=app state=absent'  不会删除家目录
    
    安装mkpasswd 
    yum insatll expect 
    mkpasswd 生成口令
    openssl passwd -1  生成加密口令
    
删除用户及家目录等数据
    Group：管理组
        ansible srv -m group -a "name=testgroup system=yes"   创建组
        ansible srv -m group -a "name=testgroup state=absent" 删除组
```

# ansible系列命令2

```
可以通过网上写好的
ansible-galaxy
    > 连接 https://galaxy.ansible.com 
      下载相应的roles(角色)
    
    > 列出所有已安装的galaxy
        ansible-galaxy list
    
    > 安装galaxy
        ansible-galaxy install geerlingguy.redis
    
    > 删除galaxy
        ansible-galaxy remove geerlingguy.redis
        
ansible-pull
    推送命令至远程，效率无限提升，对运维要求较高
    
ansible-playbook  可以引用按照标准的yml语言写的脚本
    执行playbook
    示例：ansible-playbook hello.yml
        cat hello.yml
        #hello world yml file
        - hosts: websrvs    #- 和host 之间有空格
          remote_user: root    #remote,远程；remote和上面的hosts要上下对其
          tasks:
            - name: hello world
              command: /usr/bin/wall hello world
ansible-vault  (了解)    #vault:密室
功能：管理加密解密yml文件
    ansible-vault [create|decrypt|edit|encrypt|rekey|view]
        ansible-vault encrypt hello.yml 加密        #encrypt,加密
        ansible-vault decrypt hello.yml 解密
        ansible-vault view hello.yml    查看
        ansible-vault edit hello.yml    编辑加密文件
        ansible-vault rekey hello.yml   修改口令
        ansible-vault create new.yml    创建新文件
Ansible-console：2.0+新增，可交互执行命令，支持tab  (了解)
    root@test (2)[f:10] $
    执行用户@当前操作的主机组 (当前组的主机数量)[f:并发数]$
    设置并发数：         forks n   例如： forks 10
    切换组：             cd 主机组 例如： cd web
    列出当前组主机列表： list
    列出所有的内置命令： ?或help
    示例：
        root@all (2)[f:2]$ list
        root@all (2)[f:2]$ cd appsrvs
        root@all (2)[f:2]$ forks 5 #更改并发数量
        root@appsrvs (2)[f:5]$ list
        root@appsrvs (2)[f:5]$ yum name=httpd state=present
        root@appsrvs (2)[f:5]$ service name=httpd state=started
```