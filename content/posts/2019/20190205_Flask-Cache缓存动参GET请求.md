---
title: Flask-Cache缓存动参GET请求
date: 2019-02-05
tags:
 - 框架
categories:
 - 2019
 - 技术
---


**自定义动态参数缓存键**

**1**
``` python
def make_cache_key():
    args = request.args
    key = request.path + '?' + urllib.parse.urlencode([
        (k, v) for k in sorted(args) for v in sorted(args.getlist(k))
    ])
    return key
```
复杂，好处是即使表单数据顺序打乱，在转换为cache_key时也sorted()过的，不会出现冗余cache的情况

**2**
``` python
def make_cache_key(*args, **kwargs):
    args = str(hash(frozenset(request.args.items())))
    key = str((request.path + args).encode('utf-8'))
    return key
```

**使用**
``` python
@cache.cached(timeout=600, key_prefix=make_cache_key)
def 路由函数：
    return jsonify(status=200, msg="", data="")
```
