**OSPF身份验证**

# 1、认证方式

- Null

- 简单密码身份验证

- MD5身份验证

- 接口认证

- 区域认证

# 2、解决的问题

- 路由更新时是以明文的方式进行更新，所以当路由更新的数据包被其他第三方窃取，则可能会导致

一些安全问题，可以使用身份去解决这个问题

# 3、接口认证

- 需要进行身份验证的接口彼此都需要配置了相同的身份验证才可以通过

```
R3(config)#router ospf 1
R3(config-router)#area 1 authentication message-digest
R3(config-router)#int e0/1
R3(config-if)#ip ospf message-digest-key 2 md5 cisco
```