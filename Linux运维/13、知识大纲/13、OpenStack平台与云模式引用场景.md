# 第一章：掌握 OpenStack 企业级云计算平台及 IaaS、PaaS、SaaS 云模式应用场景

1. Openstack 企业级云平台介绍

1. 详细对比 IaaS、PaaS 和 SaaS 云平台功能及用处

1. OpenStack 概念架构模型

1. 详解 Keystone、Glance、Nova、Neutron、Cinder、Heat

1. 详解 Ceilometer、Trove、Ironic、Swift 及 Horizon 等各组件

1. 理清 OpenStack 的 概念模型、OpenStack 各服务的组件概念，各服务间的协作流程

1. OpenStack 常用安装方式介绍

1. OpenStack 的生产环境部署模型及实验模型

1. 部署硬件需求及注意事项

1. 企业面试题

# 第二章：掌握 OpenStack 认证服务 KeyStone

1. OpenStack 认证服务 KeyStone 详解

1. KeyStone 认证名词（User，Project，Token，Role，Service，KeyStone）

1. KeyStone 认证服务部署

1. KeyStone 连接方式（KeyStone -os-username -os-password）

1. 创建 default 域、admin 项目、admin 用户和 admin 角色

1. KeyStone demo 与 service 项目

1. KeyStone 服务注册

1. 企业面试题

# 第三章：掌握 OpenStack 镜像服务 Glance

1. OpenStack 镜像服务 Glance 介绍及部署

1. glanceapi 与 glance-registry 配置文件功能详解

1. KeySton_authtoken 模块功能及设置

1. KeyStone 设置服务注册

1. 企业面试题

# 第四章：掌握 OpenStack 计算服务 Nova 与 RabbitMQ

1. OpenStack 计算服务 Nova 功能介绍

1. Nova 插件 API、Cert、Scheduler、Conductor、Consoleauth、Navncproxy

1. Nova 之 RabbitMQ 消息队列配置

1. Nova 配置文件详解及创建 Nova 服务

1. 企业面试题

# 第五章：掌握 OpenStack 网络服务 Neutron

1. OpenStack 网络服务 Neutron 功能介绍

1. OpenStack Neutron 架构介绍

1. Neutron 网络配置（公共网络与私有网络）

1. Neutron 插件之 ML2 配置

1. Linuxbridge 代理对接虚拟网络与公共物理网络

1. 网络服务 nova-api 配置

1. 配置 Neutron 计算节点

1. 企业面试题

# 第六章：掌握 OpenStack 云主机创建、使用、管理

1. 创建 m1.nano 规格主机及注意事项

1. 增加云主机安全组

1. 云主机安全组规则设置

1. 创建实例及实例分配

1. 云主机使用

1. 云主机删除

1. 企业面试题

# 第七章：掌握 OpenStack 图形化 Dashboard

1. OpenStack 图形化 Dashboard 功能介绍

1. Dashboard 创建虚拟机流程介绍

1. 图形界面输入用户名密码到 keyston 进行认证

1. 创建虚拟机的请求转换给 REST API 并发送给 NOVA API

1. token 到 keyston 查询是否合法

1. NOV api 和数据库进行交互

1. RabbitMQ 消息发送至 nova scheduler

1. nova scheduler 算法确认

1. nova scheduler 数据库交互写入数据

1. nova computer 创建虚拟机

1. nova conductor 代替 nova computer

1. nova conductor 创建虚拟机

1. glance 获取镜像

1. lace 联系 keyston 进行认证

1. 联系 neutron 获取网络

1. neutron 联系 keyston 进行认证

1. nova computer 再联系 cinder 获取磁盘

1. cinder 再到 keyston 进行认证

1. mova computer 调用 KVM 创建虚拟机

1. 企业面试题

# 第八章：掌握 Ceph 分布式块存储，Ceph 集群部署，Ceph 存储管理

1. 分布式文件系统介绍

2. Ceph 存储概述，对象存储-文件存储-块存储

3. Ceph 存储架构及组件介绍

4. Ceph-deploy 部署

5. 部署 Ceph 块存储集群

6. 镜像创建，动态调整容量大小

7. 客户端 KRBD 访问存储

8. 创建存储镜像快照

9. 使用快照恢复数据

10. 存储快照克隆

11. 客户都安磁盘映射撤销

12. 删除快照与镜像

13. 企业面试题

# 第九章：掌握 Ceph 文件存储，账号认证，Ceph 存储的元数据，存储池

1. 创建虚拟机磁盘镜像

1. Ceph 认证账号

1. Ceph 元数据服务器部署

1. 创建 Ceph 存储池

1. 创建 Ceph 文件系统

6. 客户端挂载存储使用存储

7. 企业面试题

# 第十章：掌握 Ceph 对象存储

1. 对象存储概述

1. 部署 RGW 程序

1. 新建网关实例

1. 服务端端口修改

1. 客户端测试存储

1. 第三方软件访问存储

1. 企业面试题

# 第十一章：掌握 JumperServer 堡垒机管理企业集群服务器

1. JumpServer 堡垒机企业应用场景概述

1. JumpServer 架构介绍

1. JumpServer 部署

1. JumpServer 身份认证

1. JumpServer 账号管理

1. JumpServer 权限控制

1. JumpServer 审计

1. JumpServer 安全提升

1. JumpServer 大规模部署

1. 企业面试题

# 第十二章：掌握 OpenVPN 虚拟专用网络

1. OpenVPN 虚拟专用网络应用场景介绍

1. OpenVPN 服务器搭建及部署

1. 网络通信机制及证书环境初始化

1. 基于证书实现客户端登录

1. OpenVPN 服务器维护

6. 客户端证书快速签发

7. 客户端证书吊销

8. 企业面试

# 第十三章：掌握 VMware vSphere 企业虚拟化

1. VMware vSphere 虚拟化介绍

1. 构建 vSphere 虚拟化平台

1. 创建和部署 ESXi Server

1. vCenter Server 部署

1. 利用 vCenter 管理 ESXi

1. Virtual Machine 基础概念

1. Deploy From Template

1. Create Wizar 创建虚拟机

1. 企业面试题