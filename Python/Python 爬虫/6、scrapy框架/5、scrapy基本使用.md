```
1. scrapy项目的结构
    项目名字
        项目名字
            spiders文件夹 （存储的是爬虫文件）
                init
                自定义的爬虫文件    核心功能文件  ****************
            init
            items        定义数据结构的地方 爬取的数据都包含哪些
            middleware   中间件    代理
            pipelines    管道   用来处理下载的数据
            settings     配置文件    robots协议  ua定义等

2. response的属性和方法
    response.text   获取的是响应的字符串
    response.body   获取的是二进制数据
    response.xpath  可以直接是xpath方法来解析response中的内容
    response.extract()   提取seletor对象的data属性值
    response.extract_first() 提取的seletor列表的第一个数据
```

## 应用实例：

```
import scrapy
from project_name.items import ProjectNameItem
class BaiduSpider(scrapy.Spider):
    name = 'baidu'
    allowed_domains = ['category.dangdang.com']
    start_urls = ['http://category.dangdang.com/cp01.01.02.00.00.00.html']
    def parse(self, response):
        # # print(response.text)
        # result_list=response.xpath("//input[@id='su']/@value")
        # print(result_list)
        # print("运行了！！！")
        '''
            //ul[@id='component_59']/li[1]/a/img/@src              图片url 第一本
            //ul[@id='component_59']/li/a/img/@data-original    图片url
            //ul[@id='component_59']/li/a/img/@alt              书名
            //ul[@id='component_59']/li/p[3]/span[1]/text()           价格
        '''
        photo_first=response.xpath("//ul[@id='component_59']/li[1]/a/img/@src  ")
        print(photo_first.extract_first())    #第一本书的图片
        photo_list=response.xpath("//ul[@id='component_59']/li/a/img/@data-original") #后面书的图片
        for photo in photo_list:
            print(photo.extract())
        book_name_list=response.xpath("//ul[@id='component_59']/li/a/img/@alt ")  #书的名字
        for book_name in book_name_list:
            print(book_name.extract())
        book_price_list=response.xpath("//ul[@id='component_59']/li/p[3]/span[1]/text()") #书的价格
        for book_price in book_price_list:
            print(book_price.extract())
            
        # li_list=response.xpath("//ul[@id='component_59']/li")
        # for li in li_list[0:1]:          #第一本书的相关信息
        #     imgSrc="http:"+li.xpath("./a/img/@src").extract()[0]
        #     name=li.xpath("./a/img/@alt ").extract()[0]
        #     price=li.xpath("./p[3]/span[1]/text()").extract()[0]
        #     book=ProjectNameItem(book_name=name,book_price=price,book_imgSrc=imgSrc)
        #     yield book      #返回给管道
        #
        # for li in li_list[1:]:          #后面几本书的相关信息
        #     imgSrc="http:"+li.xpath("./a/img/@data-original").extract()[0]
        #     name=li.xpath("./a/img/@alt ").extract()[0]
        #     price=li.xpath("./p[3]/span[1]/text()").extract()[0]
        #     book=ProjectNameItem(book_name=name,book_price=price,book_imgSrc=imgSrc)
        #     yield book      #返回给管道
```