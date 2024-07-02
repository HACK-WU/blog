# ModelOperationSet.py

```
# _*_ coding : utf-8 _*_
# @Time : 2023/5/2 18:09
# @Author : HackWu
# @File : my_models
# @Project : CMDB
from django.shortcuts import  render
from django.views.generic import  View
from  django.http import  JsonResponse
import  json
from django.core.paginator import Paginator, EmptyPage
class MyPaginator():
    def __init__(self):
        self.page_range=None
        self.current_page=None
    def createPages(self,request,queryset_obj,content_num,page_num=5):
        num=request.GET.get("page")
        if not num: #如果num为空
            num="1"
        num=int(num)
        paginator=Paginator(queryset_obj,content_num)
        self.page_range=paginator.page_range  #获取到分页范围
        try:
            self.current_page=paginator.page(num)    #获取到当前的分页内容
        except EmptyPage:
            self.current_page=paginator.page(1)      #如果出错默认显示第一页的内容
            num=1
        if page_num%2==0:
            page_num-=1
        left_num=page_num//2
        if paginator.num_pages>page_num:
            right=num+left_num+1
            left=num-left_num
            if right>paginator.num_pages:
                right=paginator.num_pages+1
                left=right-page_num
            if left<=0:
                left=1
                right=page_num+1
            self.page_range=range(left,right)
        dic={
            "current_page":self.current_page,
            "page_range":self.page_range,
            "num":num,
        }
        return dic      #返回当前页面和页面的范围
class ModelOperationsSet(View,MyPaginator):     #模型类，序列化数据
    model=None
    template_name_list=None
    template_name_create=None
    template_name_update=None
    page_num=5
    content_num=None
    meta={}
    success={}
    fail={}
    res_success={
        "res":"success"
    }
    res_fail={
        "res":"fail"
    }
    def check(self):        #检查
        if not self.model:
            raise ValueError("Model is not allowed to be None. ")
        if not isinstance(self.meta,dict):
            raise TypeError("the type of 'meta' must be dict")      #meta
        if not isinstance(self.success,dict):
            raise  TypeError("value of the 'success' must be a dict")
        if not  isinstance(self.fail,dict):
            raise  TypeError("value of the 'fail' must be a dict")
        return
    def frontend_serialized(self):    #序列化前端数据
        data=self.request.POST              #获取前端传来的数据
        if len(data)==0:
            data=self.request.body.decode("utf-8")
            data=json.loads(data)       #将JSON数据转换为字典
        print("hostname",data.get("hostname"))
        dic={}
        manyList=[]
        if "ManyToManyField" in self.meta:          #保存外键的值
            for key in self.meta.get("ManyToManyField"):
                front_data=data.getlist(key)
                if not front_data:
                    front_data=data.get(key)
                    manyList.append((key,[front_data]))
                else:
                   manyList.append((key,front_data))
        for field in self.model._meta.fields:
            field_name=str(field.name)          #后端字段名称
            front_name=field_name               #前端字段名称，默认两个相等
            is_file=False       #是否是文件
            if field_name in self.meta:
                meta_field=self.meta.get(field_name)
                if isinstance(meta_field,dict):         #判断是否为一个字典，如果为字典，证明有额外的补充
                    if "relation" in meta_field:        #有关系型字段
                        relation=meta_field.get("relation")
                        if relation in ("ForeignKey","OneToOneField"):
                            field_name=field_name+"_id"         #更改主键名称为:field_id
                            front_name=field_name
                    if field_name in meta_field:
                        front_name=meta_field.get(field_name)
                    if meta_field.get("FileField",False)==True:     #说明此字段是文件对象
                        dic[field_name]=self.request.FILES.get(front_name)
                        continue    #直接下一步
                elif isinstance(meta_field,str):    #判断是否为字符串
                    front_name=meta_field
            dic[field_name]=data.get(front_name)
        return  dic,manyList
    def backend_serialized(self,mode_instance):       #序列化后端数据
        dic={}
        for field in self.model._meta.fields:
            field_name=str(field.name)
            if field_name in self.meta:
                meta_field=self.meta.get(field_name)
                if isinstance(meta_field,dict):
                    if "relation" in meta_field:
                        relation=meta_field.get("relation")
                        if relation in ("ForeignKey","OneToOneField"):
                            field_name=field_name+"_id"
                        if field_name in meta_field:
                            dic[meta_field.get(field_name)]=getattr(mode_instance,field_name)
                        else:
                            dic[field_name]=getattr(mode_instance,field_name)
                    front_name=field_name       #默认前后端字段名称一样
                    if meta_field.get(field_name,False):
                        front_name=meta_field.get(field_name)
                    if meta_field.get("FileField",False)==True:     #说明此字段是文件对象
                        dic[front_name]=str(getattr(mode_instance,field_name))
                if isinstance(meta_field,str):
                    dic[self.meta.get(field_name)]=getattr(mode_instance,field_name)
            else:
                dic[field_name]=getattr(mode_instance,field_name)
        if "ManyToManyField" in self.meta:
            tup=self.meta.get("ManyToManyField")
            for item in tup:
                id_list=[]
                for model in getattr(mode_instance,item).all():
                    if "show_field" in self.meta:
                        show_field=self.meta.get("show_field").get(item)
                        if show_field:
                            id_list.append(getattr(model,show_field))
                        else:
                            id_list.append(model.pk)
                    else:
                        id_list.append(model.pk)
                if item in self.meta:
                    show_name=self.meta.get(item)
                    if isinstance(show_name,str):
                        dic[show_name]=id_list
                else:
                    dic[item]=id_list
        print("获取完成")
        print(dic)
        return  dic
    def create_Update(self,operation,pk=None):      #更新或创建操作
        ModelOperationsSet.res_success["operation"]=operation
        ModelOperationsSet.res_fail["operation"]=operation
        dic,manyList=self.frontend_serialized()
        print(dic,manyList)
        try:
            model_obj=None
            if operation=="create":
                model_obj=self.model.objects.create(**dic)        #创建数据
                if len(manyList)>0:
                    for key,value in manyList:
                        if hasattr(model_obj,key):
                            getattr(model_obj,key).set(value)
                        else:
                            getattr(model_obj,key+"_set").set(value)
            elif operation=="update":
                if not pk:
                    raise  ValueError("pk not allowed be None")
                self.model.objects.filter(pk=pk).update(**dic)     #更新数据
                model_obj=self.model.objects.get(pk=pk)     #获取到新的数据对象
                if len(manyList)>0:
                    for key,value in manyList:
                        if hasattr(model_obj,key):
                            getattr(model_obj,key).set(value)
                        else:
                            getattr(model_obj,key+"_set").set(value)
            self.res_success.update(self.success)
            self.res_success["data"]=self.backend_serialized(model_obj)
            return  self.res_success
        except Exception as e:
            print(e)
            self.res_fail.update(self.fail)
            self.res_fail["msg"]=str(e)
            return  self.res_fail
    def delete(self,pk):        #删除操作
        ModelOperationsSet.res_success["operation"]="delete"
        ModelOperationsSet.res_fail["operation"]="delete"
        try:
            self.model.objects.filter(pk=pk).delete()
            self.res_success.update(self.success)
            return  self.res_success
        except Exception as e:
            print(e)
            self.res_fail.update(self.fail)
            self.res_fail["msg"]=str(e)
            return  self.res_fail
    def query(self,pk):
        ModelOperationsSet.res_success["operation"]="query"
        ModelOperationsSet.res_fail["operation"]="query"
        try:
            model_obj=self.model.objects.get(pk=pk)
            res=self.backend_serialized(model_obj)
            self.res_success["data"]=res
            self.res_success.update(self.success)
            return  self.res_success
        except Exception as e:
            print(e)
            self.res_fail.update(self.fail)
            self.res_fail["msg"]=str(e)
            return  self.res_fail
    def list(self):
        ModelOperationsSet.res_success["operation"]="list"
        ModelOperationsSet.res_fail["operation"]="list"
        try:
            list=[]
            model_list=self.model.objects.all()
            for model in model_list:
                res=self.backend_serialized(model)
                list.append(res)
            self.res_success["data"]=list
            self.res_success.update(self.success)
            return  self.res_success
        except Exception as e:
            print(e)
            self.res_fail.update(self.fail)
            self.res_fail["msg"]=str(e)
            return  self.res_fail
    def get(self,request,operation):
        self.check()
        if operation=="create":         #创建
            return render(request,self.template_name_create)
        elif operation=="update":         #更新
            model_obj=self.model.objects.get(pk=request.GET.get("pk"))
            return render(request,self.template_name_update,{"model_obj":model_obj})
        elif operation=="delete":       #删除
            pk=request.GET.get("pk")
            res=self.delete(pk)
            return JsonResponse(res)
        elif operation=="query":        #查询
            pk=request.GET.get("pk")
            res=self.query(pk)
            return  JsonResponse(res)
        elif operation=="list":
            user_agent=request.META.get("HTTP_USER_AGENT","")
            if self.template_name_list and "Mozilla" in user_agent:     #判断用户代理是否是浏览器
                model_list=self.model.objects.all().order_by("pk")
                if not self.content_num:
                    return render(request,self.template_name_list,{"model_list":model_list})
                else:
                    dic=self.createPages(request,model_list,self.content_num,self.page_num)
                    return render(request,self.template_name_list,dic)
            else:
                res=self.list()
                return  JsonResponse(res)
        else:
            dic={
                "res":"fail",
                "msg":"please use 'POST'method to request"
            }
            return  JsonResponse(dic)
    def post(self,request,operation):
        self.check()
        if operation=="create":
            res=self.create_Update(operation="create")
            return  JsonResponse(res)
        elif operation=="update":
            res=self.create_Update(operation="update",pk=request.GET.get("pk"))
            return  JsonResponse(res)
        elif operation=="delete":
            res=self.delete(request.GET.get("pk"))
            return JsonResponse(res)
        else:
            dic={
                "res":"fail",
                "msg":"please use 'GET' method to request"
            }
            return  JsonResponse(dic)
```

# url 路由

```
re_path(r"^target_model/(?P<operation>create|update|delete|query|list)",views.ServerOperationView.as_view(),name="serveruser_operation"),
```