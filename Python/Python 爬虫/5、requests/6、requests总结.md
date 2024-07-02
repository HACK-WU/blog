**requests总结**

# cookie登录古诗文网实例：

```
# _*_ coding : utf-8 _*_
# @Time : 2022/5/24 16:43
# @Author : HackWu
# @File : cookie登录古诗文网
# @Project : requests
# 通过登陆  然后进入到主页面
# 通过找登陆接口我们发现 登陆的时候需要的参数很多
# _VIEWSTATE: /m1O5dxmOo7f1qlmvtnyNyhhaUrWNVTs3TMKIsm1lvpIgs0WWWUCQHl5iMrvLlwnsqLUN6Wh1aNpitc4WnOt0So3k6UYdFyqCPI6jWSvC8yBA1Q39I7uuR4NjGo=
# __VIEWSTATEGENERATOR: C93BE1AE
# from: http://so.gushiwen.cn/user/collect.aspx
# email: 595165358@qq.com
# pwd: action
# code: PId7
# denglu: 登录
# 我们观察到_VIEWSTATE   __VIEWSTATEGENERATOR  code是一个可以变化的量
# 难点:(1)_VIEWSTATE   __VIEWSTATEGENERATOR  一般情况看不到的数据 都是在页面的源码中
#     我们观察到这两个数据在页面的源码中 所以我们需要获取页面的源码 然后进行解析就可以获取了
#     (2)验证码
'''
__VIEWSTATE: U/nbiY8PUJzlvORmjxCZo+7BEOTS7uJbmWX3/LvsnSE5NoYhE2ys7EdkyPn4dZdpJ3/AUxoqEeDqtkikgB1Bt6xX5vlEOWpPtN14pYRNl3Xa087T1tN2/nteiOg=
__VIEWSTATEGENERATOR: C93BE1AE  
网页地址：https://so.gushiwen.cn/user/login.aspx?from=http://so.gushiwen.cn/user/collect.aspx
    from: http://so.gushiwen.cn/user/collect.aspx
email: 18706249257
pwd: 123123
code: T5NJ
denglu: 登录
1、需要获取验证码：
    -将每次访问时显示的验证码下载下来，观察后手动输入验证码。
2、需要找出__VIESTATE这个变量的值   
    通过查看网页源码即可获得：
    <input type="hidden" name="__VIEWSTATE" id="__VIEWSTATE" value="jZtk5sYnRjYb2wjmOV66UJ2o/ujC7CX5SGccEhXEvQxhfZjYOO5etdG1EgvxkeEssJjBxnvbu3Ow2tzXLn6ZG3xZdfTfxNe3b/rgWQgJlsKeyixHwELgYB+WWiY=" />
3、注意了:
    -当再次访问post请求地址时，验证码又会被刷新一次。
        所以如果使用先访问源码网页，爬取验证码，之后在进行一次表单访问，那么验证码会被重新刷新，
        所以之前识别的验证码就会失效。
    -所以我们后面的访问，不能是一次独立的访问。而是需要建立在之前的访问的基础之上，
    -此时就需要用到requests.session()
        -request.session()  #返回一个session对象，这个对象，封装了这次访问的请求对象，并且包含原来的url
            所以我们可以使用这个请求对象再进行一次访问，就相当于是从这个页面发起的访问。
4、当访一个服务器，需要从特定的网页发起请求时，
    我们可以使用requests.session():
    session=requests.session()  #返回的session，就相当于requests,用法是一样的。相当于在当前页面发起的访问。
验证码访问地址：
    https://so.gushiwen.cn/RandCode.ashx?t=1653455306097?t=1653455306832
'''
import requests
from lxml import etree
from bs4 import BeautifulSoup
url="https://so.gushiwen.cn/user/login.aspx?from=http%3a%2f%2fso.gushiwen.cn%2fuser%2fcollect.aspx"
headers={
    "user-agent":"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/98.0.4758.102 Safari/537.36",
}
# 1、访问网页，获取到网页源码，然后获取到VIEWSTAT的值
#//form[@id='aspnetForm']/div[1]/input/@value
response=requests.get(url=url,headers=headers)
# content=response.text     #网页源码
# tree=etree.HTML(content)
# result=tree.xpath("//form[@id='aspnetForm']/div[1]/input/@value")[0]
# print(result)
content=response.content    #返回二进制网页源码
soup=BeautifulSoup(content,"lxml")
viewstate=soup.select("#__VIEWSTATE")[0].attrs.get("value")     #__VIEWSTATE:
# 2、利用requests.session访问验证码图片，并下载
# 获取验证码图片
code = soup.select('#imgCode')[0].attrs.get('src')
code_url = 'https://so.gushiwen.cn' + code
print(code_url)
session=requests.session()
response_imgCode=session.get(code_url)
imgCode=response_imgCode.content  #二进制数据数据
print(imgCode)
with open("imgCode.jpg","wb") as f:
    f.write(imgCode)
imgcode=input("请输入验证码：")
# 3、post方式登录
# https://so.gushiwen.cn/user/login.aspx?from=http%3a%2f%2fso.gushiwen.cn%2fuser%2fcollect.aspx
url_post="https://so.gushiwen.cn/user/login.aspx?from=http%3a%2f%2fso.gushiwen.cn%2fuser%2fcollect.aspx"
data_post={
    "__VIEWSTATE":viewstate,
    "__VIEWSTATEGENERATOR": "C93BE1AE",
    "from":"http://so.gushiwen.cn/user/collect.aspx",
    "email":"18706249257",
    "pwd":"123456",
    "code":imgcode,
    "denglu": "登录"
}
response_post=session.post(url=url,data=data_post,headers=headers)
content_post=response_post.text
with open("gushiwenwang.html",'w',encoding="utf-8") as f:
    f.write(content_post)
```