---
title: CS系统相关
date: 2022-06-20
tags:
- golang
- 记录
categories:
- 2022
- 技术
---

## cs gocron web&node
**gocron-web**
```
nohup ./gocron-linux-amd64/gocron web > /opt/server/gocron/log/gocron_web.log 2>&1 &
```
**gocron-node**
```
nohup sudo -u gocron ./gocron-node-linux-amd64/gocron-node > /opt/server/gocron/log/gocron_node.log 2>&1 &
```

## cs web vue
```
cd /opt/docker/nginx/html/gin-web/
git pull
docker restart nginx
```
## cs server gin
```
cd /opt/server/gin-server
git pull

ps -ax | grep go
kill ***
nohup go run . >/dev/null 2>&1 &
```

## cs cccf-spider
```
cd /opt/server/cccf-spider
git pull
```