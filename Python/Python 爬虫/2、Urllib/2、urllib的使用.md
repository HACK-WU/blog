# 总结：

# 1、urllib的使用

```
urllib.request.urlopen() 模拟浏览器向服务器发送请求    
response    服务器返回的数据    
    response的数据类型是HttpResponse        
    字节‐‐>字符串        
    解码decode                
    字符串‐‐>字节        
    编码encode                
    read()       字节形式读取二进制   扩展：rede(5)返回前几个字节        
    readline()   读取一行        
    readlines()  一行一行读取 直至结束        
    getcode()    获取状态码        
    geturl()     获取url        
    getheaders() 获取headers        
urllib.request.urlretrieve()    
    请求网页        
    请求图片        
    请求视频 
```

# 2、请求对象定制

```
UA介绍：User Agent中文名为用户代理，简称 UA，它是一个特殊字符串头，使得服务器能够识别客户使用的操作系统
及版本、CPU 类型、浏览器及版本。浏览器内核、浏览器渲染引擎、浏览器语言、浏览器插件等
```

```
语法：request = urllib.request.Request()
```

扩展：编码的由来

```
'''编码集的演变‐‐‐
由于计算机是美国人发明的，因此，最早只有127个字符被编码到计算机里，也就是大小写英文字母、数字和一些符号，
这个编码表被称为ASCII编码，比如大写字母A的编码是65，小写字母z的编码是122。
但是要处理中文显然一个字节是不够的，至少需要两个字节，而且还不能和ASCII编码冲突，
所以，中国制定了GB2312编码，用来把中文编进去。
你可以想得到的是，全世界有上百种语言，日本把日文编到Shift_JIS里，韩国把韩文编到Euc‐kr里，
各国有各国的标准，就会不可避免地出现冲突，结果就是，在多语言混合的文本中，显示出来会有乱码。
因此，Unicode应运而生。Unicode把所有语言都统一到一套编码里，这样就不会再有乱码问题了。
Unicode标准也在不断发展，但最常用的是用两个字节表示一个字符（如果要用到非常偏僻的字符，就需要4个字节）。
现代操作系统和大多数编程语言都直接支持Unicode。
'''
```

# 3、编解码

## 1)get请求方式：urllib.parse.quote()

```
eg：
import urllib.request
import urllib.parse
url = 'https://www.baidu.com/s?wd='
headers = {
    'User‐Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like
Gecko) Chrome/74.0.3729.169 Safari/537.36'
}
url = url + urllib.parse.quote('小野')
request = urllib.request.Request(url=url,headers=headers)
response = urllib.request.urlopen(request)
print(response.read().decode('utf‐8'))
```

## 2)get请求方式：urllib.parse.urlencode()

```
eg:
import urllib.request
import urllib.parse
url = 'http://www.baidu.com/s?'
data = {
    'name':'小刚',
    'sex':'男',
}
data = urllib.parse.urlencode(data)
url = url + data
print(url)
headers = {
    'User‐Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like
Gecko) Chrome/74.0.3729.169 Safari/537.36'
}
request = urllib.request.Request(url=url,headers=headers)
response = urllib.request.urlopen(request)
print(response.read().decode('utf‐8'))
```

## 3)post请求方式：

```
eg:百度翻译
import urllib.request
import urllib.parse
url = 'https://fanyi.baidu.com/sug'
headers = {
    'user‐agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like
Gecko) Chrome/74.0.3729.169 Safari/537.36'
}
keyword = input('请输入您要查询的单词')
data = {
    'kw':keyword
}
data = urllib.parse.urlencode(data).encode('utf‐8')
request = urllib.request.Request(url=url,headers=headers,data=data)
response = urllib.request.urlopen(request)
print(response.read().decode('utf‐8'))
```

总结：post和get区别？

```
1：get请求方式的参数必须编码，参数是拼接到url后面，编码之后不需要调用encode方法
2：post请求方式的参数必须编码，参数是放在请求对象定制的方法中，编码之后需要调用encode方法
```

## 案例练习：百度详细翻译

```
import urllib.request
import urllib.parse
url = 'https://fanyi.baidu.com/v2transapi'
headers = {

    'cookie': 'REALTIME_TRANS_SWITCH=1; FANYI_WORD_SWITCH=1; HISTORY_SWITCH=1;
    SOUND_SPD_SWITCH=1; SOUND_PREFER_SWITCH=1; PSTM=1537097513;
BIDUPSID=D96F9A49A8630C54630DD60CE082A55C; BAIDUID=0814C35D13AE23F5EAFA8E0B24D9B436:FG=1;
to_lang_often=%5B%7B%22value%22%3A%22en%22%2C%22text%22%3A%22%u82F1%u8BED%22%7D%2C%7B%22value%22
%3A%22zh%22%2C%22text%22%3A%22%u4E2D%u6587%22%7D%5D;
from_lang_often=%5B%7B%22value%22%3A%22zh%22%2C%22text%22%3A%22%u4E2D%u6587%22%7D%2C%7B%22value%
22%3A%22en%22%2C%22text%22%3A%22%u82F1%u8BED%22%7D%5D; BDORZ=B490B5EBF6F3CD402E515D22BCDA1598;
delPer=0; H_PS_PSSID=1424_21115_29522_29519_29099_29568_28835_29220_26350; PSINO=2; locale=zh;
Hm_lvt_64ecd82404c51e03dc91cb9e8c025574=1563000604,1563334706,1565592510;
Hm_lpvt_64ecd82404c51e03dc91cb9e8c025574=1565592510;
yjs_js_security_passport=2379b52646498f3b5d216e6b21c6f1c7bf00f062_1565592544_js',

    'user‐agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like
Gecko) Chrome/76.0.3809.100 Safari/537.36',
}
data = {
    'from': 'en',
    'to': 'zh',
    'query': 'you',
    'transtype': 'realtime',
    'simple_means_flag': '3',
    'sign': '269482.65435',
    'token': '2e0f1cb44414248f3a2b49fbad28bbd5',
}
#参数的编码
data = urllib.parse.urlencode(data).encode('utf‐8')
# 请求对象的定制
request = urllib.request.Request(url=url,headers=headers,data=data)
response = urllib.request.urlopen(request)
# 请求之后返回的所有的数据
content = response.read().decode('utf‐8')
import json
# loads将字符串转换为python对象
obj = json.loads(content)
# python对象转换为json字符串  ensure_ascii=False  忽略字符集编码
s = json.dumps(obj,ensure_ascii=False)
print(s)
```

# 4、ajax的get请求

案例：豆瓣电影

```
# 爬取豆瓣电影前10页数据
# https://movie.douban.com/j/chart/top_list?
type=20&interval_id=100%3A90&action=&start=0&limit=20
# https://movie.douban.com/j/chart/top_list?
type=20&interval_id=100%3A90&action=&start=20&limit=20
# https://movie.douban.com/j/chart/top_list?
type=20&interval_id=100%3A90&action=&start=40&limit=20
import urllib.request
import urllib.parse
# 下载前10页数据
# 下载的步骤：1.请求对象的定制  2.获取响应的数据 3.下载
# 每执行一次返回一个request对象
def create_request(page):
    base_url = 'https://movie.douban.com/j/chart/top_list?type=20&interval_id=100%3A90&action=&'
    headers = {
            'User‐Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML,
like Gecko) Chrome/76.0.3809.100 Safari/537.36'
    }
    data={
        #  1 2  3  4
        #  0 20 40 60
        'start':(page‐1)*20,
        'limit':20
    }
    # data编码
    data = urllib.parse.urlencode(data)
    url = base_url + data
    request = urllib.request.Request(url=url,headers=headers)
    return request
# 获取网页源码
def get_content(request):
    response = urllib.request.urlopen(request)
    content = response.read().decode('utf‐8')
    return content
def down_load(page,content):
#    with open（文件的名字，模式，编码）as fp:
#        fp.write(内容)
    with open('douban_'+str(page)+'.json','w',encoding='utf‐8')as fp:
        fp.write(content)
if __name__ == '__main__':
    start_page = int(input('请输入起始页码'))
    end_page = int(input('请输入结束页码'))
    for page in range(start_page,end_page+1):
        request = create_request(page)
        content = get_content(request)
        down_load(page,content)
```

# 5、ajax的post请求

案例：KFC官网

```
# _*_ coding : utf-8 _*_
# @Time : 2022/5/21 13:54
# @Author : HackWu
# @File : 11kfc餐厅查询
# @Project : urllib
'''
    request method: post
    http://www.kfc.com.cn/kfccda/ashx/GetStoreList.ashx?op=cname
    http://www.kfc.com.cn/kfccda/ashx/GetStoreList.ashx?op=cname
    http://www.kfc.com.cn/kfccda/ashx/GetStoreList.ashx?op=cname
    form-data:
        cname: 镇江
        pid:
        pageIndex: 2
        pageSize: 10
    Cookie: route-cell=ksa; Hm_lvt_1039f1218e57655b6677f30913227148=1653055472,1653112679; Hm_lpvt_1039f1218e57655b6677f30913227148=1653112679; Hm_lvt_5fd8501a4e4e0eddf0c4596de7bd57ab=1653055472,1653112679; Hm_lpvt_5fd8501a4e4e0eddf0c4596de7bd57ab=1653112679; ASP.NET_SessionId=f2x5ktqydllxntaqj13zbwv3; SERVERID=f73042d09468fdd08da1a18a1f85e14d|1653113255|1653112678
    User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/98.0.4758.102 Safari/537.36
    X-Requested-With: XMLHttpRequest  ,:Ajax请求方式
'''
import urllib.request
import urllib.parse
import json
def create_request(page):       #定制request请求对象
    url="http://www.kfc.com.cn/kfccda/ashx/GetStoreList.ashx?op=cname"
    headers={
        "User-Agent":"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/98.0.4758.102 Safari/537.36",
        "Cookie":"route-cell=ksa; Hm_lvt_1039f1218e57655b6677f30913227148=1653055472,1653112679; Hm_lpvt_1039f1218e57655b6677f30913227148=1653112679; Hm_lvt_5fd8501a4e4e0eddf0c4596de7bd57ab=1653055472,1653112679; Hm_lpvt_5fd8501a4e4e0eddf0c4596de7bd57ab=1653112679; ASP.NET_SessionId=f2x5ktqydllxntaqj13zbwv3; SERVERID=f73042d09468fdd08da1a18a1f85e14d|1653113255|1653112678",
    }
    data={
        "cname": "镇江",
        "pid":"",
        "pageIndex": page,
        "pageSize": 10,
    }
    data=urllib.parse.urlencode(data).encode("utf-8")   #对请求体中的数据进行格式编码
    request=urllib.request.Request(url=url,data=data,headers=headers)
    return request
def get_content(request):
    response=urllib.request.urlopen(request)
    content=response.read().decode("utf-8") #返回一个json数据
    return content
def down_content(content,page):
    file_name=f"kfc{page}.json"
    with open(file_name,"w",encoding="utf-8") as f:
        f.write(content)
if __name__=="__main__":
    for page in range(1,6):
        request=create_request(page)
        get_content(request)
        content=get_content(request)
        down_content(content,page)
    print("打印完成！！！")
```