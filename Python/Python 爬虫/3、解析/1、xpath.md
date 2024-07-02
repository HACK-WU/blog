**xpath总结**

# 1、xpath

```shell
xpath使用：
    注意：提前安装xpath插件    
    （1）打开chrome浏览器    
    （2）点击右上角小圆点    
    （3）更多工具    
    （4）扩展程序    
    （5）拖拽xpath插件到扩展程序中    
    （6）如果crx文件失效，需要将后缀修改zip    
    （7）再次拖拽    
    （8）关闭浏览器重新打开    
    （9）ctrl + shift + x    
    （10）出现小黑框    
1.安装lxml库          
    pip install lxml ‐i https://pypi.douban.com/simple            
2.导入lxml.etree      
    from lxml import etree            
3.etree.parse()   解析本地文件    
    html_tree = etree.parse('XX.html')           
4.etree.HTML()    服务器响应文件    
    html_tree = etree.HTML(response.read().decode('utf‐8')           
4.html_tree.xpath(xpath路径)
```

## 2、xpath基本语法

```shell
xpath基本语法：
1.路径查询    
    //：查找所有子孙节点，不考虑层级关系        
    / ：找直接子节点        
2.谓词查询    
    //div[@id]                  #div中含有id属性的标签

    //div[@id="maincontent"]    #div中id="maincontent"的标签        
3.属性查询    
    //@class          #查看class的属性值
4.模糊查询    
    //div[contains(@id, "he")]       #div中，id属性值含有"he"的标签        
    //div[starts‐with(@id, "he")]    #div中，id属性值以"he"开头的标签
5.内容查询    
    //div/h1/text()            #div中的子标签中的h1的文本内容
6.逻辑运算    
    //div[@id="head" and @class="s_down"]        #div中id="head"并且class="s_down"的标签
    //title | //price         #查找title标签或者price标签
```

- 例子

```
tree=etree.parse("01xpath的基本使用.html")
# print(tree)
# li_list=tree.xpath("//body/ul/li")
#
# #判断列表的长度
# print(len(li_list))
# 查找所有的有id的li标签
# li_list=tree.xpath("//body/ul/li[@id]/text()")
#查找id=l1的li标签
# li_list=tree.xpath("//ul/li[@id='l1']/text()")
#查找到id=l1的li标签的class的属性值
# li=tree.xpath("//ul/li[@id='l1']/@class")
# 查询id中包含l的li标签
# li=tree.xpath("//ul/li[contains(@id,'l')]/text()")
#查询id值以l开头的li标签
# li_list=tree.xpath("//ul/li[starts-with(@id,'l')]/text()")
# 查询id=li并且class="c1" 的li标签
# li_list=tree.xpath("//ul/li[@id='l1' and @class='c1']/text()")
# 查找
li_list=tree.xpath("//ul/li[@id='l1']/text() | //ul/li[@id='l2']/text()")
print(li_list)
print(len(li_list))
```

应用案例： 1.站长素材图片抓取并且下载（[http://sc.chinaz.com/tupian/shuaigetupian.html](http://sc.chinaz.com/tupian/shuaigetupian.html)）--》懒加载

# 3、应用实例

## 1）获取百度首页的“百度一下”四个字

```
# _*_ coding : utf-8 _*_
# @Time : 2022/5/21 22:46
# @Author : HackWu
# @File : 02百度网站的百度一下
# @Project : 解析
import  urllib.request
import  urllib.parse
from lxml import etree
url="http://www.baidu.com"
# urllib.request.urlretrieve(url,"baidu.html")
headers={
    "User-Agent":"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/98.0.4758.102 Safari/537.36",
}
request=urllib.request.Request(url=url,headers=headers)
response=urllib.request.urlopen(request)
content=response.read().decode("utf-8")
# 解析源码 来获取我们想要的数据
# 解析服务器响应的文件
tree=etree.HTML(content)
#获取想要的数据
result=tree.xpath("//input[@id='su']/@value")   #返回值是一个列表list
print(result)
```

## 2）爬取站长素材前5页的所有的美女图片

```
# _*_ coding : utf-8 _*_
# @Time : 2022/5/21 23:03
# @Author : HackWu
# @File : 03站长素材
# @Project : 解析
'''
    Request URL: https://sc.chinaz.com/tupian/meinvtupian.html  第一页
    Request URL: https://sc.chinaz.com/tupian/meinvtupian_2.html 第二页
    实验获取到站长素材前5页美女图片
    步骤：
        1、现获取到每一页的源码
        2、在源码中找到每个图片的下载地址
        3、下载图片
    注意点：图片的懒加载，
        就是当一个页面的需要加载大量的图片时，为了减少服务器的消耗，
        前端页面不会一次性向服务器请求所有的图片到前端，
        比较电脑屏幕就这么大，全拿来了，你也看不到啊，
        所以一般情况下只请求一部分图片，就是你一开始就能看到的那一部分。
        然后等你不断下滑页面时，在请求接下里的图片。
        这就是图片的懒加载
        那么本页面是如何做到图片懒加载的呢？
            原来是一开始img标签使用的是src2='xxxxx.jpg',并不是src.
            但是src2是无效的属性，并不会对后面的地址发起请求。
            然后当需要加载那个页面时，就会把src2变为src,用这样的方式，实现对图片的
            实时请求，也就是懒加载。
'''
import urllib.request
from lxml import  etree
def create_request(page):
    base_url="https://sc.chinaz.com/tupian/"
    if page==1:
        second_url="meinvtupian.html"
    else:
        second_url=f"meinvtupian_{page}.html"
    url=base_url+second_url
    headers={
        "User_Agent":"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/98.0.4758.102 Safari/537.36",
    }
    request=urllib.request.Request(url=url,headers=headers)
    return  request
def get_content(request):
    response=urllib.request.urlopen(request)
    content=response.read().decode("utf-8")
    return content
def down_image(content):
    tree=etree.HTML(content)
    alt_list=tree.xpath("//div[@id='container']//img/@alt")
    src_list=tree.xpath("//div[@id='container']//img/@src2")
    print(len(alt_list))
    print(len(src_list))
    for index in range(len(alt_list)):
        name=alt_list[index]
        url="http:"+src_list[index]
        # print(name+": "+url)
        urllib.request.urlretrieve(url,f"image/{name}.jpg")
if __name__ == '__main__':
    for page in range(2,6):
        request=create_request(page)
        content=get_content(request)
        down_image(content)
    print("下载完成！！！")
```