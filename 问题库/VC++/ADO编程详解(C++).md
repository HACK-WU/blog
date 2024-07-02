# ADO编程详解(C++)帅气滴点C 2019-01-04 20:49:15  7665  收藏 57

![](https://csdnimg.cn/release/blogv2/dist/pc/img/tobarCollect.png)

![](https://csdnimg.cn/release/blogv2/dist/pc/img/articleReadEyes.png)

![](https://csdnimg.cn/release/blogv2/dist/pc/img/reprint.png)

分类专栏： C/C++ 数据库

# 一、概述

 ADO即Microsoft ActiveXData Object，是Microsoft继ODBC之后，基于OLE DB技术的一种数据库操作技术，使您能够编写通过 OLE DB 提供者对在数据库服务器中的数据进行访问和操作的应用程序。ADO同OLE DB、数据库之间的关系可以用下图来表示：



![](http://www.myexception.cn/img/2013/01/23/104922502.jpg)

 其主要优点是易于使用、高速度、低内存支出和占用磁盘空间较少。ADO 支持用于建立基于客户端/服务器和 Web 的应用程序的主要功能。

# 二、ADO初步认识

 在此介绍的ADO编程技术都是基于C++的，要熟练掌握如何用ADO操作数据库，首先对ADO技术得有个总体的了解，AD0主要由几个对象组成：Connection、Command、Paramenter、Recorderset、Fields、Error、Property对象，对数据库的操作，都是通过这几个对象来进行的，对象模型的关系图如下：



![](http://www.myexception.cn/img/2013/01/23/104922503.jpg)

 

##  2.1 Connection对象

在数据库应用里操作数据源都必须通过该对象，这是数据交换的环境。Connection对象代表了同数据源的一个会话，在客户/服务器模型里，这个会话相当于同服务器的一次网络连接。不同的数据提供者提供的该对象的集合、方法和属性不同。

借助于Connection对象的集合、方法和属性，可以使用Open和Close方法建立和释放一个数据源连接。使用Execute方法可以执行一个数据操作命令，使用BeginTrans、CommitTrans和RollbackTrans方法可以启动、提交和回滚一个处理事务。通过操作the Errors 集合可以获取和处理错误信息，操作CommandTimeout属性可以设置连接的溢出时间，操作ConnectionString属性可以设置连接的字符串，操作Mode属性可以设置连接的模式，操作Provider属性可以指定OLE DB提供者。

## 2.2 Command对象

Command对象是一个对数据源执行命令的定义，使用该对象可以查询数据库并返回一个Recordset对象，可以执行一个批量的数据操作，可以操作数据库的结构。不同的数据提供者提供的该对象的集合、方法和属性不同。

借助于Command对象的集合、方法和属性，可以使用Parameters集合制定命令的参数，可以使用Execute方法执行一个查询并将查询结果返回到一个Recordset对象里，操作CommandText属性可以为该对象指定一个命令的文本，操作CommandType属性可以指定命令的类型，操作Prepared可以得知数据提供者是否准备好命令的执行，操作CommandTimeout属性可以设置命令执行的溢出时间。

## 2.3 Parameter对象

Parameter对象在Command对象中用于指定参数化查询或者存储过程的参数。大多数数据提供者支持参数化命令，这些命令往往是已经定义好了的，只是在执行过程中调整参数的内容。

借助于Parameter对象的集合、方法和属性，可以通过设置Name属性指定参数的名称，通过设置Value属性可以指定参数的值，通过设置Attributes和Direction、Precision、NumericScale、Size与Type 属性可以指定参数的信息，通过执行AppendChunk方法可以将数据传递到参数里。

## 2.4 Recordset对象

如果执行的命令是一个查询并返回存放在表中的结果集，这些结果集将被保存在本地的存储区里，Recordset对象是执行这种存储的ADO对象。通过Recordset对象可以操纵来自数据提供者的数据，包括修改和更新行、插入和删除行。

 

## 2.5   Field对象

Recordset对象的一个行由一个或者多个Fields对象组成，如果把一个Recordset对象看成一个二维网格表，那么Fields对象就是这些列。这些列里保存了列的名称、数据类型和值，这些值是来自数据源的真正数据。为了修改数据源里的数据，必须首先修改Recordset对象各个行里Field对象里的值，最后Recordset对象将这些修改提交到数据源。

借助于Field对象的集合、方法和属性，可以通过读取Name属性，获知列的名称。通过操作Value属性可以改变列的值，通过读取Type、Precision和NumericScale 属性，可获知列的数据类型、精度和小数位的个数，通过执行AppendChunk 和GetChunk 方法可以操作列的值。

## 2.6 Error对象

Error对象包含了ADO数据操作时发生错误的详细描述，ADO的任何对象都可以产生一个或者多个数据提供者错误，当错误发生时，这些错误对象被添加到Connection 对象的Errors集合里。当另外一个ADO对象产生一个错误时，Errors集合里的Error对象被清除，新的Error对象将被添加到Errors集合里。

借助于Errosr对象的集合、方法和属性，可以通过读取Number和Description属性，获得ADO错误号码和对错误的描述，通过读取Source属性得知错误发生的源。

## 2.7  Property对象

Property对象代表了一个由提供者定义的ADO对象的动态特征。ADO对象有两种类型的Property对象：内置的和动态的。内置的Property对象是指那些在ADO里实现的在对象创建时立即可见的属性，可以通过域作用符直接操作这些属性。动态的Property对象是指由数据提供者定义的底层的属性，这些属性出现在ADO对象的Properties集合里，例如，如果一个Recordset 对象支持事务和更新，这些属性将作为Property对象出现在Recordset对象的Properties集合里。动态属性必须通过集合进行引用，比如使用下面的语法：

   MyObject.Properties(0)   

或者

        MyObject.Properties("Name")

不能删除任何类型的属性对象。借助于Property对象的集合、方法和属性，可以通过读取Name属性获得属性的名称，通过读取Type属性获取属性的数据类型，通过读取Value属性获取属性的值。

 

# 三、ADO编程

通常情况下，一个基于ADO的数据库应用使用如下过程操作数据源里的数据：

(1) 创建一个Connection对象。定义用于连接的字符串信息，包括数据源名称、用户ID、口令、连接超时、缺省数据库以及光标的位置。一个Connection 对象代表了同数据源的一次会话。可以通过Connection 对象控制事务，即执行BeginTrans、CommitTrans和RollbackTrans方法。

(2) 打开数据源，建立同数据源的连接。

(3) 执行一个SQL命令。一旦连接成功，就可以运行查询了。可以以异步方式运行查询，也可以异步地处理查询结果，ADO会通知提供者后台提供数据。这样可以让应用程序继续处理其它事情而不必等待。

(4) 使用结果集。完成了查询以后，结果集就可以被应用程序使用了。在不同的光标类型下，可以在客户端或者服务器端浏览和修改行数据。

(5) 终止连接。当完成了所有数据操作后，可以销毁这个同数据源的连接。

## 3.1准备工作

### 引入ADO库

   ADO是以DLL封装的，要使用ADO，首先得引入其DLL库，引入ADO类型库的方法有多种，在此我们就介绍一种----通过预处理指令#import引入。一般情况下，在windows操作系统的Program Files\Common Files\System\ado目录下都有一个msado*.dll文件,根据windows版本不同,该文件可以是msado1.dll、msado15.dll、msado2.dll，对于此文件，版本不一样，可能导致一些冲突，如：系统自带的msado*.dll可能与程序编译所用的版本不一样，可能会导致一些异常，那么对于这种情况，我个人一般的做法是，在软件目录下面带上msado*.dll文件，这样就排除了对软件运行环境的依赖。

 Ado类型库引入后，程序在编译过程中，VC++会读出msado*.dll中的类型库信息，自动产生两个该类型库的头文件和实现文件msado15.tlh和msado15.tli（在您的Debug或Release目录下）。在这两个文件里定义了ADO的所有对象和方法，以及一些枚举型的常量等。以下是引入方法：

```javascript



```

