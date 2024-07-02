```shell
[lang]
name=中文简体
[message]
applyLangTip           = 重启程序来应用更改。
runCommands            = 执行命令
[menu]
id               = 96
service          = 服务
help             = 帮助
officialSite     = 官网
officialHelp     = 帮助文档
[UI]
title          = 集成运行环境
stop           = 停止
startZentao    = 启动
account          = 账号
password         = 密码
```

# 插值表达式：

在使用configparser进行插值时，可以通过提供一个字典对象来将变量传递给配置解析器。具体步骤如下：

1. 创建一个 configparser

```python
import configparser
config = configparser.ConfigParser()
config.read('config.ini')
```

1. 在配置文件中使用插值表达式，例如：

```
[Log]
path = D:\python_project\es-ops\tmp_dir
level = DEBUG
format = [%(asctime)s][%(process)d][%(levelname)s] %(message)s
```

1. 定义一个字典对象，包含要传递给配置解析器的变量：

```python
variables = {
    'asctime': '2023-10-13 12:34:56',
    'process': 123,
    'levelname': 'INFO'
}
```

1. 使用字典对象作为使用字典对象作为vars参数传递给configparser对象的get()方法

```python
log_format = config.get('Log', 'format', vars=variables)
print(log_format)
```

这样，configparser会根据提供的字典对象进行插值替换，输出正确的日志格式。在以上示例中，输出结果是[2023-10-13 12:34:56][123][INFO] %(message)s。

通过这种方式，你可以灵活地将变量传递给配置文件中的插值表达式，并实现动态的配置值。