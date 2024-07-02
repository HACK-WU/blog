# 第一节 创建父工程

![](images/WEBRESOURCEd351ea3a87def04317a4957a5e407a15截图.png)

![](images/WEBRESOURCE0f348bc73f168187894011674738964fstickPicture.png)

![](images/WEBRESOURCEf43e5d155b000f5b3363b5f2e0e8f2a2stickPicture.png)

创建 Project 后，IDEA 会自动弹出下面提示，我们选择

![](images/WEBRESOURCEf48f6dee1e1d0e20ab59204c2e4489fa截图.png)

这个自动导入

另外也可以通过 IDEA 的 Settings 设置来开启：

![](images/WEBRESOURCE9bf010107c172ca70bbca7aa42010eeastickPicture.png)

# 第二节 配置Maven信息

每次创建 Project 后都需要设置 Maven 家目录位置，否则 IDEA 将使用内置的 Maven 核心程序（不稳定）并使用默认的本地仓库位置。这样一来，我们在命令行操作过程中已下载好的 jar 包就白下载了，默认的本地仓库通常在 C 盘，还影响系统运行。

配置之后，IDEA 会根据我们在这里指定的 Maven 家目录自动识别到我们在 settings.xml 配置文件中指定的本地仓库。

![](images/WEBRESOURCE9275d80b10bf6eb1834b533d432aa401stickPicture.png)

# 第三节 创建Java模块工程

![](images/WEBRESOURCE33fda42172ca52a11ba94a44295cea00stickPicture.png)

![](images/WEBRESOURCE73975f675a386e0f534cfa0b75458a95stickPicture.png)

# 第四节 创建Web模块工程

按照前面的同样操作创建模块，

Web 模块将来打包当然应该是 

```
<packaging>war</packaging>
```

1

首先打开项目结构菜单：

![](images/WEBRESOURCEa7fc5905e261a8f17b4f6d8b1c7f5b18截图.png)

然后到 Facets 下查看 IDEA 是否已经帮我们自动生成了 Web 设定。正常来说只要我们确实设置了打包方式为 war，那么 IDEA 2019 版就会自动生成 Web 设定。

![](images/WEBRESOURCE9399ac363fb2f12d78037a443cda91cestickPicture.png)

另外，对于 IDEA 2018 诸版本没有自动生成 Web 设定，那么请参照下面两图，我们自己创建：

![](images/WEBRESOURCE42de1395dd1696210c809c3c3a0ca09estickPicture.png)

![](images/WEBRESOURCEe444fc073fc9c68cf16be56ffe611674stickPicture.png)

![](images/WEBRESOURCE99f3b8c279ef73d232f431f0612f6c66截图.png)

![](images/WEBRESOURCE42bd15410eb075f43ad93bec72cd13e8stickPicture.png)

结合 Maven 的目录结构，Web 资源的根目录需要设置为 src/main/webapp 目录。

![](images/WEBRESOURCEb2e232b835186307d401e3cb378a292fstickPicture.png)

![](images/WEBRESOURCE31352368e5a0e8fbb5784ae1b499edc1截图.png)

# 第五节 其他操作

![](images/WEBRESOURCE2ea93c68d0d8c3f055799c44722a186cstickPicture.png)

![](images/WEBRESOURCEcefe75d9e8263e7f189e139d39aec76d截图.png)

![](images/WEBRESOURCE03b2efedd566aad53957af3206a03ed1截图.png)

![](images/WEBRESOURCE11b3ec911aab93e69e8f6da017ee2467stickPicture.png)

![](images/WEBRESOURCE69c3008c3ed1689ec9f6d84b30df4e18截图.png)

如果有需要，还可以给命令后面附加参数：

![](images/WEBRESOURCEf6bc81a3eb70a7a2e5b9006a6f6a537a截图.png)

```
 -D 表示后面要附加命令的参数，字母 D 和后面的参数是紧挨着的，中间没有任何其它字符
 maven.test.skip=true 表示在执行命令的过程中跳过测试
mvn clean install -Dmaven.test.skip=true
```

1

2

3

![](images/WEBRESOURCEe752b0f6308b8b1b31b216c3e8fa0507stickPicture.png)

Maven工程除了自己创建的，还有很多情况是别人创建的。而为了参与开发或者是参考学习，我们都需要导入到 IDEA 中。下面我们分几种不同情况来说明：

目前我们通常使用的都是 Git（本地库） + 码云（远程库）的版本控制系统，结合 IDEA 的相关操作方式请点[**这里** (opens new window)](http://heavy_code_industry.gitee.io/code_heavy_industry/pro008-Git/lecture/chapter05/verse03.html)查看

直接使用 IDEA 打开工程目录即可。下面咱们举个例子：

假设别人发给我们一个 Maven 工程的 zip 压缩包：maven-rest-demo.zip。从码云或GitHub上也可以以 ZIP 压缩格式对项目代码打包下载。

如果你的所有 IDEA 工程有一个专门的目录来存放，而不是散落各处，那么首先我们就把 ZIP 包解压到这个指定目录中。

![](images/WEBRESOURCE1add31d801ec0e89b47ba287b2f0ba2estickPicture.png)

只要我们确认在解压目录下可以直接看到 pom.xml，那就能证明这个解压目录就是我们的工程目录。那么接下来让 IDEA 打开这个目录就可以了。

![](images/WEBRESOURCEe26e8f86696e1fd3daa952284602e919截图.png)

![](images/WEBRESOURCEa31f8b8e0a2b9a50d6903188688f06fcstickPicture.png)

打开一个新的 Maven 工程，和新创建一个 Maven 工程是一样的，此时 IDEA 的 settings 配置中关于 Maven 仍然是默认值：

![](images/WEBRESOURCEe4c9722bb89d631827800d396d0e9d25stickPicture.png)

所以我们还是需要像新建 Maven 工程那样，指定一下 Maven 核心程序位置：

![](images/WEBRESOURCE052a896ccae20f701059caefd7c8e5aestickPicture.png)

在实际开发中，通常会忽略模块（也就是module）所在的项目（也就是project）仅仅导入某一个模块本身。这么做很可能是类似这样的情况：比如基于 Maven 学习 SSM 的时候，做练习需要导入老师发给我们的代码参考。

![](images/WEBRESOURCE351b8d74cb5660a1b759a68401d9a3ccstickPicture.png)

![](images/WEBRESOURCE7facb44e65f4a8fe2102af42d1f59c30截图.png)

![](images/WEBRESOURCE64df0e1d8b7ea8d7b68092ca82e9f546截图.png)

这个工程（project）是我们事先在 IDEA 中创建好的。

![](images/WEBRESOURCE0842b2e40d9c3b0b3937e3853351212estickPicture.png)

![](images/WEBRESOURCEba234862e433ba3ae4d534788e623390截图.png)

![](images/WEBRESOURCE140d3b86d5fde698d9a230db253df6d8截图.png)

![](images/WEBRESOURCE0f9a612f849f94afaf12240bef1510f6stickPicture.png)

![](images/WEBRESOURCE0dc1a411c16df5b429b6c2ea2c9f9f7bstickPicture.png)

![](images/WEBRESOURCE112c78616c3764e13df63aed4d4320eestickPicture.png)

![](images/WEBRESOURCEd5b925c720907617c293e47cf25cccfcstickPicture.png)

刚刚导入的 module 的父工程坐标还是以前的，需要改成我们自己的 project。

![](images/WEBRESOURCEa2650ae6e0b081252964e0b9d9a44df3stickPicture.png)

![](images/WEBRESOURCE918d62a16747c6923690c5756113363dstickPicture.png)

![](images/WEBRESOURCE11774edca72e93b3e485dde9145c89c8stickPicture.png)

其它操作和上面演示的都一样，只是多一步：删除多余的、不正确的 web.xml 设置。如下图所示：

![](images/WEBRESOURCEa5ac4bed3821701073df83ba73113950stickPicture.png)