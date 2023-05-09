---
title: macOS安装Homebrew问题
date: 2020-04-01
tags:
 - 问题
 - 记录
categories:
 - 2020
 - 技术
---

Homebrew是一款Mac OS平台下的软件包管理工具，拥有安装、卸载、更新、查看、搜索等很多实用的功能

**问题：** macOS安装Homebrew时总是报错（Failed to connect to raw.githubusercontent.com port 443: Connection refused）

**原因：** GitHub的raw.githubusercontent.com域名解析被污染了。

**解决：** 通过修改hosts解决此问题。

## 解决
1. 在 [www.ipaddress.com](https://www.ipaddress.com/) 查询 raw.githubusercontent.com 的真实IP。
2. 修改hosts: `sudo vim /etc/hosts`
3. 添加如下内容: 199.232.28.133 raw.githubusercontent.com

## hosts 权限
方法1
1. 打开Finder，按快捷键组合 Shift+Command+G 查找文件，输入/etc/hosts,确认前往
2. 进入文件夹后，复制该文件到桌面，修改成功后保存，将原先的host文件替换掉即可

方法2 
1. 打开Finder，按快捷键组合 Shift+Command+G 查找文件，输入/private,确认前往后可看到 etc 文件夹，邮件选择'显示简介'，在底部打开‘共享和权限’
2. 将everyone的权限改为‘读与写’，保存后直接修改hosts文件，最后完成后将权限改回来




