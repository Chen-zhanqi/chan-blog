---
title: postgresql-创建主键自增的表
date: 2022-06-20
tags:
- 数据库
categories:
- 2022
- 技术
---

```
方法一：

CREATE TABLE test_a (
  id serial,
  name character varying(128),
  constraint pk_test_a_id primary key( id)
);

方法二：

CREATE TABLE test_b(
  id serial PRIMARY KEY,
  name character varying(128)
); 

这两种方法用的是pg的serial类型实现自增，drop表的时候指定的序列也会drop掉

方法三：先创建主键表

CREATE TABLE test_c (
  id integer PRIMARY KEY,
  name character varying(128)
);  

再手动创建序列
CREATE SEQUENCE test_c_id_seq
START WITH 1
INCREMENT BY 1
NO MINVALUE
NO MAXVALUE
CACHE 1;

将序列赋值给主键
ALTER TABLE test_c alter column id set default nextval('test_c_id_seq');

这种方法在drop表的时候序列不会随着drop掉
```

