**JsonPath总结**

# 1、JsonPath

```
jsonpath的安装及使用方式：
    pip安装：             
       pip install jsonpath                
    jsonpath的使用：            
                        obj = json.load(open('json文件', 'r', encoding='utf‐8'))
                        ret = jsonpath.jsonpath(obj, 'jsonpath语法')
```

教程连接（[http://blog.csdn.net/luxideyao/article/details/77802389](http://blog.csdn.net/luxideyao/article/details/77802389)）

案例练习:淘票票

作业： 1.股票信息提取（[http://quote.stockstar.com/](http://quote.stockstar.com/)）

- 2.boos直聘

- 3.中华英才

- 4.汽车之家

# 2、jsonpath的基本语法

```
# _*_ coding : utf-8 _*_
# @Time : 2022/5/22 19:53
# @Author : HackWu
# @File : 04JsonPath
# @Project : 解析
import jsonpath
import  json
obj=json.load(open("store.json",encoding="utf-8"))
# print(obj)
# 书店所有的书的作者
# author_list=jsonpath.jsonpath(obj,"$.store.book[*].author")
# print(author_list)
# 所有的作者
# author_list=jsonpath.jsonpath(obj,"$..author")
# # print(author_list)
# store下面所有的元素
# tag_list=jsonpath.jsonpath(obj,"$.store.*")
# print(tag_list)
# store里面所有东西的钱
# price_list=jsonpath.jsonpath(obj,"$.store..price")
# print(price_list)
# 第三本书
# book=jsonpath.jsonpath(obj,"$.store.book[2]")   #.相当于子元素 ； ..相当于后代元素
# print(book)
# 最后一本书
# book=jsonpath.jsonpath(obj,"$.store.book[(@.length-1)]")
# print(book)
# 前三本书
# book_list=jsonpath.jsonpath(obj,"$.store.book[0,1,2]")    #方法一：单独指定
# print(book_list)
# book_list=jsonpath.jsonpath(obj,"$.store.book[:3]")    #方法二
# print(book_list)
# 过滤出所有包含isbn的书
# book_list=jsonpath.jsonpath(obj,"$.store.book[?(@.isbn)]")
# # print(book_list)
# 哪本书超过了十块钱
book_list=jsonpath.jsonpath(obj,"$.store.book[?(@.price>10)]")
print(book_list)
```

# 3、应用实例

## 1）使用jsonpath获取淘票票所有的地区名称：

```
# _*_ coding : utf-8 _*_
# @Time : 2022/5/22 21:22
# @Author : HackWu
# @File : 05jsonpath解析淘票票
# @Project : 解析
'''
    https://dianying.taobao.com/cityAction.json?activityId&_ksTS=1653226532327_97&jsoncallback=jsonp98&action=cityAction&n_s=new&event_submit_doGetAllRegion=true
'''
import jsonpath
import json
import urllib.request
def create_jsonfile():
    url="https://dianying.taobao.com/cityAction.json?activityId&_ksTS=1653226532327_97&jsoncallback=jsonp98&action=cityAction&n_s=new&event_submit_doGetAllRegion=true"
    headers={
        "User-Agent":"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/98.0.4758.102 Safari/537.36",
        "cookie":'miid=6293481551937808078; t=ef848b1f978d69cedc338376e60a8365; cookie2=1271cb72d2369136919fb81915c037f7; v=0; _tb_token_=ee6d7e3beef69; cna=Ad+lGrdduQ8CAXBX7GkPi0+m; xlly_s=1; tb_city=110100; tb_cityName="sbG+qQ=="; tfstk=cxEfBwTjGIAb3I7w0-6Pb75AYFiNZr5SEZGYh_GD5wdcq0efiMKEOezaSQmZBY1..; isg=BMHBPlbBR5rzNquusP8IxOd70A3b7jXgeA3fEyMWREgnCuDcazvTsIgE7H5MAs0Y; l=eBLHRTMqLm6WHc3aBO5Cnurza77TXIRb8sPzaNbMiInca1mdtFOdiNCh_aS6SdtjgtfbDetzQ4is6RE6zsap_giMW_N-1NKmIxJ6-',
        "referer": "https://dianying.taobao.com/?spm=a1z21.3046609.city.1.4a6e112as3yGsy&city=110100",
    }
    request=urllib.request.Request(url=url,headers=headers)
    response=urllib.request.urlopen(request)
    content=response.read().decode("utf-8")
    content=content.split("(")[1].split(")")[0]
    with open("taopp.json","w",encoding="utf-8") as f:
        f.write(content)
    print("打印完成！！")
if __name__ == '__main__':
    json_obj=json.load(open("taopp.json","r",encoding="utf-8"))
    regionName_list=jsonpath.jsonpath(json_obj,"$..regionName")
    print(regionName_list)
```