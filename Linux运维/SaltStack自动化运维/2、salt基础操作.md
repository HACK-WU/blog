# salt-key 的使用方式

```
salt-key -L   #显示所有证书
salt-key -A   #接收所有证书
salt-key -a   [hostname]   #接收某个host的证书
salt-key -D    #删除所有证书
salt-key -d [hostname]  #删除某个host的证书，删除之后，想要在添加有时候可能需要重启minion才可以
```

# saltstack的管控方式

1. 调用saltstack提供的模块

1. 写sls状态

# 调用saltstack提供的模块

```
Usage: salt [options] '<target>' <function> [arguments]
```

![](images/WEBRESOURCE2614e350aec6070ba504ec8eccbb31ea截图.png)

![](images/WEBRESOURCE6bb947ac71d98f64f34d7c927364788f截图.png)

![](images/WEBRESOURCE171eea57a6573d4a424ab9bc9dfaf0f2截图.png)

# 常见模块模块应用

![](images/WEBRESOURCE9a17fe4f47d52367ac43ed6b4a5fc2de截图.png)

![](images/WEBRESOURCE254b71d0c2e90596e75432b1875eb6ec截图.png)

# sls状态文件

![](images/WEBRESOURCE54e58255725fe2ee35dc851cdace201c截图.png)

![](images/WEBRESOURCE145cd796bd7e536ddbe74fd7e1473120截图.png)

![](images/WEBRESOURCEc2a2ff2e04a7b66c5f6d6e81fe6a7b84截图.png)

![](images/WEBRESOURCE9db0c68ded3fac53e70d18bf0bf3bfbd截图.png)

![](images/WEBRESOURCE99bd5aff47e6c9d0012ccc9ac669d300截图.png)

![](images/WEBRESOURCE005be967211064d70789cba24639eb72截图.png)

![](images/WEBRESOURCEae2161e16e51544802a900d773db06a9截图.png)

![](images/WEBRESOURCEc34e682db89b8608dd8e6e40d1faf2f1截图.png)

![](images/WEBRESOURCE6253bda26f6065f419ac48925c6051ae截图.png)

# jinja模板

![](images/WEBRESOURCEb479cae577109265375777ecf05c78db截图.png)

![](images/WEBRESOURCEfae1dd32f43c80545fa251a3865cf692截图.png)