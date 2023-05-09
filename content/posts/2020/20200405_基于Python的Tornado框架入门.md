---
title: 基于Python的Tornado框架入门记录
date: 2020-04-05
tags:
 - 框架
categories:
 - 2020
 - 技术
---


## Tornado简介
Tornado-基于Python的web服务端框架， 与现有主流的web服务端（以及大多数Python框架）有着明显的区别： 是非阻塞式，速度相当快。得利于其非阻塞式的方式，Tornado每秒可以处理数以千计的连接，因此是实时web服务的一个理想框架。

## Tornado安装
`pip install tornado`

## Tornado web程序流程
1. 创建web应用实例对象，第一个初始化参数为路由映射列表
2. 定义实现路由映射列表的handler类
3. 创建实例，监听服务器端口
4. 启动。执行IOLoop类的start()方法

## 处理HTTP请求
```
RequestHandler.get(args,kwargs)      #处理get请求
RequestHandler.post(*args, kwargs)   #处理post请求
RequestHandler.put(args, kwargs)     #处理put请求
RequestHandler.delete(*args, kwargs) #处理delete请求
```

## Hello world
``` py
import tornado.ioloop
import tornado.web

# 定义实现路由映射列表的handler类
class MainHandler(tornado.web.RequestHandler):
    def get(self):
        self.write("Hello world")

class Application(tornado.web.Application):
    def __init__(self):
        handlers = [
            (r'/', MainHandler),
        ]
        # 创建web应用实例对象，第一个初始化参数为路由映射列表
        tornado.web.Application.__init__(self, handlers)

if __name__=="__main__":
    app = Application()
    # 创建实例，监听服务器端口
    app.listen(8000)
    print("Tornado Started in port 8000，http://127.0.0.1:8000")
    # 启动。执行IOLoop类的start()方法
    tornado.ioloop.IOLoop.current().start()
```

Hello world创建了一个socket服务并监听8000端口，当接收到请求时根据路由规则来找到相应的类处理该请求，并根据请求方式指定相应类中的指定方法处理。 所以当我们在浏览器键入127.0.0.1:8000，服务端会给浏览器返回Hello world。
