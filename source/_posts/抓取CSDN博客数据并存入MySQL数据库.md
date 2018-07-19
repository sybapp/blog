---
title: 抓取CSDN博客数据并存入MySQL数据库
date: 2018-07-19 15:38:30
tags: Python 爬虫
author: 孙远博
---

终于放暑假了，这也算当作暑假的任务之一



## 爬取内容

- 贺老师的CSDN博客
- 所有文章内容，标题，发表时间，阅读数，评论数，分类，原文地址，并存入MySQL数据库



## 使用的库

- re正则表达式
- pymysql
- multiprocessing多进程库



## 代码

### spider.py

```python
import re
import requests
from requests.exceptions import RequestException


def get_page(list_page):
    url = 'https://blog.csdn.net/sxhelijian/article/list/' + str(list_page)
    headers = {
        'User-Agent': 'Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) '
                      + 'Chrome/67.0.3396.99 Safari/537.36'
    }
    try:
        response = requests.get(url, headers=headers)
        if response.status_code == 200:
            return response.text
        return None
    except RequestException:
        print('请求出错！')
        return None

def parse_page(html):
    pattern = re.compile(r'<h4.*?href="(.*?)".*?</span>(.*?)</a>.*?date">(.*?)</span>.*?'
                         + r'阅读数：(.*?)</span>.*?评论数：(.*?)</span>', re.S)
    items = re.findall(pattern, html)
    for item in items:
        yield {
            '标题': item[1].strip(),
            '发表时间': item[2],
            '阅读数': item[3],
            '评论数': item[4],
            '原文地址': item[0]
        }


def get_classhtml(url):
    headers = {
        'User-Agent': 'Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) '
                      + 'Chrome/67.0.3396.99 Safari/537.36'
    }
    try:
        response = requests.get(url, headers=headers)
        if response.status_code == 200:
            return response.text
    except RequestException:
        print('请求出错！')
        return None


def get_class(html):
    pattern1 = re.compile(r'<div id="article_content".*?>(.*?)</div>', re.S)
    pattern2 = re.compile(r'个人分类：.*?target="_blank">(.*?)<', re.S)
    pattern3 = re.compile(r'<[^>]+>', re.S)
    context = re.findall(pattern1, html)
    item1 = pattern3.sub('', context[0].strip())
    item2 = re.findall(pattern2, html)
    if item2:
        for item in item2:
            yield {
                '文章内容': item1,
                '个人分类': item.strip()
            }
    else:
        yield {
            '文章内容': item1,
            '个人分类': '未分类'
        }


def main(list_page):
    for item in parse_page(get_page(list_page)):
        dict1 = item
        for i in get_class(get_classhtml(item['原文地址'])):
            dict2 = i
            info = dict(dict1, **dict2)
            yield info


if __name__ == '__main__':
    for i in range(1, 10):
        main(i)


```



### mysql.py

```python
import pymysql
import spider
import multiprocessing

database = 'csdn'


def create_db(database):
    db = pymysql.connect(
        host='localhost',
        port=3306,
        user='root',
        password='******',
    )
    cursor = db.cursor()
    cursor.execute('SELECT VERSION()')
    print('Databases version ', cursor.fetchone())
    sql = 'CREATE DATABASE IF NOT EXISTS {} DEFAULT CHARACTER SET utf8mb4'.format(database)
    cursor.execute(sql)
    db.close()


def create_table(database):
    db = pymysql.connect(
        host='localhost',
        port=3306,
        user='root',
        password='sybappe377551',
        db='{}'.format(database)
    )
    cursor = db.cursor()
    sql = 'CREATE TABLE IF NOT EXISTS {} (' \
          'title VARCHAR(255) NOT NULL, ' \
          'date VARCHAR(255) NOT NULL, ' \
          'readnum VARCHAR(255) NOT NULL, ' \
          'commentnum VARCHAR(255) NOT NULL, ' \
          'articleurl VARCHAR(255) NOT NULL, ' \
          'articleclass VARCHAR(255) NOT NULL, ' \
          'context TEXT NOT NULL, ' \
          'PRIMARY KEY (title))'.format(database)
    cursor.execute(sql)
    db.close()
def insertdb(database, dict):
    db = pymysql.connect(
        host='localhost',
        port=3306,
        user='root',
        password='sybappe377551',
        db='{}'.format(database)
    )
    title = dict['标题']
    date = dict['发表时间']
    readnum = dict['阅读数']
    commentnum = dict['评论数']
    articleurl = dict['原文地址']
    articleclass = dict['个人分类']
    context = dict['文章内容']
    sql = 'INSERT INTO csdn(title, date, readnum, commentnum, articleurl, articleclass, context) ' \
          'values(%s, %s, %s, %s, %s, %s, %s)'
    cursor = db.cursor()
    try:
        cursor.execute(sql, (title, date, readnum, commentnum, articleurl, articleclass, context))
        db.commit()
    except Exception:
        db.rollback()
    db.close()


def getandinsert(page):
    for item in spider.main(page):
        insertdb(database, item)


if __name__ == '__main__':
    create_db(database)
    create_table(database)
    pool = multiprocessing.Pool()
    pool.map(getandinsert, [page for page in range(1, 100)])

```



运行`mysql.py`即可开始爬取



### 运行结果

![运行结果](https://t1.aixinxi.net/o_1cip8dleg67i1uf50gp5m1aqpa.png-w.jpg)