---
title: pyinstaller打包scrapy项目
date: 2021-04-10
tags:
 - python
 - pyinstaller
categories:
 - 2021
 - 技术
---



## 简介
pyinstaller -F xxx.py 打包后会生成xxx.spec文件，，spec文件其实就相当于一个打包的架构文件，里面写了要怎么打包，类似于docker中的DockerFile。在用pyinstaller -F xxx.py这种方式打包的时候，程序也是先生成spec文件，再次打包的时候可以直接pyinstaller test.spec就可以。


## 步骤
### 新建crawl.py
在scrapy.cfg文件同一层，新建一个py文件，起名为 crawl.py

```py
# -*- coding: utf-8 -*-
from scrapy.crawler import CrawlerProcess
from scrapy.utils.project import get_project_settings
 
# 这里是必须引入的
#import robotparser
 
import scrapy.spiderloader
import scrapy.statscollectors
import scrapy.logformatter
import scrapy.dupefilters
import scrapy.squeues
 
import scrapy.extensions.spiderstate
import scrapy.extensions.corestats
import scrapy.extensions.telnet
import scrapy.extensions.logstats
import scrapy.extensions.memusage
import scrapy.extensions.memdebug
import scrapy.extensions.feedexport
import scrapy.extensions.closespider
import scrapy.extensions.debug
import scrapy.extensions.httpcache
import scrapy.extensions.statsmailer
import scrapy.extensions.throttle
 
import scrapy.core.scheduler
import scrapy.core.engine
import scrapy.core.scraper
import scrapy.core.spidermw
import scrapy.core.downloader
 
import scrapy.downloadermiddlewares.stats
import scrapy.downloadermiddlewares.httpcache
import scrapy.downloadermiddlewares.cookies
import scrapy.downloadermiddlewares.useragent
import scrapy.downloadermiddlewares.httpproxy
import scrapy.downloadermiddlewares.ajaxcrawl
#import scrapy.downloadermiddlewares.chunked
import scrapy.downloadermiddlewares.decompression
import scrapy.downloadermiddlewares.defaultheaders
import scrapy.downloadermiddlewares.downloadtimeout
import scrapy.downloadermiddlewares.httpauth
import scrapy.downloadermiddlewares.httpcompression
import scrapy.downloadermiddlewares.redirect
import scrapy.downloadermiddlewares.retry
import scrapy.downloadermiddlewares.robotstxt
 
import scrapy.spidermiddlewares.depth
import scrapy.spidermiddlewares.httperror
import scrapy.spidermiddlewares.offsite
import scrapy.spidermiddlewares.referer
import scrapy.spidermiddlewares.urllength
 
import scrapy.pipelines
 
import scrapy.core.downloader.handlers.http
import scrapy.core.downloader.contextfactory
 
# 自己项目用到的包
import openpyxl
import json
import time
import os
 
process = CrawlerProcess(get_project_settings())
 
# 'spider_name'替换成自己的爬虫名
process.crawl('spider_name')
process.start() # the script will block here until the crawling is finished
```

### 打包crawl.py
```python
pyinstaller -F crawl.py
```
-F 表示打包成单独的 .exe 文件

其他参数在本页面搜索框输入 `程序打包`

### 运行crawl.exe
1.双击运行，报错会闪退
2.打开新终端，将crawl.exe 托进去运行，可看见报错信息

### 问题
1. 缺少文件
```shell
FileNotFoundError: [Errno 2] No such file or directory: 'C:\\Users\\ADMINI~1\\Ap
pData\\Local\\Temp\\_MEI77122\\scrapy\\VERSION'
```
少VERSION文件，这个VERSION文件安装的scrapy模块下的一个文件，在安装目录，比如scrapy文件都安装在这个目录下D:\Program Files\python374\Lib\site-packages\scrapy。

可以在spec文件中直接配置
```python
# -*- mode: python ; coding: utf-8 -*-
block_cipher = None
a = Analysis(['crawl.py'],
 pathex=['C:\\Users\\Administrator\\Desktop\\qiaofenqi'],
 binaries=[],
 datas=[('D:\\Program Files\\python374\\Lib\\site-packages\\scrapy\\mime.types','scrapy'),
	('D:\\Program Files\\python374\\Lib\\site-packages\\scrapy\\VERSION','scrapy'),
	('.','.' )],
 hiddenimports=[],
 hookspath=[],
 runtime_hooks=[],
 excludes=[],
 win_no_prefer_redirects=False,
 win_private_assemblies=False,
 cipher=block_cipher,
 noarchive=False)
pyz = PYZ(a.pure, a.zipped_data,
 cipher=block_cipher)
exe = EXE(pyz,
 a.scripts,
 a.binaries,
 a.zipfiles,
 a.datas,
 [],
 name='crawl',
 debug=False,
 bootloader_ignore_signals=False,
 strip=False,
 upx=True,
 upx_exclude=[],
 runtime_tmpdir=None,
 console=True )
```
主要改的地方就是datas参数，要加入打包的资源文件，mime.types放到scrapy文件夹下，VERSION放到scrapy文件夹下，(’.’,’.’ )把当前目录放到打包后的根路径下，就是把整个爬虫文件打包进去。

重新打包，打包之前把上次产生的build和dist文件夹删掉，因为这里直接用的(’.’,’.’ )把整个文件都加了打包文件里了（乐意的话可以只把py文件加入datas，但目录结构不能变），会把build和dist也打进去，造成exe文件很大。

**重新打包命令**
```python
pyinstaller crawl.spec
```

2. 把xxx.exe文件拷贝到其他地方运行报错
```python
KeyError: 'Spider not found: credit'
```
爬虫运行是要依赖scrapy.cfg这个配置文件，把原来爬虫文件夹里的scrapy.cfg复制一下，和exe文件放到同目录下就可以


