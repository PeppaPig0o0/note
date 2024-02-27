# Scrapy



## 基础知识

基于Twisted 异步网络框架

中文文档https://scrapy-chs.readthedocs.io/zh-cn/1.0/topics/jobs.html

![image-20231216202806533](E:\笔记\爬虫\assets\image-20231216202806533.png)

### 三个内置对象

- request请求对象:由url、method、post_data、headers等构成
- response响应对象:由url、body、status、headers等构成
- item数据对象:本质是个字典

### 各模块作用

![image-20231216205332998](E:\笔记\爬虫\assets\image-20231216205332998.png)

### 安装 

```
pip install scrapy
pip install scrapy -i https://pypi.douban.com/simple
```

可能有twisted安装问题，导致安装失败

​	1#如果报错，需要安装twisted库   ,  http://www.lfd.uci.edu/~gohlke/pythonlibs/#twisted   ,根据python版本进行下载，然后

```
pip install C:\........\Twisted-........whl  
pip install scrapy 
```

​	2#如果报错需要更新pip

```
python -m pip install --upgrade pip
```

​	3#win32报错

```
pip install pypiwin32
```

​	4# 如果还报错，使用  anaconda  ,  建议先安装 anaconda , 卸载系统Python解释器，使用anaconda环境

### 创建项目

```bash
# 1.创建项目:
scrapy startproject mySpider(爬虫项目名)

# 2.创建爬虫     爬虫名字 允许爬取的域名
scrapy genspider itcast itcast.cn

# 3.提取数据:
根据网站结构在spider中实现数据采集相关内容

# 4.保存数据
使用pipeline进行数据后续处理和保存
```

- spiders目录：主要存放爬虫
- settings.py ：项目配置文件
- items.py:  定义数据存储模型
- middlewares.py ：中间件
- pipelines.py ：  数据处理相关,数据清洗保存等相关

## 编写爬虫

修改起始url

检查允许修改的域

在parse方法中实现爬取逻辑

```python
response.xpath('//div[@class="lay_con"]//li')
# 返回 Selector对象
```

extract() 和 extract_first()

```
# 从Selector对象中解析结果
li.xpath('.//img[@class="mask_img1"]/@src').extract
li.xpath('.//img[@class="mask_img1"]/@src').extract_first()
```

```
yeild 返回数据
```



## response对象

- response.url——当前响应的url地址
- response.request.url——当前响应对应的请求的url地址
- response.headers——响应头
- response.requests.headers——当前响应的请求头。
- response.body——响应体，也就是html代码，byte类型
- response.status——响应状态码
- response.text
- response.json()
- response.xpath()
- response.css()

## Request请求对象

Request()





## 保存数据

### pipelines.py

```python
class JwxtPipeline:
    # 开启爬虫时候执行
    def open_spider(self, spider):
        self.file = open('data.csv', 'w', encoding='utf-8')
        self.file.write()
        
    def process_item(self, item, spider):
        return item
    
	# 关闭爬虫时候执行
    def close_spider(self, spider):
        self.file.close()
```

如果项目中有多个spider，运行某个spider时只使用对应的pipeline ——> 使用 spider.name

```python
class HrwangyiPipeline:
    def open_spider(self, spider):
        # 判断某个爬虫是不是对应的爬虫
        if spider.name == 'hr_wangyi':
            self.file = open('hr_wangyi.json', 'w', encoding='utf-8')

    def process_item(self, item, spider):
        if spider.name == 'hr_wangyi':
            item['job_num'] = item['job_num'].replace('\t', '').replace('\r\n', '').replace('\n', '').strip()
            print(item)
            json_data = json.dumps(dict(item), ensure_ascii=False) + ',\n'
            self.file.write(json_data)
        return item  # 注意这里

    def close_spider(self, spider):
        if spider.name == 'hr_wangyi':
            self.file.close()
```

### ImagesPipeline

图片下载管道ImagesPipeline

```python
import scrapy
from scrapy.pipelines.images import ImagesPipeline

...

class ImagesDownloadPipeline(ImagesPipeline):
    # 重写下面几个方法
    # get_media_requests
    # item_completed
    # file_path
    def get_media_requests(self, item, info):
        # 负责下载 返回一个请求
        return scrapy.Request(item['image_url'])

    def file_path(self, request, response=None, info=None):
        # 生成文件路径
        file_name = request.url.split('/')[-1]
        return f'images/{file_name}'

    def item_completed(self, results, item, info):
        # 返回文件详细信息
        print(results)
```



```python
# setting.py中设置
ITEM_PIPELINES = {
   "feilu.pipelines.FeiluPipeline": 300,
   "feilu.pipelines.ImagesDownloadPipeline": 301,  # 开启管道
}

# 设置图片存储目录
IMAGES_STORE = "images"
```







## 重构start_requests方法

```python
    def start_requests(self):
        url = self.start_urls[0]
        temp = '复制过来的cookies'
        cookies = {data.split('=')[0]:data.split('=')[1] for data in temp.split(';')}
        yield scrapy.Request(url=url, callback=self.parse, cookies=cookies)
```

发送POST请求

 

```
scrapy.FromRequest类
```

## crawlspider

继承Spider,自动根据规则,提取链接并发送请求给引擎

crawlspider经常应用于数据在一个页面上进行采集的情况，如果数据在多个页面上采集，这个时候通常使用spider。因为crawlspider用meta传参不太方便

```python
# 自动生成的模板
import scrapy
# 链接提取器
from scrapy.linkextractors import LinkExtractor
from scrapy.spiders import CrawlSpider, Rule  # 规则


class TencentCrawlSpider(CrawlSpider):
    name = "tencent_crawl"
    allowed_domains = ["tencent.com"]
    start_urls = ["https://tencent.com"]
    
    # 链接提取规则(元组)
	# 使用Rule对象，生成链接提取对象
    rules = (Rule(LinkExtractor(allow=r"Items/"), callback="parse_item", follow=True),)

    # 没有了parse方法
    def parse_item(self, response):
        item = {}
        #item["domain_id"] = response.xpath('//input[@id="sid"]/@value').get()
        #item["name"] = response.xpath('//div[@id="name"]').get()
        #item["description"] = response.xpath('//div[@id="description"]').get()
        return item

```

### Rule

### LinkExtractor

```python
# LinkExtractor用于设置链接提取规则，一般使用allow参数，接收正则表达式
# follow参数决定是否在链接提取器提取的链接对应的响应中继续应用链接提取器提取链接
#使用Rule类生成链接提取规则对象
LinkExtractor(allow=r'Items/')，callback='parse_item',follow=True)
```

## 中间件

下载中间件和爬虫中间件

对header以及cookie进行更换和处理；使用代理ip；对请求进行定制化操作等

中间件编写在 middlewares.py文件中

下载中间件用的比较多，爬虫中间件比较少

权重值越小，优先级越高

### Downloader Middlewares

```python
def process_request(self, request, spider):
	"""
	通常写的比较多
	1. 当每个request通过下载中间件时，该方法被调用
	2. 当返回None时：通过引擎传递给其他权重低的process_request方或，该request对象传递给下载器。如果所有中间件都返回None，则request对象最终被交给下载器。
	3. 返回Response对象：不再请求，把response返回引擎（相当于下载器被截断，直接通过中间件获取了response）
	4. 返回Request对象：把request对象通过引擎交还给调度器，此时将不通过其他权重低的process_request方法（其他下载中间件）。如果返回为请求，则将请求交给调度器（通过引擎交给）。
	"""
	
    return None

def process_response(self, request, response, spider):
    """
    通常写的比较少
    1.当下载器完成http请求，传递响应给引擎的时候调用
    2.返回Resposne: 通过引擎交给爬虫处理或交给权重更低的其他下载中间件的process_response方
    法
    3.返回Request对象: 通过引擎交给调取器继续请求，此时将不通过其他权重低的process_request方
    送
	"""
    return response
```

### 随机UA

middlewares.py中

```python
import random
from fake_useragent import UserAgent

class RandomUserAgentMiddleware(object):
    def __init__(self, crawler):
        super(RandomUserAgentMiddleware, self).__init__()
        self.ua = UserAgent()
        self.ua_type = crawler.settings.get('RANDOM_UA_TYPE', 'random')

    @classmethod
    def from_crawler(cls, crawler):
        return cls(crawler)

    def process_request(self, request, spider):
        def get_ua():
            return getattr(self.ua, self.ua_type)
        request.headers.setdefault('User-Agent', get_ua())
```

settings.py中

```python
# 下载中间件
DOWNLOADER_MIDDLEWARES = {
    "douban.middlewares.DoubanDownloaderMiddleware": None,
    "douban.middlewares.RandomUserAgentMiddleware": 400,
    'scrapy.downloadermiddlewares.useragent.UserAgentMiddleware': None,
}
```

### 随机IP

```

```

### Selenium

```python
from selenium import webdriver
from scrapy.http import HtmlResponse
class SeleniumMiddleware(object):
    def process_request(self, request, spider):
        url = request.url
        if '某字段' in url:
            # 这里写各种selenium代码
            service = webdriver.ChromeService(executable_path=r'chromedriver.exe路径')
            driver = webdriver.Chrome(service=service)
            driver.get(url)
            time.sleep(3)
            # 获取网页源码
            data = driver.page_source
            driver.quit()

            # 创建响应对象，返回Response对象
            res = HtmlResponse(url=url, body=data, encoding='utf-8', request=request)
             return res
```

## scrapy-Redis

scrapy 是一个通用的爬虫框架，但是不支持分布式，
scrapy-Redis 是为了更方便地实现scrapy分布式爬取，而提供了一些以redis为基础的组件(仅有组件)

### 安装

```bash
pip install scrapy-redis
```



### 介绍

scrapy-redis提供了四种组件

- Scheduler调度器
- Duplication Fileter过滤器
- Item Pipeline管道
- Base Spider基础分布式爬虫类

![clipboard.png](E:\笔记\爬虫\Scrapy.assets\watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0hhcnJ5dHN6,size_16,color_FFFFFF,t_70.png)

分布式策略

1.首先Slaver端从Master端拿任务 (Request、 url) 进行数据抓取，Slaver抓取数据的同时，产生新任务的Request便提交给 Master 处理：

2.Master端只有一个Redis数据库，负责将未处理的Request去重和任务分配，将处理后的Request加入待爬队列，并且存储爬取的数据。

Scrapy-Redis默认使用的就是这种策略，我们实现起来很简单，因为任务调度等工作Scrapy-Redis都已经帮我们做好了，我们只需要继承RedisSpider、指定redis_key就行了。

缺点是，Scrapy-Redis调度的任务是Request对象，里面信息量比较大(不仅包含url，还有callback函数.headers等信息)，可能导致的结果就是会降低爬虫速度、而且会占用Redis大量的存储空间，所以如果要保证效率，那么就需要一定硬件水平。

![clipboard.png](E:\笔记\爬虫\Scrapy.assets\watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0hhcnJ5dHN6,size_16,color_FFFFFF,t_70-1703211186323-3.png)

### 拿取样本

使用scarpy-redis的example来修改

```bash
1.样本
git clone git@github.com:rmax/scrapy-redis.git
```

```bash
2.样本爬虫介绍
# dmoz.py 普通crawlspider爬虫，不需要
# myspider_redis.py  分布式Spider模板
# mycrawler_redis.py  分布式CrawlSpider模板

Spider       ====>  RedisSpider
Craw1Spider  ====>  RedisCrawlSpider
name         ====>  name
start_urls   ====>  redis_key
__init__()   ====>  allowed_domains

[注]_init__()是一个坑，现在还是使用allowed_domains这种列表的形式
```

```
3.存储到redis中
scrapy-redis组件已经写好往redis中存放的管道，只需要使用即可，默认存储到本机的redis服务中如果想存储到其它的redis服务中，需要在配置文件中配器
REDIS_HOST='ip地址'
REDIS_PORT=6379
#也可以使用 REDIS_URL ="redis://密码@123.60.79.108:6379/4"
```

### 流程

新建scrapy项目和爬虫

把scarpy-redis样本中的这些文件的代码复制到，scrapy项目对应的文件中

```python
# 1.setting.py
# scarpy-redis.example.setting.py   ====>   scrapy.setting.py

# 去重器
DUPEFILTER_CLASS = "scrapy_redis.dupefilter.RFPDupeFilter"
# 使用scrapy-redis的调度器
SCHEDULER = "scrapy_redis.scheduler.Scheduler"
#是否允许暂停
SCHEDULER_PERSIST = True

# 管道
ITEM_PIPELINES = {
    # 加入这个，使用redis管道，自动存入redis，默认存入本机
    'scrapy_redis.pipelines.RedisPipeline': 400,
}

# 日志级别
LOG_LEVEL = 'DEBUG'



# 远程链接Redis(不需要认证)
# REDIS_HOST = '123.60.79.108'
# REDIS_PORT = 6379
# 远程链接Redis（第一种）
# REDIS_HOST = '123.60.79.108'
# REDIS_PORT = 6379
# REDIS_ENCODING = 'utf-8'
# REDIS_PARAMS = {'password': '007741ak'}
# 远程链接Redis(第二种)
# REDIS_URL ="redis://:密码@123.60.79.108:6379/3"
REDIS_URL = "redis://:007741ak@123.60.79.108:6379/3"
```

```python
# 1.setting.py
# scarpy-redis.example.爬虫文件.py   ====>   scrapy.爬虫文件.py

# import scrapy
from scrapy_redis.spiders import RedisSpider, RedisCrawlSpider
from scrapy.linkextractors import LinkExtractor
from scrapy.spiders import Rule

# start_urls = ["https://faloo.com"]
# 监听当前爬虫连接的redis库中指定的key为"爬虫:start_urls"的key
redis_key = 'free:start_urls'
```

### 运行

```bash
# slaver端， 开启了一个slaver，(前台阻塞)
# 语法：scrapy runspider 爬虫文件路径.py
scrapy runspider free.py

# 可以通过多个终端，开启多个slaver
```

```sql
# 因为爬虫没有start_urls
# 向redis中添加起始url
# 语法：lpush 爬虫名:start_urls "起始地址"
lpush free:start_urls "https://b.faloo.com/y_0_0_0_0_6_1_1.html"
```

