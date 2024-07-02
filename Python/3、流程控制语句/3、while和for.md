# 1、while



## 1)语法1

```javascript
a=0
while a<5 ：
    print(a)
    a+=1
print("终于结束了")
```



![](images/WEBRESOURCE250f25431b9269db61290a08494fc45b截图.png)



## 语法2（Python独有）

```javascript
a=0
while a<5 ：
    print(a)
    a+=1
else :
    print("a不小于5了",a)
print("终于结束了")
```



![](images/WEBRESOURCE6c38182905398e878b7f37ec898f26fe截图.png)



---

## 2、pass(用来站位没有意义）

```javascript
a=10
if a<20:
    pass
print(a)
```



![](images/WEBRESOURCE6192faecad256d8ef7250fc7c3a46047截图.png)



---

# 3、for

```javascript
list=list("123456789")
print(list)
for s in list:
    print(s)
    

```



![](images/WEBRESOURCE841baea092bea896cd46be0a2084fcec截图.png)



---

# 4、 for else

如果for 循环是正常结束，而不是被打断结束的，那么就会执行else中代码。

```javascript
num=0
for i in range(3):
    print(i)
    num+=1
    if num==2:
        break
else:
    print(num)
```



![](images/WEBRESOURCE18ffa5fa334b537856c74567c393b7ec截图.png)

