```
from bs4 import BeautifulSoup
def fun(request):
    content=request.POST.get("content")
    #模块使用
    #1、先生成一个对象
    soup=BeautifulSoup(content,"html.parser")
    #2、获取所有的标签
    tags=soup.find_all()
    for tag in tags:
        # print(tag.name) #获取所有的标签名
        # 针对scrip标签直接删除
        if tag.name=="script":
            #删除标签
            tag.decompose()
    content=str(soup)
```