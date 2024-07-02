# **1、读取**

- open()打开文件时，默认是以纯文本文件打开的

- open(),默认的编码为None

- open(fileName,encoding="utf-8"),指定编码

**demo2.txt**

```javascript
我在学python
```

```javascript
file_name="demo2.txt"
try:
    with open(file_name,encoding="utf-8")as file_obj:
        content=file_obj.read()
        print(content)
except FileNotFoundError:
    print(f"{file_name}文件没找到！")
```

![](images/WEBRESOURCE3f2b3033dc73287091fafd0658b136b6截图.png)

# **2、读取大文件**

- 如果直接调用read(),它会将文本文件的所有内容一次性全部都读出来，

- 如果文件过大，很占内容，所有文件较大，不要直接调用read()

- **read()**可以接收一个size作为参数（表示字符数），该参数用来指定要读取的字符数量，默认值为-1，表示将会读取全部内容

- 每一次读取，都是从上次读取到的位置开始读取

**demo.txt**

```javascript
1、Why do we use it?
2、It is a long established fact that a reader will be distracted by the readable content o
3、f a page when looking at its layout. The point of using Lorem Ipsum is that it has a more-or-less
4、normal distribution of letters, as opposed to using 'Content here, content here', making it look
5、like readable English. Many desktop publishing packages and web page editors now use Lorem Ipsum
6、as their default model text, and a search for 'lorem ipsum' will uncover many web sites still in
7、their infancy. Various versions have evolved over the years, sometimes by accident, sometimes on
8、purpose (injected humour and the like).
9、我
10、在
11、学
12、Python
```

- 

```javascript
file_name="demo.txt"
try:
    with open(file_name,encoding="utf-8")as file_obj:
     chunk=100	#指定每一次读取的数量
     #创建一个循环来读取文件的内容
     while True:
         content=file_obj.read()
         if not content :
             break
         print(content)
except FileNotFoundError:
    print(f"{file_name}文件没找到！")
```

![](images/WEBRESOURCE1f2a4d71eb8fc9563bb99ea19936df76截图.png)

# **3、readline()方法**

- 该方法可以用来读取一行的内容

- **readlines()方法：**

- 该方法用于一行一行读取内容，它会将每次读取到的一行的内容全都封装到一个列表中返回

```javascript
file_name="demo.txt"
try:
    with open(file_name,encoding="utf-8")as file_obj:
   	for str in range(8):
       		print(file_obj.readline())
   	print(file_obj.readlines())
except FileNotFoundError:
    print(f"{file_name}文件没找到！")
```

![](images/WEBRESOURCE3b58c16d836a7221d43eabefcd940d03截图.png)