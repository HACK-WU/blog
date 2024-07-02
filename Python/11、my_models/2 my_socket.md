```python
import json
import struct
def my_send(obj,data,encoding="utf-8"):  
    '''
    obj：   正真用于发送数据的对象。
        obj.send(data)
    data:   要发送的数据
    encoding:    数据的编码格式，默认的是utf-8
    '''
    header={
        "total_size":len(data)
    }
    header=json.dumps(header).encode(encoding)
    header_size=len(header)
    header_size=struct.pack("i",header_size)
    obj.send(header_size)
    obj.send(header)
    obj.send(data)
    
    return True
def my_recv(obj,fun=None,encoding="utf-8",is_return=True,trunk=1024*1024):
    '''
        obj：       真正用于接接收数据的对象
        fun:        一个函数对象,默认为None,可以不用传，可以用于处理每次接受到的数据
            fun(data) 就表示处理这个数据
        is_return  用于标记，此函数是否要返回接受到的数据，Ture(默认),表示返回。False。表示不返回
        encoding:   数据的编码格式，默认的是utf-8
        trunk:      指定每次接收数据的大小，默认为1MB，如果内存大，可以调大一点，内存小，调小一点。
    
    '''
    total_data=b""        #用于存储接受到的数据
    header_size=obj.recv(4)         #获取到一个4个字节的信息，里面封装了header的长度
    header_size=struct.unpack("i",header_size)[0]   #获取到header的长度
    header=obj.recv(header_size).decode(encoding)
    header=json.loads(header)
    total_size=header.get("total_size")         #获取到真实数据的大小
    data_recv=0
    while data_recv<total_size:
        data=obj.recv(trunk)
        if fun:
            fun(data)
        if is_return:
            total_data+=data
        data_recv+=trunk
    if is_return:
        return total_data     #返回接受到的所有数据
    else:
        return None
```