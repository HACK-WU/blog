## xadmin后台管理

安装：luffy虚拟环境下

```shell
# 适配的版本为Django2.x
# 相应的drf版本应该为3.10.x
pip install https://codeload.github.com/sshwsfc/xadmin/zip/django2
```

注册app：dev.py

```python
INSTALLED_APPS = [
    # ...
    # xadmin主体模块
    'xadmin',
    # 渲染表格模块
    'crispy_forms',
    # 为模型通过版本控制，可以回滚数据
    'reversion',
]
```

xadmin：需要自己的数据库模型类，完成数据库迁移

```python
python manage.py makemigrations
python manage.py migrate
```

设置主路由替换掉admin：主urls.py

```python
# xadmin的依赖
import xadmin
xadmin.autodiscover()
# xversion模块自动注册需要版本控制的 Model
from xadmin.plugins import xversion
xversion.register_models()
urlpatterns = [
    # ...
    path(r'xadmin/', xadmin.site.urls),
]
```

创建超级用户：大luffyapi路径终端

```python
# 在项目根目录下的终端
python manage.py createsuperuser
# 账号密码设置：admin | Admin123
```

完成xadmin全局配置：新建home/adminx.py

```python
# home/adminx.py
# xadmin全局配置
import xadmin
from xadmin import views
class GlobalSettings(object):
    """xadmin的全局配置"""
    site_title = "路飞学城"  # 设置站点标题
    site_footer = "路飞学城有限公司"  # 设置站点的页脚
    menu_style = "accordion"  # 设置菜单折叠
xadmin.site.register(views.CommAdminView, GlobalSettings)
```

在adminx.py中注册model：home/adminx.py

```python
from . import models
# 如果某个model想要被xadmin管理，那么也需要在xadmin中进行注册。
xadmin.site.register(models.Banner)
```