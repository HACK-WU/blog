Chrome-headless 模式， Google 针对 Chrome 浏览器 59版 新增加的一种模式，可以让你不打开UI界面的情况下

使用 Chrome 浏览器，所以运行效果与 Chrome 保持完美一致

# 1、系统要求

```
1.系统要求：
          Chrome
                Unix\Linux 系统需要 chrome >= 59
                Windows 系统需要 chrome >= 60
          Python3.6
          Selenium==3.4.*
          ChromeDriver==2.31
```

# 2、配置

```
from selenium import webdriver
from selenium.webdriver.chrome.options import Options    
    chrome_options = Options()
    chrome_options.add_argument('‐‐headless')
    chrome_options.add_argument('‐‐disable‐gpu')
    path = r'C:\Program Files (x86)\Google\Chrome\Application\chrome.exe'
    chrome_options.binary_location = path
browser = webdriver.Chrome(chrome_options=chrome_options)    
browser.get('http://www.baidu.com/')
```

# 3、封装配置

```
3.配置封装：
          from selenium import webdriver
          #这个是浏览器自带的  不需要我们再做额外的操作
          from selenium.webdriver.chrome.options import Options
          def share_browser():
              #初始化
              chrome_options = Options()
              chrome_options.add_argument('‐‐headless')
              chrome_options.add_argument('‐‐disable‐gpu')
              #浏览器的安装路径    打开文件位置
              #这个路径是你谷歌浏览器的路径
              path = r'C:\Program Files (x86)\Google\Chrome\Application\chrome.exe'
              chrome_options.binary_location = path
              browser = webdriver.Chrome(chrome_options=chrome_options)
              return  browser
  封装调用：
          from handless import share_browser
          browser = share_browser()
          browser.get('http://www.baidu.com/')
          browser.save_screenshot('handless1.png')
```