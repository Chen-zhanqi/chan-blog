---
title: docker-compose安装nginx,mysql,redis
date: 2023-02-18
tags:
- Docker
categories:
- 2023
- 技术
---

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
    command: mysqld --character-set-server=utf8mb4 --collation-server=utf8mb4_unicode_ci
    environment:
      - TZ=Asia/Shanghai
      - MYSQL_ROOT_PASSWORD=qi52051020
    volumes:
      - /etc/localtime:/etc/localtime:ro
      - /opt/docker/mysql/data:/var/lib/mysql
      - /opt/docker/mysql/my.cnf:/etc/mysql/my.cnf
    ports:
      - "3306:3306"
    privileged: true

  redis:
    hostname: redis
    image: redis:5.0.4
    container_name: redis
    restart: always
    command: redis-server /etc/redis.conf
    environment:
      - TZ=Asia/Shanghai
    volumes:
      - /etc/localtime:/etc/localtime:ro
      - /opt/docker/redis/data:/data
      - /opt/docker/redis/redis.conf:/etc/redis.conf
    ports:
      - "6379:6379"
    privileged: true
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
```shell
protected-mode yes
port 6379
tcp-backlog 511
timeout 0
daemonize yes
supervised no
pidfile /var/run/redis_6379.pid
loglevel notice
logfile /var/log/redis/redis.log
databases 16
save 900 1
save 300 10
save 60 10000
stop-writes-on-bgsave-error yes
rdbcompression yes
rdbchecksum yes
dbfilename dump.rdb
dir /var/lib/redis
slave-serve-stale-data yes
slave-read-only yes
repl-diskless-sync no
repl-diskless-sync-delay 5
repl-disable-tcp-nodelay no
slave-priority 100
requirepass 密码
appendonly no
appendfilename "appendonly.aof"
appendfsync everysec
no-appendfsync-on-rewrite no
auto-aof-rewrite-percentage 100
auto-aof-rewrite-min-size 64mb
aof-load-truncated yes
lua-time-limit 5000
slowlog-log-slower-than 10000
slowlog-max-len 128
latency-monitor-threshold 0
notify-keyspace-events ""
hash-max-ziplist-entries 512
hash-max-ziplist-value 64
list-max-ziplist-size -2
list-compress-depth 0
set-max-intset-entries 512
zset-max-ziplist-entries 128
zset-max-ziplist-value 64
hll-sparse-max-bytes 3000
activerehashing yes
client-output-buffer-limit normal 0 0 0
client-output-buffer-limit slave 256mb 64mb 60
client-output-buffer-limit pubsub 32mb 8mb 60
hz 10
aof-rewrite-incremental-fsync yes
```

