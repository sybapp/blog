---
title: flask仿豆瓣微信小程序
date: 2018-08-13 19:07:01
tags: Python
author: 孙远博
---

## flask仿豆瓣微信小程序

这是知了课堂上的第三章上的小案例，仿照豆瓣的微信小程序UI。由于没有学习到flask操作MySQL数据库，故使用 文件的方式进行传输后台数据。

## 代码

#### app.py

flask启动文件

```python
from flask import Flask, render_template
from context import *

context = {
    'movies': movies,
    'tvs': tvs,
}

app = Flask(__name__)


@app.route('/')
def index():
    return render_template('index.html', **context)


@app.route('/list/<id>')
def list(id):
    if id == '1':
        items = movies
    else:
        items = tvs
    return render_template('list.html', items=items)


```

#### context.py 

数据(由于是测试用数据，所以使用了重复来增加数量，感觉以后可以使用爬虫将数据存储到数据库，然后使用flask调用)

```
# 电影
movies = [
    {
        'thumbnail': 'https://img3.doubanio.com/view/movie_poster_cover/lpst/public/p2499792043.webp',
        'title': u'王牌特工2：黄金圈',
        'rating': u'7.3',
    },
    {
        'title': u'羞羞的铁拳',
        'thumbnail': u'https://img1.doubanio.com/view/movie_poster_cover/lpst/public/p2499793218.webp',
        'rating': u'7.6',
    },
   ...
   ...

# 电视剧
tvs = [
    {
        'title': u'鬼吹灯之精绝古城',
        'thumbnail': u'https://img3.doubanio.com/view/movie_poster_cover/lpst/public/p2404604903.jpg',
        'rating': u'8.4',
    },
    {
        'title': u'孤芳不自赏',
        'thumbnail': u'https://img1.doubanio.com/view/movie_poster_cover/lpst/public/p2407425119.jpg',
        'rating': u'3.7',
    },
    ...
    ...

```

#### templates

##### base.html

父模板

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>{% block title %}{% endblock %}</title>
    <link rel="stylesheet" href="{{ url_for('static', filename='css/base.css') }}">
    {% block head %}
    {% endblock %}
</head>
<body>
<div class="container">
    <div class="search-group">
        <input type="text" class="search-input">
    </div>
    <div class="item-list-group">
        {% block body %}
        {% endblock %}
    </div>
</div>
{% block footer %}
{% endblock %}
</body>
</html>
```

##### index.html

首页的实现

```html
{% extends 'base.html' %}
{% from 'macros.html' import itemGroup, itemListGroup %}

{% block title %}豆瓣评分{% endblock %}

{% block head %}
    <link rel="stylesheet" href="{{ url_for('static', filename='css/main.css') }}">
{% endblock %}

{% block body %}
    {{ itemListGroup('电影', movies, 1) }}
    {{ itemListGroup('电视剧', tvs, 2) }}
{% endblock %}
```

##### list.html

列表页的实现

```html
{% extends 'base.html' %}
{% from 'macros.html' import itemGroup %}

{% block title %}
    {% if items == movies %}
        豆瓣电影评分
    {% else %}
        豆瓣电视剧评分
    {% endif %}
{% endblock %}

{% block head %}
    <link rel="stylesheet" href="{{ url_for('static', filename='css/main.css') }}">
    <style>
        .item-list-group {
            overflow: hidden;
            padding: 10px;
        }
    </style>
{% endblock %}

{% block body %}
    {% for item in items %}
        {{ itemGroup(item.thumbnail,item.title,item.rating) }}
    {% endfor %}
{% endblock %}
```

##### macros.html

每一个div的宏

```html
{# itemGroup宏 #}
{% macro itemGroup(thumbnail, title, rating) %}
    <div class="item-group">
        <img src="{{ thumbnail }}" class="thumbnail">
        <p class="item-title">{{ title | truncate(4) }}</p>
        <p class="item-rating">
            {% set lights = (rating | int / 2) | int %}
            {% set halfs = (rating | int % 2) | int %}
            {% set grays = 5 - lights - halfs %}
            {% for light in range(0, lights) %}
                <img src="{{ url_for('static', filename='imgs/rate_light.png') }}">
            {% endfor %}
            {% for half in range(0, halfs) %}
                <img src="{{ url_for('static', filename='imgs/rate_half.png') }}">
            {% endfor %}
            {% for foo in range(0, grays) %}
                <img src="{{ url_for('static', filename='imgs/rate_gray.png') }}">
            {% endfor %}
            {{ rating }}
        </p>
    </div>
{% endmacro %}

{# itemListGroup宏 #}
{% macro itemListGroup(class, items, id=id) %}
    <div class="item-list-top">
        <span class="module-title">{{ class }}</span>
        <a href="{{ url_for('list', id = id) }}" class="more-btn">更多</a>
    </div>
    <div class="list-group">
        {% for item in items[0:4] %}
            {{ itemGroup(item.thumbnail, item.title, item.rating) }}
        {% endfor %}
    </div>
{% endmacro %}

```

#### static

##### 	css

		###### 		base.css

​		父级CSS

```css
* {
    margin: 0;
    padding: 0;
    list-style: none;
    text-decoration: none;
    font-family: "Microsoft YaHei";
}

.container {
    width: 480px;
    height: 720px;
    background: #fff;
}

.search-group {
    padding: 14px 8px;
    background: #38ad54;
}

.search-group .search-input {
    background: #fff;
    display: block;
    width: 100%;
    height: 30px;
    /*设置圆角 */
    border-radius: 5px;
    /*消除输入时的蓝色边框*/
    outline: none;
    /*消除输入框内边框 */
    border: none;
}

.item-list-group .item-list-top{
    overflow: hidden;
    padding: 10px;
}
```

###### 		main.css

​		主CSS

```css
.list-group {
    overflow: hidden;
    padding: 0 10px;
}

.item-group {
    float: left;
    margin : 2px 5px;
    padding: 2px;
}

.item-group .thumbnail {
    display: block;
    width: 100px;
    height: 150px;
}

.item-group .item-title, .item-group .item-rating {
    font-size: 12px;
    text-align: center;
}

.item-rating img {
    width: 10px;
    height: 10px;
}

.module-title {
    float: left;
}

.more-btn {
    float: right;
}


```

#### imgs

存有评分的星星图标

	##### 	rate_gray.png

##### 	rate_half.png

##### 	rate_light.png



### 启动

控制台

windows

```bash
set FLASK_APP=app.py
set FLASK_ENV=development # 开启debug模式
flask run
```

Linux

```bash
export FLASK_APP=app.py
export FLASK_ENV=development # 开启debug模式
flask run
```



### 源码

[GitHub](https://github.com/sybapp/flask_study/tree/master/%E4%BB%BF%E8%B1%86%E7%93%A3%E5%BE%AE%E4%BF%A1%E5%B0%8F%E7%A8%8B%E5%BA%8F)

