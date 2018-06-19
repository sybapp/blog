---
title: Python练习0004
date: 2018-06-19 18:58:29
tags: Python
author: 孙远博
---

# 第 0004 题

**任一个英文的纯文本文件，统计其中的单词出现的个数。**

## 正则表达式

```python
"""
以下是使用re正则表达式操作
只统计了总的个数
"""

import re


def count_word(file_path):
    with open(file_path, 'r') as file:
        text = file.read()
        words = re.findall(r'[a-zA-z]+', text)
        count = len(words)
    return count


if __name__ == '__main__':
    print(count_word('text.txt'))



```

## collections 和 os

```python
"""
这个可以统计每个单词出现的个数
"""

import collections
import os


with open('test.txt','r') as fp:
    str1=fp.read().split(' ')
b = collections.Counter(str1)
with open('result.txt','w') as result_file:
    for key,value in b.items():
        result_file.write(key+':'+str(value)+'\n')
```

