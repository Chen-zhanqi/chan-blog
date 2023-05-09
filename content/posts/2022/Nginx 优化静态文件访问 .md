---
title: Nginx 优化静态文件访问
date: 2022-06-20
tags:
- nginx
categories:
- 2022
- 技术
---

Nginx 对于处理静态文件的效率要远高于 Web 框架，因为可以使用 gzip 压缩协议，减小静态文件的体积加快静态文件的加载速度、开启缓存和超时时间减少请求静态文件次数。
# 一 开启 gzip
```
gzip on;
#该指令用于开启或关闭gzip模块(on/off)

gzip_buffers 16 8k;
#设置系统获取几个单位的缓存用于存储gzip的压缩结果数据流。16 8k代表以8k为单位，安装原始数据大小以8k为单位的16倍申请内存

gzip_comp_level 6;
#gzip压缩比，数值范围是1-9，1压缩比最小但处理速度最快，9压缩比最大但处理速度最慢

gzip_http_version 1.1;
#识别http的协议版本

gzip_min_length 256;
#设置允许压缩的页面最小字节数，页面字节数从header头得content-length中进行获取。默认值是0，不管页面多大都压缩。这里我设置了为256

gzip_proxied any;
#这里设置无论header头是怎么样，都是无条件启用压缩

gzip_vary on;
#在http header中添加Vary: Accept-Encoding ,给代理服务器用的

gzip_types
    text/xml application/xml application/atom+xml application/rss+xml application/xhtml+xml image/svg+xml
    text/javascript application/javascript application/x-javascript
    text/x-json application/json application/x-web-app-manifest+json
    text/css text/plain text/x-component
    font/opentype font/ttf application/x-font-ttf application/vnd.ms-fontobject
    image/x-icon;
#进行压缩的文件类型,这里特别添加了对字体的文件类型

gzip_disable "MSIE [1-6]\.(?!.*SV1)";
#禁用IE 6 gzip

```
# 二 开启超时时间
通过设置Expires，开启缓存。
```
location ~ .*\.(gif|jpg|jpeg|png|bmp|swf|flv|ico)$ {
    expires 30d;
    access_log off;
}

location ~ .*\.(eot|ttf|otf|woff|svg)$ {
    expires 30d;
    access_log off;
}

location ~ .*\.(js|css)?$ {
    expires 7d;
    access_log off;
}

```

