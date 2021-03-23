import scrapy
from ..items import  HouseofindyaItem

class DescSpider(scrapy.Spider):
    name = "desc"
    start_urls = [
        'https://www.houseofindya.com/zyra/necklace-sets/cat',
    ]

    def parse(self, response):
        items= HouseofindyaItem()
        urls = response.xpath('//*[@id="JsonProductList"]/li/@data-url').extract()
        items['urls'] = response.xpath('//*[@id="JsonProductList"]/li/@data-url').extract()

        for url in urls:
            url = response.urljoin(url)
            yield scrapy.Request(url=url, callback=self.parse_descriptions)
        yield items

    def parse_descriptions(self, response):
        items = HouseofindyaItem()
        items['title'] =  response.xpath('//*[@id="wrapper"]/div[4]/div/div[2]/div[2]/h1/text()').extract(),
        items['price'] = response.xpath('//*[@id="wrapper"]/div[4]/div/div[2]/div[2]/h4/span[2]/text()').extract(),
        items['description'] = response.css('#tab-1 p::text').extract(),

        yield items
