**get请求**

```python
requests.get()    
eg:        
 import requests            
 url = 'http://www.baidu.com/s?'
headers = {
   'User‐Agent': 'Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML,like Gecko) Chrome/65.0.3325.181 Safari/537.36'               
      }
data = {
    'wd':'北京'
   }
response = requests.get(url,params=data,headers=headers)
              
    定制参数    
    参数使用params传递        
    参数无需urlencode编码        
    不需要请求对象的定制        
    请求资源路径中？可加可不加 
```