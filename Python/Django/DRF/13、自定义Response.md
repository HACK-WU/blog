```python
from rest_framework.response import Response
# 以后都用自己封装的
class APIResponse(Response):
    def __init__(self,code=100,msg='成功',data=None,status=None,headers=None,**kwargs):
        dic = {'code': code, 'msg': msg}
        if  data:
            dic = {'code': code, 'msg': msg,'data':data}
        dic.update(kwargs)
        super().__init__(data=dic, status=status,headers=headers)
# 使用
return APIResponse(data={"name":'lqz'},token='dsafsdfa',aa='dsafdsafasfdee')
return APIResponse(data={"name":'lqz'})
return APIResponse(code='101',msg='错误',data={"name":'lqz'},token='dsafsdfa',aa='dsafdsafasfdee',header={})
```