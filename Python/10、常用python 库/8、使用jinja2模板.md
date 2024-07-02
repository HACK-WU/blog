要在 Python 中使用 Jinja2 模板语法，您需要先安装 Jinja2 库。可以使用 pip 命令进行安装，具体命令如下：

```python
pip install Jinja2

```

安装完成后，就可以在 Python 代码中使用 Jinja2 模板了。以下是一个简单的示例：

```python
from jinja2 import Template

# 定义模板字符串
template_string = "Hello, {{ name }}!"

# 创建模板对象
template = Template(template_string)

# 渲染模板
result = template.render(name="World")

# 打印结果
print(result)

```

运行此代码，输出如下：

```
Hello, World!

```

在上面的示例中，我们首先导入 `Template` 类。然后，定义了一个模板字符串 `template_string`，其中包含了一个变量 `name`。接下来，创建了一个模板对象 `template`，并使用 `render` 方法渲染模板，并通过传递一个 `name` 参数来指定变量的值。最后，使用 `print` 函数打印渲染后的结果。

除了模板字符串，您还可以从文件中加载模板。假设您有一个名为 `template.html` 的文件，其中包含了模板内容，可以使用以下代码加载并渲染该模板：

```python
from jinja2 import Environment, FileSystemLoader

# 创建环境对象
env = Environment(loader=FileSystemLoader("/path/to/templates"))

# 加载模板
template = env.get_template("template.html")

# 渲染模板，默认使用的编码为utf-8
result = template.render(name="World")

# 打印结果,结果就是渲染之后的文本内容。
print(result)

#也可以使用字典的方式进行渲染：
context={
	"name":"hello world"
}
result = template.render(context)

print(result)


# 将渲染结果保存到新文件，并指定编码格式
output_file = "/path/to/output/output.html"
with open(output_file, "w", encoding="utf-8") as f:
    f.write(result)

```

请将 `/path/to/templates` 替换为包含模板文件的目录路径，`template.html` 替换为您的模板文件名称。

这只是一个简单的示例，Jinja2 还提供了更多高级功能和语法特性，如条件判断、循环等。您可以参考 Jinja2 的官方文档以了解更多信息：<https://jinja.palletsprojects.com/>

# [如何获取 jinja 2 模板中所有变量的列表](https://segmentfault.com/q/1010000043270205)

```python
env = Environment(loader=FileSystemLoader(ROOT_DIR+"/template"))
template = env.get_template("elasticsearch.yml")

template_source = env.loader.get_source(env, 'elasticsearch.yml')
parsed_content = env.parse(template_source)
print(meta.find_undeclared_variables(parsed_content))

>> {'es_data', 'http_post', 'is_master', 'transport_port', 'es_repo', 'es_logs', 'cluster_name', 'unicast_hosts', 'minimum_master_nodes'}
```

# Jinja2BaseMap

```python
# coding=utf-8
# @Author:wyp
# @FileName:baseMap.py

from jinja2 import Environment, FileSystemLoader, meta
from pathlib import Path


class JinJa2BaseMap:
    template_dir_path = None
    template_render_path = None
    template_name = None

    def __init__(self, context: dict):
        self.check()
        self.context = context
        self.env = Environment(loader=FileSystemLoader(self.template_dir_path))

    def check(self):
        template_file = f"{self.template_dir_path}/{self.template_name}"
        if not Path(template_file).exists():
            raise FileNotFoundError(f"The file '{template_file}' was not found.")

        if not Path(self.template_render_path).parent.exists():
            raise FileNotFoundError(f"The directory '{self.template_render_path}' was not found.")

    def get_var_list(self):
        """ Get all variables of the template_file file."""
        template_source = self.env.loader.get_source(self.env, self.template_name)
        parsed_content = self.env.parse(template_source)
        var_list = meta.find_undeclared_variables(parsed_content)
        return tuple(var_list)

    def render(self):
        """Generate a new file.
        Returns: The content of the new file.
        """
        for item in self.get_var_list():
            if item not in self.context:
                raise ValueError(f"{self.context} don't have the key '{item}'.")

        template = self.env.get_template(self.template_name)
        content = template.render(self.context)
        with open(self.template_render_path, mode="wt", encoding="utf-8") as f:
            f.write(content)
        return content

```

