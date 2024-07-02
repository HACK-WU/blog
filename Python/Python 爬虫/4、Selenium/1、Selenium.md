**Selenium**

# **1、Selenium**

## 1）什么是selenium？

```

（1）Selenium是一个用于Web应用程序测试的工具。    
（2）Selenium 测试直接运行在浏览器中，就像真正的用户在操作一样。    
（3）支持通过各种driver（FirfoxDriver，IternetExplorerDriver，OperaDriver，ChromeDriver）驱动
真实浏览器完成测试。   
（4）selenium也是支持无界面浏览器操作的。 
```

## 2）为什么使用selenium

```

模拟浏览器功能，自动执行网页中的js代码，实现动态加载
```

## 3）如何安装selenium？

```
1）操作谷歌浏览器驱动下载地址    
http://chromedriver.storage.googleapis.com/index.html             
（2）谷歌驱动和谷歌浏览器版本之间的映射表    
http://blog.csdn.net/huilan_same/article/details/51896672            
（3）查看谷歌浏览器版本    
谷歌浏览器右上角‐‐>帮助‐‐>关于            
（4）pip install selenium 
```

## 4）selenium的使用步骤？

```
（1）导入：from selenium import webdriver    
（2）创建谷歌浏览器操作对象：    
        path = 谷歌浏览器驱动文件路径                
        browser = webdriver.Chrome(path)                
（3）访问网址    
        url = 要访问的网址                
        browser.get(url) 
```

## 5）elenium的元素定位？

```
元素定位：自动化要做的就是模拟鼠标和键盘来操作来操作这些元素，点击、输入等等。操作这些元素前首先
要找到它们，WebDriver提供很多定位元素的方法
       
方法：        
              1.find_element_by_id
               eg:button = browser.find_element_by_id('su')     
               button=browser.find_element("id","su")     #新版本 
               
              2.find_elements_by_name
               eg:name = browser.find_element_by_name('wd')  #name指的是标签中的name属性     
               button=browser.find_element("name","wd")  #新版本
                  
              3.find_elements_by_xpath
               eg:xpath1 = browser.find_elements_by_xpath('//input[@id="su"]')  
               button=browser.find_elements("xpath","//input[@id='su']")         #新版本
               
              4.find_elements_by_tag_name
               eg:names = browser.find_elements_by_tag_name('input')      #指的就是标签名称  
               button=browser.find_element("tag name","input")        #新版本
               
              5.find_elements_by_css_selector
               eg:my_input = browser.find_elements_by_css_selector('#kw')[0]     #类似于BeautifulSoup模块中的soup.select()方法    
                button=browser.find_element("css selector","#kw")      #新版本
                
              6.find_elements_by_link_text        #获取的是html页面中的，具有链接作用的文本。
               eg:browser.find_elem
               eg:browser.find_element_by_link_text("新闻") 
               button=browser.find_element("link text","新闻")   #新版本
               
               
***************************实例： ************************8              
from selenium import webdriver
path="chromedriver.exe"         #驱动器的位置
browser=webdriver.Chrome(path)  #创建一个浏览器对象
url="https://www.baidu.com"
browser.get(url)
# content=browser.page_source     #返回页面资源，是一个str
# print(content)
# print(type(content))
# 根据id查找对象
# button=browser.find_element("id","su")
# print(button)
# 根据标签的属性值来获取对象
# button=browser.find_element("name","wd")
# print(button)
# 根据xpath语言来获取对象
# button=browser.find_elements("xpath","//input[@id='su']")
# print(button)
# 根据标签的名称来获取对象
# button=browser.find_element("tag name","input")
# print(button)
# 根据css selector来获取对象
# button=browser.find_element("css selector","#kw")
# print(button)
# print(button.tag_name)
# 根据link name来来获取对象
# button=browser.find_element("link text","新闻")
# print(button)
# print(button.text)
'''
      ID = "id"
    XPATH = "xpath"
    LINK_TEXT = "link text"
    PARTIAL_LINK_TEXT = "partial link text"
    NAME = "name"
    TAG_NAME = "tag name"
    CLASS_NAME = "class name"
    CSS_SELECTOR = "css selector"
'''
```

## 6）访问元素信息

```
获取元素属性    
    .get_attribute('class')        
获取元素文本    
    .text        
获取标签名    
    .tag_name 
    
*******************************实例:********************************        
from selenium import webdriver

path = 'chromedriver.exe'
browser = webdriver.Chrome(path)


url = 'http://www.baidu.com'
browser.get(url)


input = browser.find_element_by_id('su')

# 获取标签的属性
print(input.get_attribute('class'))
# 获取标签的名字
print(input.tag_name)

# 获取元素文本
a = browser.find_element_by_link_text('新闻')
print(a.text)
```

## 7）交互

```
点击:click()    
输入:send_keys()    
后退操作:browser.back()    
前进操作:browser.forword()    
模拟JS滚动:    
    js='document.documentElement.scrollTop=100000'       #滑动到页面底部 
    browser.execute_script(js)                           #执行js代码        
获取网页代码:page_source     
退出:browser.quit() 
**********************************实例********************************************
# _*_ coding : utf-8 _*_
# @Time : 2022/5/23 16:32
# @Author : HackWu
# @File : 03 交互
# @Project : selenium
from selenium import webdriver
import  time
path="chromedriver.exe"
browser=webdriver.Chrome(path)
browser.get("http://www.baidu.com")     #这步打开浏览器输入地址
time.sleep(2)   #睡眠2秒
# 找到input 输入框
input=browser.find_element("id","kw")
input.send_keys("周杰伦")  #输入字符
time.sleep(2)
#点击百度一下，相当于回车
button=browser.find_element("id","su")
button.click()  #点击
time.sleep(2)
js="document.documentElement.scrollTop=10000"   #滑到底部
browser.execute_script(js)
time.sleep(2)
next=browser.find_element("xpath","//a[@class='n']")    #点击下一页
next.click()
browser.back()      #返回之前的页面
time.sleep(2)
browser.forward()   #前进
time.sleep(3)
browser.quit()      #退出
```