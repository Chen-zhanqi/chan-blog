---
title: 模拟登录TB与Cookies序列化
date: 2020-04-15
tags:
 - 爬虫
categories:
 - 2020
 - 技术
---

:::tip
TB网页登录流程解析，使用Python代码实现登录后保存Cookie信息
:::
<!-- more -->

## 一、淘宝登录流程
![TB登录流程图](https://gitee.com/chen-zq/bgimages/raw/master/img/淘宝登录流程图.png)
> 淘宝ua参数：ua(User-Agent)故名用户代理，淘宝的ua参数加入了浏览器、ip、电脑、时间等信息，然后加密生成，在很多地方使用，不仅仅是登录

**从代码层面考虑将模拟登录淘宝分为以下四个步骤：**

1. 输入用户名后，浏览器会向淘宝（taobao.com）发起一个post的请求，判断是否出现滑块验证！

2. 用户输入密码后，浏览器向淘宝（taobao.com）又发起一个post请求，验证用户名密码是否正确，如果正确则返回一个token。

3. 浏览器拿着token去阿里巴巴（alibaba.com）交换st码！

4. 浏览器获取st码之后，拿着st码获取cookies，登录成功

## 二、模拟登录实现

### 1.判断是否需要滑块

> 举个例子：某台设备可能出现登录过大量的账号，这时候淘宝就可以从ua参数中获取设备号，然后对该设备进行限制！

![TB判断是否需要滑块](https://gitee.com/chen-zq/bgimages/raw/master/img/TB判断是否需要滑块.png)

### 2.验证用户名密码

这里一步也就是上面时序图图中的第5步：请求登录，这里会将用户名、ua参数、加密密码等参数post到淘宝（taobao.com）去验证。

![https://gitee.com/chen-zq/bgimages/raw/master/img/TB账号密码验证](https://gitee.com/chen-zq/bgimages/raw/master/img/TB账号密码验证.png)

验证账号密码获取st码申请地址

![TB验证密码获取st地址](https://gitee.com/chen-zq/bgimages/raw/master/img/TB验证密码获取st地址.png)

结果

![TBst码申请地址](https://gitee.com/chen-zq/bgimages/raw/master/img/TBst码申请地址.png)

可以看到申请st码链接后面带了一个token

### 3.申请st码

申请到了淘宝（taobao.com）的token，这一步就是用token来换取st码

为什么淘宝登录需要这么麻烦呢？直接在 taobao.com 登录不就可以吗？为什么要先在taobao验证用户名密码，通过之后再去 alibaba.com 换取st码登录呢？

任何公司的框架都是慢慢演变的结果，我想最开始的淘宝登录肯定没这么复杂。但是随着阿里巴巴的慢慢壮大，很多事业线都划分开来，但是这些事业线之间又有关联性，比如用户登录了淘宝账号之后天猫就不需要再登录了呢？（注意淘宝和天猫的顶级域名不同，所以不能共享cookis）为了解决这个问题，单点登录就出现了。

> 单点登录（Single Sign On），简称为 SSO，是目前比较流行的企业业务整合的解决方案之一。SSO的定义是在多个应用系统中，用户只需要登录一次就可以访问所有相互信任的应用系统。 ——百度百科

用户数据在淘宝这里，所以需要现在淘宝（taobao.com）验证用户名和密码，验证通过生成一个token，浏览器拿着token去和阿里巴巴（alibaba.com）申请单点登录码（st码），阿里巴巴收到请求验证token通过则返回st码，所以用token换st码的原因就在于单点登录！

![TB申请st码](https://gitee.com/chen-zq/bgimages/raw/master/img/TB申请st码.png)

### 4.使用st码登录

成功获取st码之后我们就可以来登录了，这一步是通过st码获取登录的cookies并保存

![TB获取cookie并保存](https://gitee.com/chen-zq/bgimages/raw/master/img/TB获取cookie并保存.png)

## 三 、验证cookie
每次运行加载本地cookies文件，判断是否过期。

![TB验证cookie是否过期](https://gitee.com/chen-zq/bgimages/raw/master/img/TB验证cookie是否过期.png)
