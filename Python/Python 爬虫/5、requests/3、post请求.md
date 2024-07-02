**post请求**

```python
requests.post()    
百度翻译:    
eg:        
import requests            
post_url = 'http://fanyi.baidu.com/sug'            
headers={
       'User‐Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36(KHTML, like Gecko) Chrome/68.0.3440.106 Safari/537.36'
  }
 data = {
     'kw': 'eye'
    }
r = requests.post(url = post_url,headers=headers,data=data) 
```

**get和post的区别**

```
get和post区别？
    1： get请求的参数名字是params  post请求的参数的名字是data      
    2： 请求资源路径后面可以不加?      
    3： 不需要手动编解码      
    4： 不需要做请求对象的定制 
```