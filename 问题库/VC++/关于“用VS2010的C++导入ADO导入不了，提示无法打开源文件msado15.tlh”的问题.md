# 关于“用VS2010的C++导入ADO导入不了，提示无法打开源文件msado15.tlh”的问题

vc++2010中，要使用ado操作数据库，所以在stdafx.h中引入了ado的dll库，引入代码如下:



```javascript
#import “C:/Program Files/Common Files/System/ado/msado15.dll” no_namespace rename(“EOF”,”adoEOF”)rename(“BOF”,”adoBOF”)
```

编译器提示intelliSense: 无法打开源文件 …/Debug/msado15.tlh的错误

解决办法很简单:在----- 生成菜单--- 重新生成 --- 即可。原因是：

当编译器遇到#import语句时，它会为引用组件类型库中的接口生成包装类，#import语句实际上相当于执行了API涵LoadTypeLib()。#import语句会在工程可执行程序输出目录中产生两个文件，分别为*.tlh(类型库头文件)及*.tli(类型库实现文件)，它们分别为每一个接口产生智能指针，并为各种接口方法、枚举类型，CLSID等进行声明，创建一系列包装方法。在没有经过编译器编译之前，文件还没有生成，所以，会出现错误提示。