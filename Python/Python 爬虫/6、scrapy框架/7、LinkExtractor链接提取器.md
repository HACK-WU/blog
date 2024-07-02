# 示例

```
import scrapy
from scrapy.linkextractors import LinkExtractor
from scrapy.spiders import CrawlSpider, Rule
'''
    /pg3-cp01.01.02.00.00.00.html
'''
class ReadSpider(CrawlSpider):
    name = 'read'
    allowed_domains = ['category.dangdang.com']
    start_urls = ['http://category.dangdang.com/cp01.01.02.00.00.00.html']
    rules = (
        Rule(LinkExtractor(allow=r'/pg\d+-cp01.01.02.00.00.00.html'), #连接提取器
             callback='parse_item',         #符合条件的话回调的函数
             follow=True),
    )
    def parse_item(self, response):
        li_list=response.xpath("//ul[@id='component_59']/li[60]")
        for li in li_list[0:1]:          #第一本书的相关信息
            imgSrc="http:"+li.xpath("./a/img/@src").extract()[0]
            name=li.xpath("./a/img/@alt ").extract()[0]
            price=li.xpath("./p[3]/span[1]/text()").extract()[0]
            print(name)
            print(response.url)
```