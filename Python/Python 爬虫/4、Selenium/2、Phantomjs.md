**Phantomjs**

# 1、什么是Phantomjs?

```
1.什么是Phantomjs？
（1）是一个无界面的浏览器    
（2）支持页面元素查找，js的执行等    
（3）由于不进行css和gui渲染，运行效率要比真实的浏览器要快很多 
```

# 2、如何使用Phantomjs

```
2.如何使用Phantomjs？
（1）获取PhantomJS.exe文件路径path    
（2）browser = webdriver.PhantomJS(path)    
（3）browser.get(url)    
 扩展：保存屏幕快照:browser.save_screenshot('baidu.png') 
```