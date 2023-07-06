---
title: php容器内安装SG11扩展
date: 2023-03-10
slug: Install-the-SG-11-extension-in-the-php-container
tags:
- Docker
categories:
- 2023
- 技术
---

### 查看PHP_VERSION
```shell
php -v | head -n1 | cut -d' ' -f2 | cut -d. -f1-2
```

### 安装
```shell
mkdir -p /tmp/sourceguardian && cd /tmp/sourceguardian

curl -Os https://www.sourceguardian.com/loaders/download/loaders.linux-x86_64.tar.gz

tar xzf loaders.linux-x86_64.tar.gz

cp ixed.${PHP_VERSION}.lin "$(php -i | grep '^extension_dir =' | cut -d' ' -f3)/sourceguardian.so"

echo "extension=sourceguardian.so" > /usr/local/etc/php/conf.d/15-sourceguardian.ini

rm -rf /tmp/sourceguardian
```

### 重启容器&验证
```shell
<?php
echo phpinfo();
```

![](https://qn.chenzqi.cn/blog/202307061845843.png)

