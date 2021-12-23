---
title: 新闻推荐之离线物料系统构建
date: 2021-12-21 14:22:35
tags: [推荐系统,爬虫]
categories: 人工智能
widgets: null
password: 328
abstract: 这是一篇加密博文，请输入密码后查看
message: 这里需要密码才能访问。
wrong_pass_message: 抱歉, 这个密码看着不太对, 请再试试.
---

### Scrapy的简介与安装

<!--more-->

Scrapy 是一种快速的高级 web crawling 和 web scraping 框架，**用于对网站内容进行爬取，并从其页面提取结构化数据**。

Ubuntu下安装Scrapy，需要先安装依赖Linux依赖

```C++
sudo apt-get install python3 python3-dev python3-pip libxml2-dev libxslt1-dev zlib1g-dev libffi-dev libssl-dev
```

在新闻推荐系统虚拟conda环境中安装scrapy

```C++
pip install scrapy
```

#### scrapy项目结构

默认情况下，所有scrapy项目的项目结构都是相似的，在指定目录对应的命令行中输入如下命令，就会在当前目录创建一个scrapy项目

```
scrapy startproject myproject
```

项目的目录结构如下：

```C++
myproject/
    scrapy.cfg
    
    myproject/  
        __init__.py
        items.py
        middlewares.py
        pipelines.py
        settings.py
        spiders/
            __init__.py
```

- scrapy.cfg: 项目配置文件
- myproject/ : 项目python模块, 代码将从这里导入
- **myproject/ items.py: 项目items文件，**
- **myproject/ pipelines.py: 项目管道文件，将爬取的数据进行持久化存储**
- myproject/ settings.py: 项目配置文件，可以配置数据库等
- **myproject/ spiders/: 放置spider的目录，爬虫的具体逻辑就是在这里实现的（具体逻辑写在spider.py文件中）,可以使用命令行创建spider，也可以直接在这个文件夹中创建spider相关的py文件**
- myproject/ middlewares：中间件，请求和响应都将经过他，可以配置请求头、代理、cookie、会话维持等

#### spider

**spider是定义一个特定站点（或一组站点）如何被抓取的类，包括如何执行抓取（即跟踪链接）以及如何从页面中提取结构化数据（即抓取项）。换言之，spider是为特定站点（或者在某些情况下，一组站点）定义爬行和解析页面的自定义行为的地方。**

爬行器是自己定义的类，Scrapy使用它从一个网站(或一组网站)中抓取信息。它们必须继承 `Spider` 并定义要做出的初始请求，可选的是如何跟随页面中的链接，以及如何解析下载的页面内容以提取数据。

对于spider来说，抓取周期是这样的：

1. 首先生成对第一个URL进行爬网的初始请求，然后指定一个回调函数，该函数使用从这些请求下载的响应进行调用。要执行的第一个请求是通过调用 `start_requests()` 方法，该方法(默认情况下)生成 `Request` 中指定的URL的 `start_urls` 以及 `parse` 方法作为请求的回调函数。
2. 在回调函数中，解析响应(网页)并返回 [item objects](https://www.osgeo.cn/scrapy/topics/items.html#topics-items) ， `Request` 对象，或这些对象的可迭代。这些请求还将包含一个回调(可能相同)，然后由Scrapy下载，然后由指定的回调处理它们的响应。
3. 在回调函数中，解析页面内容，通常使用 [选择器](https://www.osgeo.cn/scrapy/topics/selectors.html#topics-selectors) （但您也可以使用beautifulsoup、lxml或任何您喜欢的机制）并使用解析的数据生成项。
4. 最后，从spider返回的项目通常被持久化到数据库（在某些 [Item Pipeline](https://www.osgeo.cn/scrapy/topics/item-pipeline.html#topics-item-pipeline) ）或者使用 [Feed 导出](https://www.osgeo.cn/scrapy/topics/feed-exports.html#topics-feed-exports) .

**下面是官网给出的Demo:** 

```python
import scrapy

class QuotesSpider(scrapy.Spider):
    name = "quotes" # 表示一个spider 它在一个项目中必须是唯一的，即不能为不同的spider设置相同的名称。
	
    # 必须返回请求的可迭代(您可以返回请求列表或编写生成器函数)，spider将从该请求开始爬行。后续请求将从这些初始请求中相继生成。
    def start_requests(self):
        urls = [
            'http://quotes.toscrape.com/page/1/',
            'http://quotes.toscrape.com/page/2/',
        ]
        for url in urls:
            yield scrapy.Request(url=url, callback=self.parse) # 注意，这里callback调用了下面定义的parse方法
	
    # 将被调用以处理为每个请求下载的响应的方法。Response参数是 TextResponse 它保存页面内容，并具有进一步有用的方法来处理它。
    def parse(self, response):
        # 下面是直接从response中获取内容，为了更方便的爬取内容，后面会介绍使用selenium来模拟人用浏览器，并且使用对应的方法来提取我们想要爬取的内容
        page = response.url.split("/")[-2]
        filename = f'quotes-{page}.html'
        with open(filename, 'wb') as f:
            f.write(response.body)
        self.log(f'Saved file {filename}')
```

#### Xpath

**XPath 是一门在 XML 文档中查找信息的语言，XPath 可用来在 XML 文档中对元素和属性进行遍历。在爬虫的时候使用xpath来选择我们想要爬取的内容是非常方便的**，这里就提一下xpath中需要掌握的内容，参考资料中的内容更加的详细（建议花一个小时看看）。

要了解xpath, 需要先了解一下HTML（是用来描述网页的一种语言）, 这个的细节就不详细展开

**划重点：**

1. **xpath路径表达式：**XPath 使用路径表达式来选取 XML 文档中的节点或者节点集。这些路径表达式和我们在常规的电脑文件系统中看到的表达式非常相似。节点是通过沿着路径 (path) 或者步 (steps) 来选取的。

2. **了解如何使用xpath语法选取我们想要的内容，所以需要熟悉xpath的基本语法**

#### scrapy爬取新闻内容实战

在介绍这个项目之前先说一下这个项目的基本逻辑。

**环境准备：**

1. 首先Ubuntu系统里面需要安装好MongoDB数据库，这个可以参考开源项目MongoDB基础
2. python环境中安装好了scrapy, pymongo包

**项目逻辑：**

1. 每天定时从新浪新闻网站上爬取新闻数据存储到mongodb数据库中，并且需要监控每天爬取新闻的状态（比如某天爬取的数据特别少可能是哪里出了问题，需要进行排查）
2. 每天爬取新闻的时候只爬取当天日期的新闻，主要是为了防止相同的新闻重复爬取（当然这个也不能完全避免爬取重复的新闻，爬取新闻之后需要有一些单独的去重的逻辑）
3. 爬虫项目中实现三个核心文件，分别是sina.py（spider）,items.py（抽取数据的规范化及字段的定义），pipelines.py（数据写入数据库）

因为新闻爬取项目和新闻推荐系统是放在一起的，为了方便提前学习，下面直接给出项目的目录结构以及重要文件中的代码实现，最终的项目将会和新闻推荐系统一起开源出来

<img src="http://ryluo.oss-cn-chengdu.aliyuncs.com/图片image-20211103214124327.png" alt="image-20211103214124327" style="zoom: 80%;" />

1. **创建一个scrapy项目：**

```shell
scrapy startproject sinanews
```

2. **实现items.py逻辑**

```python
# Define here the models for your scraped items
#
# See documentation in:
# https://docs.scrapy.org/en/latest/topics/items.html

import scrapy
from scrapy import Item, Field

# 定义新闻数据的字段
class SinanewsItem(scrapy.Item):
    """数据格式化，数据不同字段的定义
    """
    title = Field() # 新闻标题
    ctime = Field() # 新闻发布时间
    url = Field() # 新闻原始url
    raw_key_words = Field() # 新闻关键词（爬取的关键词）
    content = Field() # 新闻的具体内容
    cate = Field() # 新闻类别
```

3. **实现sina.py (spider)逻辑**

   这里需要注意的一点，这里在爬取新闻的时候选择的是一个比较简洁的展示网站进行爬取的，相比直接去最新的新浪新闻观光爬取新闻简单很多，简洁的网站大概的链接：https://news.sina.com.cn/roll/#pageid=153&lid=2509&k=&num=50&page=1

<img src="http://ryluo.oss-cn-chengdu.aliyuncs.com/图片image-20211103213354334.png" alt="image-20211103213354334" style="zoom: 50%;" />

```python
# -*- coding: utf-8 -*-
import re
import json
import random
import scrapy
from scrapy import Request
from ..items import SinanewsItem
from datetime import datetime


class SinaSpider(scrapy.Spider):
    # spider的名字
    name = 'sina_spider'

    def __init__(self, pages=None):
        super(SinaSpider).__init__()

        self.total_pages = int(pages)
        # base_url 对应的是新浪新闻的简洁版页面，方便爬虫，并且不同类别的新闻也很好区分
        self.base_url = 'https://feed.mix.sina.com.cn/api/roll/get?pageid=153&lid={}&k=&num=50&page={}&r={}'
        # lid和分类映射字典
        self.cate_dict = {
            "2510":  "国内",
            "2511":  "国际",
            "2669":  "社会",
            "2512":  "体育",
            "2513":  "娱乐",
            "2514":  "军事",
            "2515":  "科技",
            "2516":  "财经",
            "2517":  "股市",
            "2518":  "美股"
        }

    def start_requests(self):
        """返回一个Request迭代器
        """
        # 遍历所有类型的新闻
        for cate_id in self.cate_dict.keys():
            for page in range(1, self.total_pages + 1):
                lid = cate_id
                # 这里就是一个随机数，具体含义不是很清楚
                r = random.random()
                # cb_kwargs 是用来向解析函数parse中传递参数的
                yield Request(self.base_url.format(lid, page, r), callback=self.parse, cb_kwargs={"cate_id": lid})
    
    def parse(self, response, cate_id):
        """解析网页内容，并提取网页中需要的内容
        """
        json_result = json.loads(response.text) # 将请求回来的页面解析成json
        # 提取json中我们想要的字段
        # json使用get方法比直接通过字典的形式获取数据更方便，因为不需要处理异常
        data_list = json_result.get('result').get('data')
        for data in data_list:
            item = SinanewsItem()

            item['cate'] = self.cate_dict[cate_id]
            item['title'] = data.get('title')
            item['url'] = data.get('url')
            item['raw_key_words'] = data.get('keywords')

            # ctime = datetime.fromtimestamp(int(data.get('ctime')))
            # ctime = datetime.strftime(ctime, '%Y-%m-%d %H:%M')

            # 保留的是一个时间戳
            item['ctime'] = data.get('ctime')

            # meta参数传入的是一个字典，在下一层可以将当前层的item进行复制
            yield Request(url=item['url'], callback=self.parse_content, meta={'item': item})
    
    def parse_content(self, response):
        """解析文章内容
        """
        item = response.meta['item']
        content = ''.join(response.xpath('//*[@id="artibody" or @id="article"]//p/text()').extract())
        content = re.sub(r'\u3000', '', content)
        content = re.sub(r'[ \xa0?]+', ' ', content)
        content = re.sub(r'\s*\n\s*', '\n', content)
        content = re.sub(r'\s*(\s)', r'\1', content)
        content = ''.join([x.strip() for x in content])
        item['content'] = content
        yield item 
```

4. **数据持久化实现，piplines.py**

   这里需要注意的就是实现SinanewsPipeline类的时候，里面很多方法都是固定的，不是随便写的，不同的方法又不同的功能，这个可以参考scrapy官方文档。

```python
# Define your item pipelines here
#
# Don't forget to add your pipeline to the ITEM_PIPELINES setting
# See: https://docs.scrapy.org/en/latest/topics/item-pipeline.html
# useful for handling different item types with a single interface
import time
import datetime
import pymongo
from pymongo.errors import DuplicateKeyError
from sinanews.items import SinanewsItem
from itemadapter import ItemAdapter


# 新闻item持久化
class SinanewsPipeline:
    """数据持久化：将数据存放到mongodb中
    """
    def __init__(self, host, port, db_name, collection_name):
        self.host = host
        self.port = port
        self.db_name = db_name
        self.collection_name = collection_name

    @classmethod    
    def from_crawler(cls, crawler):
        """自带的方法，这个方法可以重新返回一个新的pipline对象，并且可以调用配置文件中的参数
        """
        return cls(
            host = crawler.settings.get("MONGO_HOST"),
            port = crawler.settings.get("MONGO_PORT"),
            db_name = crawler.settings.get("DB_NAME"),
            # mongodb中数据的集合按照日期存储
            collection_name = crawler.settings.get("COLLECTION_NAME") + \
                "_" + time.strftime("%Y%m%d", time.localtime())
        )

    def open_spider(self, spider):
        """开始爬虫的操作，主要就是链接数据库及对应的集合
        """
        self.client = pymongo.MongoClient(self.host, self.port)
        self.db = self.client[self.db_name]
        self.collection = self.db[self.collection_name]
        
    def close_spider(self, spider):
        """关闭爬虫操作的时候，需要将数据库断开
        """
        self.client.close()

    def process_item(self, item, spider):
        """处理每一条数据，注意这里需要将item返回
        注意：判断新闻是否是今天的，每天只保存当天产出的新闻，这样可以增量的添加新的新闻数据源
        """
        if isinstance(item, SinanewsItem):
            try:
                # TODO 物料去重逻辑，根据title进行去重，先读取物料池中的所有物料的title然后进行去重

                cur_time = int(item['ctime'])
                str_today = str(datetime.date.today())
                min_time = int(time.mktime(time.strptime(str_today + " 00:00:00", '%Y-%m-%d %H:%M:%S')))
                max_time = int(time.mktime(time.strptime(str_today + " 23:59:59", '%Y-%m-%d %H:%M:%S')))
                if cur_time > min_time and cur_time <= max_time:
                    self.collection.insert(dict(item))
            except DuplicateKeyError:
                """
                说明有重复
                """
                pass
        return item
```

5. 配置文件，settings.py

```python
# Scrapy settings for sinanews project
#
# For simplicity, this file contains only settings considered important or
# commonly used. You can find more settings consulting the documentation:
#
#     https://docs.scrapy.org/en/latest/topics/settings.html
#     https://docs.scrapy.org/en/latest/topics/downloader-middleware.html
#     https://docs.scrapy.org/en/latest/topics/spider-middleware.html

from typing import Collection

BOT_NAME = 'sinanews'

SPIDER_MODULES = ['sinanews.spiders']
NEWSPIDER_MODULE = 'sinanews.spiders'


# Crawl responsibly by identifying yourself (and your website) on the user-agent
#USER_AGENT = 'sinanews (+http://www.yourdomain.com)'

# Obey robots.txt rules
ROBOTSTXT_OBEY = True

# Configure maximum concurrent requests performed by Scrapy (default: 16)
#CONCURRENT_REQUESTS = 32

# Configure a delay for requests for the same website (default: 0)
# See https://docs.scrapy.org/en/latest/topics/settings.html#download-delay
# See also autothrottle settings and docs
# DOWNLOAD_DELAY = 3
# The download delay setting will honor only one of:
#CONCURRENT_REQUESTS_PER_DOMAIN = 16
#CONCURRENT_REQUESTS_PER_IP = 16

# Disable cookies (enabled by default)
#COOKIES_ENABLED = False

# Disable Telnet Console (enabled by default)
#TELNETCONSOLE_ENABLED = False

# Override the default request headers:
#DEFAULT_REQUEST_HEADERS = {
#   'Accept': 'text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8',
#   'Accept-Language': 'en',
#}

# Enable or disable spider middlewares
# See https://docs.scrapy.org/en/latest/topics/spider-middleware.html
#SPIDER_MIDDLEWARES = {
#    'sinanews.middlewares.SinanewsSpiderMiddleware': 543,
#}

# Enable or disable downloader middlewares
# See https://docs.scrapy.org/en/latest/topics/downloader-middleware.html
#DOWNLOADER_MIDDLEWARES = {
#    'sinanews.middlewares.SinanewsDownloaderMiddleware': 543,
#}

# Enable or disable extensions
# See https://docs.scrapy.org/en/latest/topics/extensions.html
#EXTENSIONS = {
#    'scrapy.extensions.telnet.TelnetConsole': None,
#}

# Configure item pipelines
# See https://docs.scrapy.org/en/latest/topics/item-pipeline.html
# 如果需要使用itempipline来存储item的话需要将这段注释打开
ITEM_PIPELINES = {
   'sinanews.pipelines.SinanewsPipeline': 300,
}

# Enable and configure the AutoThrottle extension (disabled by default)
# See https://docs.scrapy.org/en/latest/topics/autothrottle.html
#AUTOTHROTTLE_ENABLED = True
# The initial download delay
#AUTOTHROTTLE_START_DELAY = 5
# The maximum download delay to be set in case of high latencies
#AUTOTHROTTLE_MAX_DELAY = 60
# The average number of requests Scrapy should be sending in parallel to
# each remote server
#AUTOTHROTTLE_TARGET_CONCURRENCY = 1.0
# Enable showing throttling stats for every response received:
#AUTOTHROTTLE_DEBUG = False

# Enable and configure HTTP caching (disabled by default)
# See https://docs.scrapy.org/en/latest/topics/downloader-middleware.html#httpcache-middleware-settings
#HTTPCACHE_ENABLED = True
#HTTPCACHE_EXPIRATION_SECS = 0
#HTTPCACHE_DIR = 'httpcache'
#HTTPCACHE_IGNORE_HTTP_CODES = []
#HTTPCACHE_STORAGE = 'scrapy.extensions.httpcache.FilesystemCacheStorage'

MONGO_HOST = "127.0.0.1"
MONGO_PORT = 27017
DB_NAME = "SinaNews"
COLLECTION_NAME = "news"
```

6. 监控脚本，monitor_news.py

```python
# -*- coding: utf-8 -*-
import sys, time
import pymongo
import scrapy 
from sinanews.settings import MONGO_HOST, MONGO_PORT, DB_NAME, COLLECTION_NAME

if __name__ == "__main__":
    news_num = int(sys.argv[1])
    time_str = time.strftime("%Y%m%d", time.localtime())

    # 实际的collection_name
    collection_name = COLLECTION_NAME + "_" + time_str
    
    # 链接数据库
    client = pymongo.MongoClient(MONGO_HOST, MONGO_PORT)
    db = client[DB_NAME]
    collection = db[collection_name]

    # 查找当前集合中所有文档的数量
    cur_news_num = collection.count()

    print(cur_news_num)
    if cur_news_num < news_num:
        print("the news nums of {}_{} collection is less then {}".\
            format(COLLECTION_NAME, time_str, news_num))
```

7. 运行脚本，run_scrapy_sina.sh

```python
# -*- coding: utf-8 -*-
"""
新闻爬取及监控脚本
"""

# 设置python环境
python="/home/recsys/miniconda3/envs/news_rec_py3/bin/python"

# 新浪新闻网站爬取的页面数量
page="1"
min_news_num="1000" # 每天爬取的新闻数量少于500认为是异常

# 爬取数据
scrapy crawl sina_spider -a pages=${page}  
if [ $? -eq 0 ]; then
    echo "scrapy crawl sina_spider --pages ${page} success."
else   
    echo "scrapy crawl sina_spider --pages ${page} fail."
fi

# 检查今天爬取的数据是否少于min_news_num篇文章，这里也可以配置邮件报警
python monitor_news.py ${min_news_num}
if [ $? -eq 0 ]; then
    echo "run python monitor_news.py success."
else   
    echo "run python monitor_news.py fail."
fi
```

8. 运行项目命令

```
sh run_scrapy_sina.sh
```

最终查看数据库中的数据：

<img src="http://ryluo.oss-cn-chengdu.aliyuncs.com/图片image-20211103214611171.png" alt="image-20211103214611171" style="zoom:80%;" />

## 物料侧画像的构建

**新物料来源**

首先要说的就是新物料的来源，物料是通过每天在新闻网站上爬取获取的，爬取新闻详细的内容在[2.2.1.4 Scrapy基础及新闻爬取实战](https://github.com/datawhalechina/fun-rec/blob/master/docs/第二章 推荐系统实战/2.2新闻推荐系统实战/docs/2.2.1.4 scrapy基础及新闻爬取实战.md)中已经详细的聊过了，这里要说明的一点就是，新闻爬取是每天凌晨的时候爬取前一天的新闻，这么做的原因是可以爬到更多的物料，缺点就是物料的时效性会延迟一天，新爬取的物料存储在MongoDB中。

**物料画像的更新**

物料画像的更新主要有一下几个方面：

1. 新物料画像添加到物料库中
2. 旧物料画像，通过用户的交互记录进行更新

首先说一下新物料添加到物料库的逻辑是什么，新物料添加到物料库这件事情肯定是发生在新闻爬取之后的，然后要将新物料添加到物料库还需要对新物料做一些简单的画像处理，目前我们定义的画像字段如下（处理后的画像存储在Mongodb）：

<img src="http://ryluo.oss-cn-chengdu.aliyuncs.com/图片image-20211203150212960.png" alt="image-20211203150212960" style="zoom: 80%;" />

具体的逻辑就是遍历今天爬取的所有文章，然后通过文章的title来判断这篇文章是否已经在物料库中(新闻网站有可能有些相同的文章会出现在多天)来去重。然后再根据我们定义的一些字段，给画像相应的字段初始化，最后就是存入画像物料池中。

关于旧物料画像的更新，这里就需要先了解一下旧物料哪些字段会被用户的行为更新。下面是新闻列表展示页，我们会发现前端会展示新闻的阅读、喜欢及收藏次数。而用户的交互（阅读、点赞和收藏）会改变这些值。

<img src="http://ryluo.oss-cn-chengdu.aliyuncs.com/图片image-20211203150835056.png" alt="image-20211203150835056" style="zoom:80%;" />

为了能够实时的在前端显示新闻的这些动态行为信息，我们提前将新闻的动态信息存储到了redis中，线上获取的时候是直接从redis中获取新闻的数据，并且如果用户对新闻产生了交互，那么这些动态信息就会被更新，我们也是直接更新redis中的值，这样做主要是为了能够让前端可以实时的获取的新闻最新的动态画像信息。

通过上面的内容我们了解到，新闻的动态画像的更新是在redis中进行的，而redis又是一个内存数据库，资源是非常宝贵的，我们不能一直将新闻的信息存储在里面，而是每天进行一次更新，只更新哪些今天可能会被用来展示的新闻（有些新闻可能从发表到现在太久了，由于新闻的时效性就没有必要再展示了）。所以为了能够保存新闻历史的动态信息，系统还需要每天将redis中的动态新闻信息更新到mongodb存储的新闻画像库中，这里的逻辑也是每天会定时触发，这里的逻辑会放在更新完新物料的画像之后，当然这里两个的先后顺序并没有什么影响，只需要注意更新物料动态画像的时候一定得再redis数据清空之前。

其实这里还有个逻辑需要说明一下，新闻的画像库其实是有两个的，一个被称为是特征库FeatureProtrail, 它存储了物料的所有字段。还有一个是存储前端展示内容的画像库RedisProtrail, 这个画像库中的物料是一样的多，只不过每个物料存储的内容不一样，这个特征库的内容每天都会被更新，作为存储再redis中的新闻内容的备份内容。所以在完成了新、旧物料画像的更新之后，我们需要将最新的物料库中的新闻信息往RedisProtrail物料库中写一份，并去掉一些前端展示不需要的字段内容。

关于物料画像的更新的核心代码：

```python
# -*- coding: utf-8 -*-
from re import S
import sys
import json
sys.path.append("../")
from material_process.utils import get_key_words
from dao.mongo_server import MongoServer
from dao.redis_server import RedisServer

"""
新闻画像中包含的字段：
0. news_id 新闻的id
1. title 标题
2. raw_key_words (爬下来的关键词，可能有缺失)
3. manual_key_words (根据内容生成的关键词)
4. ctime 时间
5. content 新闻具体内容
6. cate 新闻类别
7. likes 新闻点赞数量
8. collections 新闻收藏数量
9. read_nums 阅读次数
10. url 新闻原始链接
"""

class NewsProtraitServer:
    def __init__(self):
        """初始化相关参数
        """
        self.mongo_server = MongoServer()   
        self.sina_collection = self.mongo_server.get_sina_news_collection()
        self.material_collection = self.mongo_server.get_feature_protrail_collection()
        self.redis_mongo_collection = self.mongo_server.get_redis_mongo_collection()
        self.news_dynamic_feature_redis = RedisServer().get_dynamic_news_info_redis()

    def _find_by_title(self, collection, title):
        """从数据库中查找是否有相同标题的新闻数据
        数据库存在当前标题的数据返回True, 反之返回Flase
        """
        # find方法，返回的是一个迭代器
        find_res = collection.find({"title": title})
        if len(list(find_res)) != 0:
            return True
        return False

    def _generate_feature_protrail_item(self, item):
        """生成特征画像数据，返回一个新的字典
        """
        news_item = dict()
        news_item['news_id'] = item['news_id']
        news_item['title'] = item['title']
        # 从新闻内容中提取的关键词没有原始新闻爬取时的关键词准确，所以手动提取的关键词
        # 只是作为一个补充，当原始新闻中没有提供关键词的时候可以使用
        news_item['raw_key_words'] = item['raw_key_words']
        key_words_list = get_key_words(item['content'])
        news_item['manual_key_words'] = ",".join(key_words_list)
        news_item['ctime'] = item['ctime']
        news_item['content'] = item['content']
        news_item['cate'] = item['cate']
        news_item['url'] = item['url']
        news_item['likes'] = 0
        news_item['collections'] = 0
        news_item['read_num'] = 0
        news_item['hot_value'] = 1000 # 初始化一个比较大的热度值，会随着时间进行衰减
        
        return news_item

    def update_new_items(self):
        """将今天爬取的数据构造画像存入画像数据库中
        """
        # 遍历今天爬取的所有数据
        for item in self.sina_collection.find():
            # 根据标题进行去重
            if self._find_by_title(self.material_collection, item["title"]):
                continue
            news_item = self._generate_feature_protrail_item(item)
            # 插入物料池
            self.material_collection.insert_one(news_item)
        
        print("run update_new_items success.")

    def update_redis_mongo_protrail_data(self):
        """每天都需要将新闻详情更新到redis中，并且将前一天的redis数据删掉
        """
        # 每天先删除前一天的redis展示数据，然后再重新写入
        self.redis_mongo_collection.drop()
        print("delete RedisProtrail ...")
        # 遍历特征库
        for item in self.material_collection.find():
            news_item = dict()
            news_item['news_id'] = item['news_id']
            news_item['title'] = item['title']
            news_item['ctime'] = item['ctime']
            news_item['content'] = item['content']
            news_item['cate'] = item['cate']
            news_item['url'] = item['url']
            news_item['likes'] = 0
            news_item['collections'] = 0
            news_item['read_num'] = 0

            self.redis_mongo_collection.insert_one(news_item)
        print("run update_redis_mongo_protrail_data success.")

    def update_dynamic_feature_protrail(self):
        """用redis的动态画像更新mongodb的画像
        """
        # 遍历redis的动态画像，将mongodb中对应的动态画像更新        
        news_list = self.news_dynamic_feature_redis.keys()
        for news_key in news_list:
            news_dynamic_info_str = self.news_dynamic_feature_redis.get(news_key)
            news_dynamic_info_str = news_dynamic_info_str.replace("'", '"' ) # 将单引号都替换成双引号
            news_dynamic_info_dict = json.loads(news_dynamic_info_str)
            
            # 查询mongodb中对应的数据，并将对应的画像进行修改
            news_id = news_key.split(":")[1]
            mongo_info = self.material_collection.find_one({"news_id": news_id})
            new_mongo_info = mongo_info.copy()
            new_mongo_info['likes'] = news_dynamic_info_dict["likes"]
            new_mongo_info['collections'] = news_dynamic_info_dict["collections"]
            new_mongo_info['read_num'] = news_dynamic_info_dict["read_num"]

            self.material_collection.replace_one(mongo_info, new_mongo_info, upsert=True) # upsert为True的话，没有就插入
        print("update_dynamic_feature_protrail success.")


# 系统最终执行的不是这个脚本，下面的代码是用来测试的
if __name__ == "__main__":
    news_protrait = NewsProtraitServer()
    # 新物料画像的更新
    news_protrait.update_new_items()
    # 更新动态特征
    news_protrait.update_dynamic_feature_protrail()
    # redis展示新闻内容的备份
    news_protrait.update_redis_mongo_protrail_data()
```



上面的内容说完了物料的更新，接下来介绍一下对于更新完的物料是如何添加到redis数据库中去的。关于新闻内容在redis中的存储，我们将新闻的信息拆成了两部分，一部分是新闻不会发生变化的属性（例如，创建时间、标题、新闻内容等），还有一部分是物料的动态属性，在redis中存储的key的标识分别为：static_news_detail:news_id和dynamic_news_detail:news_id 下面是redis中存储的真实内容

<img src="http://ryluo.oss-cn-chengdu.aliyuncs.com/图片image-20211203153841222.png" alt="image-20211203153841222" style="zoom:67%;" />

<img src="http://ryluo.oss-cn-chengdu.aliyuncs.com/图片image-20211203153958220.png" alt="image-20211203153958220" style="zoom:70%;" />

这么做的目的是为了线上实时更改物料动态信息的时候更加高效一点。当需要获取某篇新闻的详细信息的时候需要查这两份数据并将数据这两部分数据拼起来最终才发送给前端展示。这部分的代码逻辑如下：

```python
import sys
sys.path.append("../../")
from dao.mongo_server import MongoServer
from dao.redis_server import RedisServer


class NewsRedisServer(object):
    def __init__(self):
        self.rec_list_redis = RedisServer().get_reclist_redis()
        self.static_news_info_redis = RedisServer().get_static_news_info_redis()
        self.dynamic_news_info_redis = RedisServer().get_dynamic_news_info_redis()

        self.redis_mongo_collection = MongoServer().get_redis_mongo_collection()

        # 删除前一天redis中的内容
        self._flush_redis_db()

    def _flush_redis_db(self):
        """每天都需要删除redis中的内容，更新当天新的内容上去
        """
        try:
            self.rec_list_redis.flushall()
        except Exception:
            print("flush redis fail ... ")

    def _get_news_id_list(self):
        """获取物料库中所有的新闻id
        """
        # 获取所有数据的news_id,
        # 暴力获取，直接遍历整个数据库，得到所有新闻的id
        # TODO 应该存在优化方法可以通过查询的方式只返回new_id字段
        news_id_list = []
        for item in self.redis_mongo_collection.find():
            news_id_list.append(item["news_id"])
        return news_id_list

    def _set_info_to_redis(self, redisdb, content):
        """将content添加到指定redis
        """
        try: 
            redisdb.set(*content)
        except Exception:
            print("set content fail".format(content))

    def news_detail_to_redis(self):
        """将需要展示的画像内容存储到redis
        静态不变的特征存到static_news_info_db_num
        动态会发生改变的特征存到dynamic_news_info_db_num
        """ 
        news_id_list = self._get_news_id_list()

        for news_id in news_id_list:
            news_item_dict = self.redis_mongo_collection.find_one({"news_id": news_id}) # 返回的是一个列表里面套了一个字典  
            news_item_dict.pop("_id")

            # 分离动态属性和静态属性
            static_news_info_dict = dict()
            static_news_info_dict['news_id'] = news_item_dict['news_id']
            static_news_info_dict['title'] = news_item_dict['title']
            static_news_info_dict['ctime'] = news_item_dict['ctime']
            static_news_info_dict['content'] = news_item_dict['content']
            static_news_info_dict['cate'] = news_item_dict['cate']
            static_news_info_dict['url'] = news_item_dict['url']
            static_content_tuple = "static_news_detail:" + str(news_id), str(static_news_info_dict)
            self._set_info_to_redis(self.static_news_info_redis, static_content_tuple)

            dynamic_news_info_dict = dict()
            dynamic_news_info_dict['likes'] = news_item_dict['likes']
            dynamic_news_info_dict['collections'] = news_item_dict['collections']
            dynamic_news_info_dict['read_num'] = news_item_dict['read_num']
            dynamic_content_tuple = "dynamic_news_detail:" + str(news_id), str(dynamic_news_info_dict)
            self._set_info_to_redis(self.dynamic_news_info_redis, dynamic_content_tuple)

        print("news detail info are saved in redis db.")


if __name__ == "__main__":
    # 每次创建这个对象的时候都会把数据库中之前的内容删除
    news_redis_server = NewsRedisServer()
    # 将最新的前端展示的画像传到redis
    news_redis_server.news_detail_to_redis()
```



**到此位置，离线物料画像的更新逻辑就介绍完了，最后把上面的逻辑用代码全部串起来的话就如下代码：**下面的代码是会在每天定时运行的，这样就将物料侧的画像构建逻辑穿起来了

```python
from material_process.news_protrait import NewsProtraitServer
from material_process.news_to_redis import NewsRedisServer

def process_material():
    """物料处理函数
    """
    # 画像处理
    protrail_server = NewsProtraitServer()
    # 处理最新爬取新闻的画像，存入特征库
    protrail_server.update_new_items()
    # 更新新闻动态画像, 需要在redis数据库内容清空之前执行
    protrail_server.update_dynamic_feature_protrail()
    # 生成前端展示的新闻画像，并在mongodb中备份一份
    protrail_server.update_redis_mongo_protrail_data()

    # 新闻数据写入redis, 注意这里处理redis数据的时候是会将前一天的数据全部清空
    news_redis_server = NewsRedisServer()
    # 将最新的前端展示的画像传到redis
    news_redis_server.news_detail_to_redis()


if __name__ == "__main__":
    process_material() 
```



## 用户侧画像的构建

对于用户画像的更新来说主要分为两方面：

1. 新注册用户画像的更新
2. 老用户画像的更新

由于我们系统中将所有注册过的用户都放到了一个表里面（新、老用户），所以每次更新画像的话只需要遍历一遍注册表中的所有用户。再说具体的画像构建逻辑之前，得先了解一下用户画像中包含哪些字段，下面是直接从mongo中查出来的

<img src="http://ryluo.oss-cn-chengdu.aliyuncs.com/图片image-20211203163848668.png" alt="image-20211203163848668" style="zoom:80%;" />

从上面可以看出，主要是用户的基本信息和用户历史信息相关的一些标签，对于用户的基本属性特征这个可以直接从注册表中获取，那么对于跟用户历史阅读相关的信息，需要统计用户历史的所有阅读、喜欢和收藏的新闻详细信息。为了得到跟用户历史兴趣相关的信息，我们需要对用户的历史阅读、喜欢和收藏这几个历史记录给存起来，其实这些信息都可以从日志信息中获取得到，但是这里有个工程上的事情得先说明一下，先看下面这个图，对于每个用户点进一篇新闻的详情页

<img src="http://ryluo.oss-cn-chengdu.aliyuncs.com/图片image-20211203164332062.png" alt="image-20211203164332062" style="zoom: 80%;" />

最底部有个喜欢和收藏，这个前端展示的结果是从后端获取的数据，那就意味着后端需要维护一个用户历史点击及收藏过的文章列表，这里我们使用了mysql来存储，主要是怕redis不够用。其实这两个表不仅仅可以用来前端展示用的，还可以用来分析用户的画像，这都给我们整理好了用户历史喜欢和收藏了。

此外前面也提到了我们可以使用用户历史阅读的文章做用户画像，为了更好处理和理解，我们也维护了一份用户历史阅读过的所有文章的mysql表（维护表的核心逻辑就是每天跑一边用户日志，更新一下用户历史阅读的记录），那么此时我们其实已经有了用户的阅读、点赞和收藏三个用户行为表了，接下来就直接可以通过这三个表来做具体的用户兴趣相关的画像了，实现的具体逻辑如下：

```python
import sys
import datetime
from collections import Counter, defaultdict

from sqlalchemy.sql.expression import table
sys.path.append("../../")
from dao.mongo_server import MongoServer
from dao.mysql_server import MysqlServer
from dao.entity.register_user import RegisterUser
from dao.entity.user_read import UserRead
from dao.entity.user_likes import UserLikes
from dao.entity.user_collections import UserCollections


class UserProtrail(object):
    def __init__(self):
        self.user_protrail_collection = MongoServer().get_user_protrail_collection()
        self.material_collection = MongoServer().get_feature_protrail_collection()
        self.register_user_sess = MysqlServer().get_register_user_session()
        self.user_collection_sess = MysqlServer().get_user_collection_session()
        self.user_like_sess = MysqlServer().get_user_like_session()
        self.user_read_sess = MysqlServer().get_user_read_session()

    def _user_info_to_dict(self, user):
        """将mysql查询出来的结果转换成字典存储
        """
        info_dict = dict()
        
        # 基本属性特征
        info_dict["userid"] = user.userid
        info_dict["username"] = user.username
        info_dict["passwd"] = user.passwd
        info_dict["gender"] = user.gender
        info_dict["age"] = user.age
        info_dict["city"] = user.city

        # 兴趣爱好 
        behaviors=["like","collection"]
        time_range = 15
        _, feature_dict = self.get_statistical_feature_from_history_behavior(user.userid,time_range,behavior_types=behaviors)
        for type in feature_dict.keys():
            if feature_dict[type]:
                info_dict["{}_{}_intr_cate".format(type,time_range)] = feature_dict[type]["intr_cate"]  # 历史喜欢最多的Top3的新闻类别
                info_dict["{}_{}_intr_key_words".format(type,time_range)] = feature_dict[type]["intr_key_words"] # 历史喜欢新闻的Top3的关键词
                info_dict["{}_{}_avg_hot_value".format(type,time_range)] = feature_dict[type]["avg_hot_value"] # 用户喜欢新闻的平均热度
                info_dict["{}_{}_news_num".format(type,time_range)] = feature_dict[type]["news_num"] # 用户15天内喜欢的新闻数量
            else:
                info_dict["{}_{}_intr_cate".format(type,time_range)] = ""  # 历史喜欢最多的Top3的新闻类别
                info_dict["{}_{}_intr_key_words".format(type,time_range)] = "" # 历史喜欢新闻的Top3的关键词
                info_dict["{}_{}_avg_hot_value".format(type,time_range)] = 0 # 用户喜欢新闻的平均热度
                info_dict["{}_{}_news_num".format(type,time_range)] = 0 # 用户15天内喜欢的新闻数量

        return info_dict

    def update_user_protrail_from_register_table(self):
        """每天都需要将当天注册的用户添加到用户画像池中
        """
        # 遍历注册用户表
        for user in self.register_user_sess.query(RegisterUser).all():
            user_info_dict = self._user_info_to_dict(user)
            old_user_protrail_dict = self.user_protrail_collection.find_one({"username": user.username})
            if old_user_protrail_dict is None:
                self.user_protrail_collection.insert_one(user_info_dict)
            else:
                # 使用参数upsert设置为true对于没有的会创建一个
                # replace_one 如果遇到相同的_id 就会更新
                self.user_protrail_collection.replace_one(old_user_protrail_dict, user_info_dict, upsert=True)
            

    def get_statistical_feature_from_history_behavior(self, user_id, time_range, behavior_types):
        """获取用户历史行为的统计特征 ["read","like","collection"] """
        fail_type = []
        sess, table_obj, history = None, None, None
        feature_dict = defaultdict(dict)

        end = datetime.datetime.now().strftime("%Y-%m-%d %H:%M:%S")
        start = (datetime.datetime.now()+datetime.timedelta(days=-time_range)).strftime("%Y-%m-%d %H:%M:%S")

        for type in behavior_types:
            if type == "read":
                sess = getattr(self,"user_{}_sess".format(type))
                table_obj = UserRead
            elif type == "like":
                sess = getattr(self,"user_{}_sess".format(type))
                table_obj = UserLikes
            elif type == "collection":
                sess = getattr(self,"user_{}_sess".format(type))
                table_obj = UserCollections
            try:
                history = sess.query(table_obj).filter(table_obj.userid==user_id).filter(table_obj.curtime>=start).filter(table_obj.curtime<=end).all()
            except Exception as e:
                print(str(e))
                fail_type.append(type)
                continue
            
            feature_dict[type] = self._gen_statistical_feature(history)
            
        return fail_type, feature_dict
          
    def _gen_statistical_feature(self,history):
        """"""
        # 为history 获取特征
        if not len(history): return None
        history_new_id = []
        history_hot_value = []
        history_new_cate = []
        history_key_word = []
        for h in history:
            news_id = h.newid 
            newsquery = {"news_id":news_id}
            result = self.material_collection.find_one(newsquery)
            history_new_id.append(result["news_id"])
            history_hot_value.append(result["hot_value"])
            history_new_cate.append(result["cate"])
            history_key_word += result["manual_key_words"].split(",")
        
        feature_dict = dict()
        # 计算平均热度
        feature_dict["avg_hot_value"] = 0 if sum(history_hot_value) < 0.001 else sum(history_hot_value) / len(history_hot_value)

        # 计算Top3的类别
        cate_dict = Counter(history_new_cate)
        cate_list= sorted(cate_dict.items(),key = lambda d: d[1], reverse=True)
        cate_str = ",".join([item[0] for item in cate_list[:3]] if len(cate_list)>=3 else [item[0] for item in cate_list] )
        feature_dict["intr_cate"] = cate_str

        # 计算Top3的关键词
        word_dict = Counter(history_key_word)
        word_list= sorted(word_dict.items(),key = lambda d: d[1], reverse=True)
        # TODO 关键字属于长尾 如果关键字的次数都是一次 该怎么去前3
        word_str = ",".join([item[0] for item in word_list[:3]] if len(cate_list)>=3 else [item[0] for item in word_list] )
        feature_dict["intr_key_words"] = word_str
        # 新闻数目
        feature_dict["news_num"] = len(history_new_id)

        return feature_dict


if __name__ == "__main__":
    user_protrail = UserProtrail().update_user_protrail_from_register_table()

```



到此位置用户画像的基本逻辑就介绍完了，下面是用户画像更新的总体逻辑代码：

```python
from user_process.user_to_mysql import UserMysqlServer
from user_process.user_protrail import UserProtrail

"""
1. 将用户的曝光数据从redis落到mysql中。
2. 更新用户画像
"""
    
def process_users():
    """将用户数据落 Mysql
    """
    # 用户mysql存储
    user_mysql_server = UserMysqlServer()
    # 用户曝光数据落mysql
    user_mysql_server.user_exposure_to_mysql()

    # 更新用户画像
    user_protrail = UserProtrail()
    user_protrail.update_user_protrail_from_register_table()


if __name__ == "__main__":
    process_users() 
```



## 画像自动化构建

上面分别对用户侧和物料侧的画像构建进行了介绍，接下来就是要将上面所有的过程都自动化运行，并且设置好定时任务，其实最核心的一点就是一定要在清除redis数据之前，完成用户和物料画像的构建，下面是构建整个自动化的流程。



**物料更新脚本：process_material.py**

```python
from material_process.news_protrait import NewsProtraitServer
from material_process.news_to_redis import NewsRedisServer


def process_material():
    """物料处理函数
    """
    # 画像处理
    protrail_server = NewsProtraitServer()
    # 处理最新爬取新闻的画像，存入特征库
    protrail_server.update_new_items()
    # 更新新闻动态画像, 需要在redis数据库内容清空之前执行
    protrail_server.update_dynamic_feature_protrail()
    # 生成前端展示的新闻画像，并在mongodb中备份一份
    protrail_server.update_redis_mongo_protrail_data()


if __name__ == "__main__":
    process_material() 
```



**用户画像更新脚本: process_user.py**

```python
from user_process.user_to_mysql import UserMysqlServer
from user_process.user_protrail import UserProtrail

"""
1. 将用户的曝光数据从redis落到mysql中。
2. 更新用户画像
"""

    
def process_users():
    """将用户数据落 Mysql
    """
    # 用户mysql存储
    user_mysql_server = UserMysqlServer()
    # 用户曝光数据落mysql
    user_mysql_server.user_exposure_to_mysql()

    # 更新用户画像
    user_protrail = UserProtrail()
    user_protrail.update_user_protrail_from_register_table()


if __name__ == "__main__":
    process_users() 
```



**redis数据更新脚本：update_redis.py**

```python
from material_process.news_protrait import NewsProtraitServer
from material_process.news_to_redis import NewsRedisServer


def update():
    """物料处理函数
    """
    # 新闻数据写入redis, 注意这里处理redis数据的时候是会将前一天的数据全部清空
    news_redis_server = NewsRedisServer()
    # 将最新的前端展示的画像传到redis
    news_redis_server.news_detail_to_redis()


if __name__ == "__main__":
    update() 
```



**最后将上面三个脚本穿起来的shell脚本offline_material_and_user_process.sh：**

```shell
#!/bin/bash

python=/home/recsys/miniconda3/envs/news_rec_py3/bin/python
news_recsys_path="/home/recsys/news_rec_server"

echo "$(date -d today +%Y-%m-%d-%H-%M-%S)"

# 为了更方便的处理路径的问题，可以直接cd到我们想要运行的目录下面
cd ${news_recsys_path}/materials

# 更新物料画像
${python} process_material.py
if [ $? -eq 0 ]; then
    echo "process_material success."
else   
    echo "process_material fail."
fi 

# 更新用户画像
${python} process_user.py
if [ $? -eq 0 ]; then
    echo "process_user.py success."
else   
    echo "process_user.py fail."
fi

# 清除前一天redis中的数据，更新最新今天最新的数据
${python} update_redis.py
if [ $? -eq 0 ]; then
    echo "update_redis success."
else   
    echo "update_redis fail."
fi


echo " "
```



**crontab定时任务：**

![image-20211203172613512](http://ryluo.oss-cn-chengdu.aliyuncs.com/图片image-20211203172613512.png)

将定时任务拆解一下：

```vim
0 0 * * * /home/recsys/news_rec_server/scheduler/crawl_news.sh >> /home/recsys/news_rec_server/logs/offline_material_process.log && 

/home/recsys/news_rec_server/scheduler/offline_material_and_user_process.sh >> /home/recsys/news_rec_server/logs/material_and_user_process.log && 

/home/recsys/news_rec_server/scheduler/run_offline.sh >> /home/recsys/news_rec_server/logs/offline_rec_list_to_redis.log
```

前面的crontab语法表示的是每天0点运行下面这一串脚本，上面命令中的 && 表示的是先运行完符号前面的内容再运行后面的命令，所以这里的&&是为了将上面三个任务串联起来，大致的执行逻辑就是：

1. 先爬取新闻数据，这里需要注意的是，虽然是今天零点爬数据，但是实际上爬的是前一天的新闻
2. 数据爬完之后，离线更新用户画像，物料画像及线上要存储再redis中的画像
3. 最后其实是离线推荐的流程，离线将用户的排序列表存到redis中，线上直接取就行了