---
title: Nginx Docker环境配置Certbot
date: 2023-05-19
slug: Nginx-Docker-Certbot-scjkba
tags:
- Docker
- Nginx
categories:
- 2023
- 技术
---



### 安装Certbot

```shell
#进入nginx容器
docker exec -it nginx /bin/bash

#通过apt安装 失败，apt需要升级
apt install certbot -y

#升级apt
apt update
apt full-upgrade

#安装Certbot
apt install certbot -y
```

### 创建ssl证书
```shell
#创建ssl证书 www.chenzqi.cn为需要解析的域名
certbot certonly --manual --preferred-challenge dns -d www.chenzqi.cn
```

**操作记录：**
```shell
root@63830afdfad5:/# certbot certonly --manual --preferred-challenge dns -d www.chenzqi.cn
Saving debug log to /var/log/letsencrypt/letsencrypt.log
Plugins selected: Authenticator manual, Installer None
Enter email address (used for urgent renewal and security notices)
 (Enter 'c' to cancel): XXX@gmail.com

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Please read the Terms of Service at
https://letsencrypt.org/documents/LE-SA-v1.2-November-15-2017.pdf. You must
agree in order to register with the ACME server. Do you agree?
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
(Y)es/(N)o: y

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Would you be willing, once your first certificate is successfully issued, to
share your email address with the Electronic Frontier Foundation, a founding
partner of the Let's Encrypt project and the non-profit organization that
develops Certbot? We'd like to send you email about our work encrypting the web,
EFF news, campaigns, and ways to support digital freedom.
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
(Y)es/(N)o: y
Account registered.
Requesting a certificate for www.chenzqi.cn
Performing the following challenges:
dns-01 challenge for www.chenzqi.cn

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Please deploy a DNS TXT record under the name
_acme-challenge.www.chenzqi.cn with the following value:

f6bGUxSVLYu******************LRfoy-BtYiE8qk

Before continuing, verify the record is deployed.
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Press Enter to Continue
```

**去域名平台按以上信息配置域名解析，然后回来按回车。运气好的话显示生成两个证书文件，成了**


### 配置nginx

```shell
server {
    listen       80;
    listen  [::]:80;

    server_name  www.fuqi.website;
    rewrite ^(.*) https://$server_name$1 permanent;
}
server {
    listen       443 ssl;
    server_name  www.fuqi.website;

	## 这里配置上一步生成的ssl证书路径
    ssl_certificate      /etc/letsencrypt/live/www.chenzqi.cn/fullchain.pem;
    ssl_certificate_key  /etc/letsencrypt/live/www.chenzqi.cn/privkey.pem;

    ssl_session_cache    shared:SSL:1m;
    ssl_session_timeout  5m;

    ssl_ciphers  HIGH:!aNULL:!MD5;
    ssl_prefer_server_ciphers  on;

    location / {
        proxy_set_header   X-Real-IP         $remote_addr;
        proxy_set_header   Host              $http_host;
        proxy_set_header   X-Forwarded-For   $proxy_add_x_forwarded_for;
        root html;
        ##这里配置成具体的ip，不要使用域名
        proxy_pass http://xx.xxx.xxx.xxx:6666;
        index index.html index.htm;
    }

    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }
}
```

### 重启nginx
```shell
docker restart nginx
```

### 自动刷新Certbot
> Certbot证书有效期为90天，且到期前30天可以续期，可以采用定时任务自动续期。
```shell
crontab -e

#每个月1号更新
0 0 1 * * docker exec -it nginx certbot renew 
```






