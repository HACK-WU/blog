api_settings 是 Django REST framework 中的一个模块，用于提供一些全局设置和默认配置。通过导入 api_settings，可以访问和修改这些全局设置，从而影响 Django REST framework 在整个应用中的行为。

api_settings 提供了一系列默认配置选项，包括认证类、权限类、分页类、过滤器类等等。这些默认配置可以在全局范围内定义，在整个应用中保持一致，同时也可以根据需要进行定制和覆盖。

例如，可以通过 api_settings.DEFAULT_RENDERER_CLASSES 来查看默认的渲染器类列表，通过 api_settings.DEFAULT_AUTHENTICATION_CLASSES 来查看默认的认证类列表。如果需要自定义这些默认配置，可以在项目的设置文件中进行覆盖。

总的来说，api_settings 提供了一个集中管理和访问默认配置选项的方式，使得开发者可以轻松地定制和调整 Django REST framework 的行为，以满足特定项目的需求。

例如，假设我们想在 Django REST framework 中使用自己定义的分页类 MyPagination，而不是默认的分页类。我们可以在全局范围内修改这个设置，使得整个应用都使用 MyPagination 来进行分页处理。

首先，在项目的设置文件中定义一个新的分页类：

```python
from rest_framework.pagination import PageNumberPagination
class MyPagination(PageNumberPagination):
    page_size = 10
    page_size_query_param = 'page_size'
    max_page_size = 100
```

然后，我们可以通过 api_settings.DEFAULT_PAGINATION_CLASS 来覆盖默认的分页类设置，将其替换为 MyPagination：

```python
from rest_framework.settings import api_settings
api_settings.DEFAULT_PAGINATION_CLASS = 'my_app.pagination.MyPagination'
```

这样，无论在哪个视图中使用分页功能，都会自动调用 MyPagination 类来进行分页处理，而不是默认的分页类。同时，我们也可以在特定的视图中覆盖这个设置，使用不同的分页类来满足特定的需求。