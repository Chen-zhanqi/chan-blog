---
title: Liunx Navicat premium 15 激活
date: 2021-05-06
tags:
 - 工具
 - 数据库
categories:
 - 2021
---

## 一、相关工具
1. navicat15-premium-cs.AppImage：Navicat 15 premium 官方简体中文试用版

2. navicat-patcher：补丁

3. navicat-keygen ：注册机

4.appimagetool-x86_64.AppImage：Linux 独立运行软件打包工具

[相关工具下载地址](https://pan.baidu.com/s/1RMpZg-6ku0REVGFFeKn1lw)

**提取码：** v587

## 二、系统环境配置
### 1.安装 capstone
```shell
sudo apt-get install libcapstone-dev
```

### 2.安装 keystone
```shell
sudo apt-get install cmake
git clone https://github.com/keystone-engine/keystone.git
cd keystone
mkdir build
cd build
../make-share.sh
sudo make install
sudo ldconfig
```

### 3.安装 rapidjson
```shell
sudo apt-get install rapidjson-dev
```

## 三、操作步骤
### 1.赋予执行权限
```shell
chmod +x appimagetool-x86_64.AppImage
chmod +x navicat-patcher
chmod +x navicat-keygen
```

### 2.解压官方软件
```shell
mkdir navicat15
mount -o loop navicat15-premium-cs.AppImage navicat15
cp -r navicat15 navicat15-patched
```

### 3.运行补丁 (生成RegPrivateKey.pem文件)
```shell
./navicat-patcher navicat15-patched
```

### 4.打包成独立运行软件
```shell
./appimagetool-x86_64.AppImage navicat15-patched navicat15-premium-cs-pathed.AppImage
```

### 5. 运行补丁后软件包
````shell
chmod +x navicat15-premium-cs-pathed.AppImage

./navicat15-premium-cs-pathed.AppImage
````

### 6.运行注册机 (打开新终端)
```shell
./navicat-keygen --text ./RegPrivateKey.pem
```
```shell
@chenshen:~/apps/navicat/Navicat15$ ./navicat-keygen --text ./RegPrivateKey.pem 
**********************************************************
*       Navicat Keygen (Linux) by @DoubleLabyrinth       *
*                   Version: 1.0                         *
**********************************************************

[*] Select Navicat product:
 0. DataModeler
 1. Premium
 2. MySQL
 3. PostgreSQL
 4. Oracle
 5. SQLServer
 6. SQLite
 7. MariaDB
 8. MongoDB
 9. ReportViewer

(Input index)> 1

[*] Select product language:
 0. English
 1. Simplified Chinese
 2. Traditional Chinese
 3. Japanese
 4. Polish
 5. Spanish
 6. French
 7. German
 8. Korean
 9. Russian
 10. Portuguese

(Input index)> 1

[*] Input major version number:
(range: 0 ~ 15, default: 12)> 15

[*] Serial number:
NAVB-B5EA-NXXV-EFVY

```

### 7.生成序列号 (断网操作)
注册页面填入生成的注册码，超时后选择手动激活，输入请求码，两次回车生成激活码，填入后即可激活成功
