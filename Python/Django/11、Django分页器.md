**Django组件-分页器Paginator**

# **MyPaginator类对象源码**

```
from django.core.paginator import Paginator, EmptyPage
class MyPaginator():
    def __init__(self):
        self.page_range=None
        self.current_page=None
        self.num=None
    def createPages(self,request,queryset_obj,content_num=3):
        num=request.GET.get("page")
        if not num: #如果num为空
            num="1"
        num=int(num)
        paginator=Paginator(queryset_obj,content_num)
        self.page_range=paginator.page_range  #获取到分页范围
        if paginator.num_pages>11:
            right=num+6
            left=num-5
            if right>paginator.num_pages:
                right=paginator.num_pages
            if left<=0:
                left=1
            self.page_range=range(left,right)
        try:
            self.current_page=paginator.page(num)    #获取到当前的分页内容
        except EmptyPage as  e:
            self.current_page=paginator.page(1)      #如果出错默认显示第一页的内容
        self.num=num
        dic={
        "current_page":self.current_page,
        "page_range":self.page_range,
        "num":self.num
        }
    return dic   #返回当前页面和页面的范围
```

# **自定义分页器HTML框架**

## content.html

```
<link href="https://cdn.bootcss.com/twitter-bootstrap/3.4.1/css/bootstrap.min.css" rel="stylesheet">
<script src="https://cdn.bootcss.com/jquery/3.3.1/jquery.min.js"></script>
<script src="https://cdn.bootcss.com/twitter-bootstrap/3.4.1/js/bootstrap.min.js"></script>
<!--***********************展示主要的数据内容#####################-->
{% for item in current_page %}    
    <!--这里是展示item数据的内容-->
      <!--自行编写数据内容-->
{% endfor%}
<!--***********************展示分页器的页码，不需要做任何更改，即可使用#####################-->
{% include "mytemplates/page_numbers" %}
```

## mytemplates/page_numbers.html

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

# **1、view**

```
from  django.core.paginator import Paginator,EmptyPage
def add_user(request):
    user_list=User.objects.all()    #获取数据库内容
    
    num=request.GET.get("page")
    if not num: #如果num为空
        num="1"
    num=int(num)
    paginator=Paginator(user_list,5)
    print(paginator.count)
    print(paginator.num_pages)
    print(paginator.page_range)
    
    page_range=paginator.page_range
    if paginator.num_pages>11:
       right=num+6
       left=num-5
       if right>paginator.num_pages:
            right=paginator.num_pages
       if left<=0:
           left=1
       page_range=range(left,right)
    try:
        current_page=paginator.page(num)
    except EmptyPage as  e:
            current_page=paginator.page(1)    #如果
    return render(request,"index.html",locals())
```

# 2、index.htm

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/3.4.1/css/bootstrap.min.css" integrity="sha384-HSMxcRTRxnN+Bdg0JdbxYKrThecOKuH5zCYotlSAcp1+c8xmyTe9GYg1l9a69psu" crossorigin="anonymous">
<body>
     <!-网页主要显示的内容-->
    {%  for user in current_page %}
        <li>{{  user.name }}:{{ user.pwd }}</li>
    {% endfor %}
     <!--分页器-->
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
</div>
</body>
</html>
```

# 3、扩展

```
def index(request):


    book_list=Book.objects.all()

    paginator = Paginator(book_list, 15)
    page = request.GET.get('page',1)
    currentPage=int(page)

    #  如果页数十分多时，换另外一种显示方式
    if paginator.num_pages>11:

        if currentPage-5<1:
            pageRange=range(1,11)
        elif currentPage+5>paginator.num_pages:
            pageRange=range(currentPage-5,paginator.num_pages+1)

        else:
            pageRange=range(currentPage-5,currentPage+5)

    else:
        pageRange=paginator.page_range


    try:
        print(page)
        book_list = paginator.page(page)
    except PageNotAnInteger:
        book_list = paginator.page(1)
    except EmptyPage:
        book_list = paginator.page(paginator.num_pages)


    return render(request,"index.html",locals())
```