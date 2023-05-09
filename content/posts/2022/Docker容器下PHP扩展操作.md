---
title: Docker容器下PHP扩展操作
date: 2022-06-20
tags:
- docker
categories:
- 2022
- 技术
---

## 常用操作
docker容器下的PHP有自己特有的安装扩展方法：
进入php容器$：```docker exec -it php /bin/bash```
```docker-php-source```：              //在容器中创建/usr/src/php文件夹
```docker-php-ext-install```：        //安装并启动扩展（常用）
```docker-php-ext-enable```：       //启动PHP扩展
```docker-php-ext-configure```： //添加扩展自定义配置，和enable搭配使用

## zip
安装zip扩展所需的依赖扩展
```
apt-get update && apt-get install -y zlib1g-dev && apt-get install -y libzip-dev
```

安装并启动zip扩展
```
docker-php-ext-install zip
```

## pdo_mysql
```
docker-php-ext-install pdo pdo_mysql
```

如果安装失败出现下面情况 /usr/local/bin/docker-php-ext-enable: cannot create /usr/local/etc/php/conf.d/docker-php-ext-pdo_mysql.ini: Directory nonexistent 
**解决方案：** 在/usr/local/etc/php目录下面新建 conf.d 也可以把 docker-php-ext-pdo_mysql.ini 文件也创建了，文件内容：
```
extension=pdo_mysql.so
```
修改完重新安装。

## gd
相关报错 *Call to undefined function think\captcha\imagecreate()* 说明缺少gd扩展

### 安装依赖库
```
apt install -y libwebp-dev libjpeg-dev libpng-dev libfreetype6-dev 
```

### 解压源码
```
docker-php-source extract 
```

### 配置编译环境
```
docker-php-ext-configure gd --with-webp-dir=/usr/include/webp --with-jpeg-dir=/usr/include --with-png-dir=/usr/include --with-freetype-dir=/usr/include/freetype2
```

### 编译安装
```
docker-php-ext-install gd
```

### 查看结果
```
php -m | grep gd
```






