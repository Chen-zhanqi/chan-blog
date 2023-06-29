---
title: docker-compose安装nginx,mysql,redis,postgres
date: 2023-02-18
slug: docker-installs-nginx-redis-mysql-postgres
tags:
- Docker
categories:
- 2023
- 技术
---

### tree结构
```angular2html
docker
├── compose
│   ├── db
│   └── nginx
├── mysql
│   ├── data
│   └── my.cnf
├── nginx
│   ├── conf.d
│   ├── etc
│   ├── html
│   ├── logs
│   ├── nginx.conf
│   └── www
└── redis
│   ├── data
│   └── redis.conf
├── postgresql
│   └── data
```

### docker-compose.yml
```shell
services:
  nginx:
    restart: always
    container_name: nginx
    image: nginx
    ports:
      - 80:80
      - 443:443
    volumes:
      - /opt/docker/nginx/html:/usr/share/nginx/html
      - /opt/docker/nginx/www:/var/www
      - /opt/docker/nginx/logs:/var/log/nginx
      - /opt/docker/nginx/nginx.conf/:/etc/nginx/nginx.conf
      - /opt/docker/nginx/etc/cert:/etc/nginx/cert
      - /opt/docker/nginx/conf.d:/etc/nginx/conf.d
    environment:
      - NGINX_PORT=80
      - TZ=Asia/Shanghai
    privileged: true
    
  mysql:
    hostname: mysql
    image: mysql:5.7.16
    container_name: mysql
    restart: always
    environment:
      - TZ=Asia/Shanghai
      - MYSQL_ROOT_PASSWORD=123456
      - MYSQL_USER=root
      - MYSQL_PASSWORD=123456
    volumes:
      - /etc/localtime:/etc/localtime:ro
      - /opt/docker/mysql/data:/var/lib/mysql
      - /opt/docker/mysql/my.cnf:/etc/mysql/my.cnf
    ports:
      - "3306:3306"
    privileged: true

  redis:
    hostname: redis
    image: redis
    container_name: redis
    restart: always
    command: redis-server /etc/redis.conf --appendonly yes
    environment:
      - TZ=Asia/Shanghai
    volumes:
      - /etc/localtime:/etc/localtime:ro
      - /opt/docker/redis/data:/data
      - /opt/docker/redis/redis.conf:/etc/redis.conf
    ports:
      - "6379:6379"
    privileged: true
   
  postgres:
    image: postgres:12-alpine
    container_name: postgres
    restart: always
    environment:
      - POSTGRES_USER=root
      - POSTGRES_PASSWORD=123456
    ports:
      - "5432:5432"
    volumes:
      - /opt/docker/postgresql/data:/var/lib/postgresql/data
    privileged: true
```


### nginx > nginx.conf
```shell
user  nginx;
worker_processes  auto;

error_log  /var/log/nginx/error.log notice;
pid        /var/run/nginx.pid;


events {
    worker_connections  1024;
}


http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    keepalive_timeout  65;

    #gzip  on;

    include /etc/nginx/conf.d/*.conf;
}
```

### mysql > my.cnf
```shell
[mysqld]
user=mysql
default-storage-engine=INNODB
character-set-server=utf8
explicit_defaults_for_timestamp=1
[client]
default-character-set=utf8
[mysql]
default-character-set=utf8
```

### redis > redis.conf
[各版本配置文件](https://redis.io/docs/management/config/)

```shell
cd /opt/docker/redis/
wget https://raw.githubusercontent.com/redis/redis/6.0/redis.conf
```

```shell
# 将如下修改为0.0.0.0，否则只能本地访问
# bind 127.0.0.1 -::1
bind 0.0.0.0

# 将如下配置修改为no，允许来自任意主机的连接
# protected-mode yes
protected-mode no

# 设置密码
# requirepass foobared
requirepass xxxxxxxx

# 持久化
appendonly yes

# 使用docker -d 时设置为no，否则冲突
daemonize no
```

