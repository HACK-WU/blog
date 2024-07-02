# 1、基本简介

```
1.BeautifulSoup简称：
     bs4
2.什么是BeatifulSoup？
BeautifulSoup，和lxml一样，是一个html的解析器，主要功能也是解析和提取数据    
3.优缺点？
缺点：效率没有lxml的效率高      
优点：接口设计人性化，使用方便 
```

# 2、安装以及创建

```shell
1.安装
pip install bs4    
2.导入
from bs4 import BeautifulSoup    
3.创建对象
服务器响应的文件生成对象    
soup = BeautifulSoup(response.read().decode(), 'lxml')        
本地文件生成对象    
soup = BeautifulSoup(open('1.html'), 'lxml')        
注意：默认打开文件的编码格式gbk所以需要指定打开编码格式 
```

# 3、节点定位

```shell
1.根据标签名查找节点
soup.a 【注】只能找到第一个a        
soup.a.name            
soup.a.attrs   
         
2.函数
(1).find(返回一个对象)        
                  find('a')：只找到第一个a标签
                  find('a', title='名字')
                  find('a', class_='名字')
(2).find_all(返回一个列表)        
                  find_all('a')  查找到所有的a
                  find_all(['a', 'span'])  返回所有的a和span
                  find_all('a', limit=2)  只找前两个a
(3).select(根据选择器得到节点对象)【推荐】        
                  1.element
                      eg:p
                  2..class
                      eg:.firstname
                  3.#id
                      eg:#firstname
                  4.属性选择器
                      [attribute]
                          eg:li = soup.select('li[class]')
                      [attribute=value]
                          eg:li = soup.select('li[class="hengheng1"]')
                  5.层级选择器
                      element element
                          div p
                      element>element
                          div>p
                      element,element
                          div,p
                           eg:soup = soup.select('a,span')
```

# 4、节点信息

```shell
(1).获取节点内容：适用于标签中嵌套标签的结构    
            obj.string
            obj.get_text()【推荐】
(2).节点的属性    
            tag.name 获取标签名
                eg:tag = find('li)
                   print(tag.name)
   tag.attrs将属性值作为一个字典返回        
(3).获取节点属性   
            obj.attrs     #返回的是一个字典，里面的键值对就是标签属性对应的键值对 
            obj.attrs.get('title')【常用】    
            obj.get('title')
            obj['title']
(4).删除：
    obj.decomposr()        #就是将自身这个标签删除，但是不删除其中的内容
```

应用实例： 1.股票信息提取（[http://quote.stockstar.com/](http://quote.stockstar.com/)）

2.中华英才网-旧版

3 .腾讯公司招聘需求抓取（[https://hr.tencent.com/index.php](https://hr.tencent.com/index.php)）

# 5、应用实例

## 1）爬取星巴克所有的菜单名称

```
# _*_ coding : utf-8 _*_
# @Time : 2022/5/22 22:57
# @Author : HackWu
# @File : 07 bs4爬取星巴克数据
# @Project : 解析
import urllib.request
import urllib.parse
from bs4 import BeautifulSoup
url="https://www.starbucks.com.cn/menu/"
response=urllib.request.urlopen(url)
content=response.read().decode("utf-8")
# print(content)
'''
    //section[@id='content']//a/strong  
'''
soup=BeautifulSoup(content,"lxml")
name_list=soup.select("section[id='content'] a>strong")
for name in name_list:
    print(name.string)
```