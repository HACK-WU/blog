# **MyPaginator类对象源码**

```
from django.core.paginator import Paginator, EmptyPage
class MyPaginator():
    def __init__(self):
        self.page_range=None
        self.current_page=None
    def createPages(self,request,queryset_obj,content_num,page_num=5):
        num=request.GET.get("page")
        if not num: #如果num为空
            num="1"
        num=int(num)
        paginator=Paginator(queryset_obj,content_num)
        self.page_range=paginator.page_range  #获取到分页范围
        try:
            self.current_page=paginator.page(num)    #获取到当前的分页内容
        except EmptyPage:
            self.current_page=paginator.page(1)      #如果出错默认显示第一页的内容
            num=1
        if page_num%2==0:
            page_num-=1
        left_num=page_num//2
        if paginator.num_pages>page_num:
            right=num+left_num+1
            left=num-left_num
            if right>paginator.num_pages:
                right=paginator.num_pages+1
                left=right-page_num
            if left<=0:
                left=1
                right=page_num+1
            self.page_range=range(left,right)
        dic={
            "current_page":self.current_page,
            "page_range":self.page_range,
            "num":num,
        }
        return dic      #返回当前页面和页面的范围
```

# **自定义分页器HTML框架**

## **content.html**

```
<link href="https://cdn.bootcss.com/twitter-bootstrap/3.4.1/css/bootstrap.min.css" rel="stylesheet">
<script src="https://cdn.bootcss.com/jquery/3.3.1/jquery.min.js"></script>
<script src="https://cdn.bootcss.com/twitter-bootstrap/3.4.1/js/bootstrap.min.js"></script>
<!--***********************展示主要的数据内容#####################-->
{% for item in current_page %}    <!--这里必须是current_page,item可以根据需求自己更改-->
    <!--这里是展示item数据的内容-->
      <!--自行编写数据内容-->
{% endfor%}
<!--***********************展示分页器的页码，不需要做任何更改，即可使用#####################-->
{% include "mytemplates/page_numbers" %}
```

## **vews.py**

```
def home(request):
    #查询本网站的所有的文章数据展示到前端页面，这里可以使用分页器
    article_query_set=models.Article.objects.all().order_by("id")
    myPaginator=MyPaginator()
    dic=myPaginator.createPages(request,article_query_set,3)
    locals().update(dic)
    return render(request,"home.html",locals())
```

## **mytemplates/page_numbers.html**

```
<!--***********************展示分页器的页码，不需要做任何更改，即可使用#####################-->
<nav aria-label="Page navigation">
    <ul class="pagination">
        <li>                        <!--显示上一页-->
            {% if current_page.has_previous %}
                <a href="?page={{ current_page.previous_page_number }}" aria-label="Previous">
            {% else %}
                <a class="disabled" href="" aria-label="Previous">
            {% endif %}
            <span aria-hidden="true">上一页</span>
            </a>
        </li>
        {% for item in  page_range %}   <!--显示中间的页码-->
            {% if num == item %}
                <li class="active"><a href="?page={{ item }}">{{ item }}</a> </li>
            {% else %}
                <li ><a href="?page={{ item }}">{{ item }}</a> </li>
            {% endif %}
        {% endfor %}
        <li>                        <!--显示下一页-->
            {% if current_page.has_next %}  <!--判断当前页面是否有下一页-->
                <a href="?page={{ current_page.next_page_number }}" aria-label="Next">  <!--有则加上下一页的链接-->
            {% else %}
                <a  class="disabled" href="" aria-label="Next">     <!--没有则链接失效-->
            {% endif %}
            <span aria-hidden="true">下一页</span>
            </a>
        </li>
    </ul>
</nav>
```