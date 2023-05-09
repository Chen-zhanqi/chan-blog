---
title: Scrapy process_item方法写入数据库记录
date: 2019-04-01
tags:
 - 框架
categories:
 - 2019
 - 技术
---



## pipelines.py
``` python
class MysqlPipeline():
    def __init__(self, host, database, user, password, port):
        self.host = host
        self.database = database
        self.user = user
        self.password = password
        self.port = port

    @classmethod
    def from_crawler(cls, crawler):
        return cls(
            host=crawler.settings.get('MYSQL_HOST'),
            database=crawler.settings.get('MYSQL_DATABASE'),
            user=crawler.settings.get('MYSQL_USER'),
            password=crawler.settings.get('MYSQL_PASSWORD'),
            port=crawler.settings.get('MYSQL_PORT'),
        )

    def open_spider(self, spider):
        self.db = pymysql.connect(self.host, self.user, self.password, self.database, charset='utf8', port=self.port)
        self.cursor = self.db.cursor()

    def close_spider(self, spider):
        self.db.close()

    def process_item(self, item, spider):
        data = dict(item)
        # data['imgs'] = data['image_paths'][0]
        # del data['image_paths']

        com_id = data.get('com_id')
        agg_time = data.get('agg_time')
        check_sql = "SELECT com_id FROM itjuzi WHERE com_id={} AND agg_time={}".format(com_id, repr(agg_time))
        count = self.cursor.execute(check_sql)
        if count:
            print("*" * 20, "{} {} 已存在".format(com_id, data.get('name')))

        else:
            keys = ', '.join(data.keys())
            values = ', '.join(['%s'] * len(data))
            sql = 'INSERT INTO %s (%s) VALUES (%s)' % ('itjuzi', keys, values)
            value = tuple([v for v in data.values()])
            self.cursor.execute(sql, value)
            self.db.commit()
            print("#" * 20, "{} {} 储存成功".format(com_id, data.get('name')))

        return item
```
