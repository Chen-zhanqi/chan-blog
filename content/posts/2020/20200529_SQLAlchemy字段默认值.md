---
title: SQLAlchemy字段默认值
date: 2020-05-29
tags:
 - 记录
 - flask
categories:
 - 2020
 - 技术
---

:::tip
Flask SQLAlchemy字段默认值参数 server_default 与 default 区别
:::
<!-- more -->

### defult
这种方法不会在数据库里面设置默认值，是使用了SQLAlchemy Model类提交数据时添加上去的值，不适合需要取数据库所有服务器时间的场合。
``` python
from sqlalchemy.sql.sqltypes import TIMESTAMP
 
class Test(db.Model):
    id = db.Column(db.Integer, primary_key = True)
    name = db.Column(db.String(32))
    create_date = db.Column(TIMESTAMP, default = datetime.datetime)
```

### server_default
使用 SQLAlchemy 模型直接在数据库设置默认值的方法：

::: warning 需要注意的 ?
Integer 默认值不能直接设置数字，需要是字符串；
DateTime 默认值需要使用func.now()；
自动更新的时间戳可以使用TIMESTAMP，只需设置nullable=False即可
:::

``` python
#mysql 日期设置默认值必须使用timestamp类型
from sqlalchemy.sql.sqltypes import TIMESTAMP
 
#func用来生成数据库函数代码，跟踪进源代码看一***
from sqlalchemy.sql import func
 
class Test(db.Model):
    id = db.Column(db.Integer, primary_key = True)
    name = db.Column(db.String(32))
    create_date = db.Column(TIMESTAMP, server_default = func.now()) # 注意这里是 server_default
```
