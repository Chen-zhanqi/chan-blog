---
title: docker-postgresql数据库备份与恢复
date: 2022-06-20
tags:
- docker
- 数据库
categories:
- 2022
- 技术
---

## 备份脚本
```sh
#!/bin/sh

# 备份存放位置
BACKUP_DIR=/data/backup/pg/

file=$BACKUP_DIR"gin_server_$(date +"%Y%m%d").sql"

docker exec -t postgres  pg_dump -U pq_user -d db_name > "${file}"


# 删除7天之前的备份
deleteDIR=$BACKUP_DIR"gin_server_$(date --date='7 day ago' +%Y%m%d).sql"
rm -rf $deleteDIR
```

## 恢复数据
### 正式数据库
```sh
cat gin_server_20190801.sql| docker exec -i postgres  psql -U pq_user
```
### 其他数据库
1. 创建数据库
```sh
create database dbname_prod_bak_20190722  owner username;
grant all privileges on database dbname_prod_bak_20190722 to username;
```
2. 恢复数据
```sh
cat gin_server_20190801.sql| docker exec -i postgres  psql -U username -d dbname_prod_bak_20190722
```











