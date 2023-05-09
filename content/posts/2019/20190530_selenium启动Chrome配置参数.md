---
title: selenium启动Chrome配置参数
date: 2019-05-30
tags:
 - python
 - 工具
categories:
 - 2019
 - 技术
---

:::tip
selenium配置
:::
<!-- more -->

> 每次当selenium启动chrome浏览器的时候，chrome浏览器很干净，没有插件、没有收藏、没有历史记录，这是因为selenium在启动chrome时为了保证最快的运行效率，启动了一个裸浏览器，这就是为什么需要配置参数的原因，但是有些时候我们需要的不仅是一个裸浏览器。

selenium启动配置参数接收是ChromeOptions类，创建方式如下：
``` python
from selenium import webdriver
option = webdriver.ChromeOptions()
```

创建了ChromeOptions类之后就是添加参数，添加参数有几个特定的方法，分别对应添加不同类型的配置项目。

设置 chrome 二进制文件位置 (binary_location)
``` python
from selenium import webdriver
option = webdriver.ChromeOptions()

# 添加启动参数
option.add_argument()

# 添加扩展应用 
option.add_extension()
option.add_encoded_extension()

# 添加实验性质的设置参数 
option.add_experimental_option()

# 设置调试器地址
option.debugger_address()
```

## 常用配置参数
``` python
from selenium import webdriver
option = webdriver.ChromeOptions()

# 添加UA
options.add_argument('user-agent="MQQBrowser/26 Mozilla/5.0 (Linux; U; Android 2.3.7; zh-cn; MB200 Build/GRJ22; CyanogenMod-7) AppleWebKit/533.1 (KHTML, like Gecko) Version/4.0 Mobile Safari/533.1"')

# 指定浏览器分辨率
options.add_argument('window-size=1920x3000') 

# 谷歌文档提到需要加上这个属性来规避bug
chrome_options.add_argument('--disable-gpu') 

 # 隐藏滚动条, 应对一些特殊页面
options.add_argument('--hide-scrollbars')

# 不加载图片, 提升速度
options.add_argument('blink-settings=imagesEnabled=false') 

# 浏览器不提供可视化页面. linux下如果系统不支持可视化不加这条会启动失败
options.add_argument('--headless') 

# 以最高权限运行
options.add_argument('--no-sandbox')

# 手动指定使用的浏览器位置
options.binary_location = r"C:\Program Files (x86)\Google\Chrome\Application\chrome.exe" 

#添加crx插件
option.add_extension('d:\crx\AdBlock_v2.17.crx') 

# 禁用JavaScript
option.add_argument("--disable-javascript") 

# 设置开发者模式启动，该模式下webdriver属性为正常值
options.add_experimental_option('excludeSwitches', ['enable-automation']) 

# 禁用浏览器弹窗
prefs = {  
    'profile.default_content_setting_values' :  {  
        'notifications' : 2  
     }  
}  
options.add_experimental_option('prefs',prefs)


driver=webdriver.Chrome(chrome_options=chrome_options)
```

## 浏览器地址栏参数
在浏览器地址栏输入下列命令得到相应的信息
``` python
about:version - 显示当前版本

　　about:memory - 显示本机浏览器内存使用状况

　　about:plugins - 显示已安装插件

　　about:histograms - 显示历史记录

　　about:dns - 显示DNS状态

　　about:cache - 显示缓存页面

　　about:gpu -是否有硬件加速

　　chrome://extensions/ - 查看已经安装的扩展
```

## 其他配置项目参数
``` python
–user-data-dir=”[PATH]” 
# 指定用户文件夹User Data路径，可以把书签这样的用户数据保存在系统分区以外的分区

　　–disk-cache-dir=”[PATH]“ 
# 指定缓存Cache路径

　　–disk-cache-size= 
# 指定Cache大小，单位Byte

　　–first run 
# 重置到初始状态，第一次运行

　　–incognito 
# 隐身模式启动

　　–disable-javascript 
# 禁用Javascript

　　--omnibox-popup-count="num" 
# 将地址栏弹出的提示菜单数量改为num个

　　--user-agent="xxxxxxxx" 
# 修改HTTP请求头部的Agent字符串，可以通过about:version页面查看修改效果

　　--disable-plugins 
# 禁止加载所有插件，可以增加速度。可以通过about:plugins页面查看效果

　　--disable-javascript 
# 禁用JavaScript，如果觉得速度慢在加上这个

　　--disable-java 
# 禁用java

　　--start-maximized 
# 启动就最大化

　　--no-sandbox 
# 取消沙盒模式

　　--single-process 
# 单进程运行

　　--process-per-tab 
# 每个标签使用单独进程

　　--process-per-site 
# 每个站点使用单独进程

　　--in-process-plugins 
# 插件不启用单独进程

　　--disable-popup-blocking 
# 禁用弹出拦截

　　--disable-plugins 
# 禁用插件

　　--disable-images 
# 禁用图像

　　--incognito 
# 启动进入隐身模式

　　--enable-udd-profiles 
# 启用账户切换菜单

　　--proxy-pac-url 
# 使用pac代理 [via 1/2]

　　--lang=zh-CN 
# 设置语言为简体中文

　　--disk-cache-dir 
# 自定义缓存目录

　　--disk-cache-size 
# 自定义缓存最大值（单位byte）

　　--media-cache-size 
# 自定义多媒体缓存最大值（单位byte）

　　--bookmark-menu 
# 在工具 栏增加一个书签按钮

　　--enable-sync 
# 启用书签同步
```
