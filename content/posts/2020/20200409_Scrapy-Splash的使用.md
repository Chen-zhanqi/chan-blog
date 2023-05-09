---
title: Scrapy-Splash的使用
date: 2020-04-09
tags:
 - 爬虫
 - 框架
 - scrapy
categories:
 - 2020
 - 技术
---


## scrapy-splash的介绍
Scrapy没有JS engine, 无法爬取JavaScript生成的动态网页，只能爬取静态网页，而在现代的网络世界中，大部分网页都会采用JavaScript来丰富网页的功能。

scrapy-splash模块主要使用了Splash. 所谓的Splash, 就是一个Javascript渲染服务。它是一个实现了HTTP API的轻量级浏览器，Splash是用Python实现的，同时使用Twisted和QT。Twisted（QT）用来让服务具有异步处理能力，以发挥webkit的并发能力。Splash的特点如下：

* 并行处理多个网页
* 得到HTML结果以及（或者）渲染成图片
* 关掉加载图片或使用 Adblock Plus规则使得渲染速度更快
* 使用JavaScript处理网页内容
* 使用Lua脚本
* 能在Splash-Jupyter Notebooks中开发Splash Lua scripts
* 能够获得具体的HAR格式的渲染信息

## scrapy-splash的安装
1. 安装scrapy-splash模块
``` py
pip3 install scrapy-splash
```

2. scrapy-splash使用的是Splash HTTP API， 所以需要一个splash instance，一般采用docker运行splash，所以需要安装docker。
**CentOS7**
```
sudo yum install docker
```

3. 开启docker服务，拉取splash镜像（pull the image）
```
sudo service docker start
sudo docker pull scrapinghub/splash
```

4. 开启容器（start the container）
```
sudo docker run -p 8050:8050 scrapinghub/splash
```
此时Splash以运行在本地服务器的端口8050(http)，在浏览器中输入'IP:8050'

## scrapy-splash的实例
一个简单的实例，利用百度查询手机号码信息。比如，在百度输入框中输入手机号码‘159’，然后查询，得到如下信息
![20200909192946](https://gitee.com/chen-zq/bgimages/raw/master/img/20200909192946.png?imgslim)
利用scrapy-splash模拟以上操作并获取手机号码信息
1. 创建scrapy项目phone
2. 配置settings.py文件，配置的内容如下
``` py
ROBOTSTXT_OBEY = False

SPIDER_MIDDLEWARES = {
    'scrapy_splash.SplashDeduplicateArgsMiddleware': 100,
}

DOWNLOADER_MIDDLEWARES = {
    'scrapy_splash.SplashCookiesMiddleware': 723,
    'scrapy_splash.SplashMiddleware': 725,
    'scrapy.downloadermiddlewares.httpcompression.HttpCompressionMiddleware': 810
}

SPLASH_URL = 'http://localhost:8050'

DUPEFILTER_CLASS = 'scrapy_splash.SplashAwareDupeFilter'
HTTPCACHE_STORAGE = 'scrapy_splash.SplashAwareFSCacheStorage'
```

具体的配置说明可以参考: [scrapy-splash](https://pypi.org/project/scrapy-splash/)

3. 创建爬虫文件phoneSpider.py, 代码如下
``` py
# -*- coding: utf-8 -*-
from scrapy import Spider, Request
from scrapy_splash import SplashRequest

# splash lua script
script = """
         function main(splash, args)
             assert(splash:go(args.url))
             assert(splash:wait(args.wait))
             js = string.format("document.querySelector('#kw').value=%s;document.querySelector('#su').click()", args.phone)
             splash:evaljs(js)
             assert(splash:wait(args.wait))
             return splash:html()
         end
         """

class phoneSpider(Spider):
    name = 'phone'
    allowed_domains = ['www.baidu.com']
    url = 'https://www.baidu.com'
    
    # start request
    def start_requests(self):
        yield SplashRequest(self.url, callback=self.parse, endpoint='execute', args={'lua_source': script, 'phone':'159*******', 'wait': 5})
   
    # parse the html content 
    def parse(self, response):
        info = response.css('div.op_mobilephone_r.c-gap-bottom-small').xpath('span/text()').extract()
        print('='*40)
        print(''.join(info))
        print('='*40)
```
4. 运行爬虫: `scrapy crawl phone`
![20200909193310](https://gitee.com/chen-zq/bgimages/raw/master/img/20200909193310.png?imgslim)
