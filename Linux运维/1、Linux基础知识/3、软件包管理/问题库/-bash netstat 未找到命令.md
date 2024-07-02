-bash: netstat: 未找到命令

在使用 netstat -ltunp 出现提示

-bash: netstat: 未找到命令

原因是没有安装这个依赖包

```javascript
yum -y install net-tools
```

