---
title: Python练习0002
date: 2018-06-18 20:26:23
tags: Python
author: 孙远博
---

# 第 0002 题

**将 0001 题生成的 200 个激活码（或者优惠券）保存到 MySQL 关系型数据库中。**

```python
# 调用 python 的随机库和字符串库
import random, string, pymysql

# 激活码由字母和数字组成， string.letter 表示所有的字母，string.digits则表示[0 - 9]所有的数字
key = string.ascii_letters + string.digits


# 获得四个(字母和数字)的随机组合
def getRandom():
    return "".join(random.sample(key, 4))


# 将上述生成的group个随机组合用'-'连接起来
def getKey(group):
    return "-".join(getRandom() for i in range(group))


# 生成n个key并放到一个list里
def getKeyForN(n):
    return [getKey(4) for i in range(n)]

# Key是存放200个随机码的列表
Key = getKeyForN(200)

# 连接MySQL数据库，密码未设置
conn = pymysql.connect(
    host='localhost',
    port=3306,
    user='root',
    password='******'
)
cursor = conn.cursor()

# 使用execute方法执行sql语句
cursor.execute('create database if not exists test')
conn.select_db('test')
cursor.execute('create table if not exists codes(code char(64))')
# 将Key遍历放入数据库中
for i in Key:
    cursor.execute('insert into codes values(%s)', i)

# 提交并关闭连接
conn.commit()
conn.close()
cursor.close()

```

