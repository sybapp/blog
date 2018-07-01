---
title: Python练习0005
date: 2018-07-01 16:12:55
tags: Python
author: 孙远博
---

# **第 0005 题** 

## 最近由于准备期末考试所以没有时间去学一些散的东西了，我感觉挂科正渐渐向我逼近 ······

**你有一个目录，装了很多照片，把它们的尺寸变成都不大于 iPhone5 分辨率的大小。**

```python
import os
from PIL import Image

# 图片文件所在文件夹路径
file_path = 'E:/Picture'
# cd到达图片文件路径
os.chdir(file_path)


# 定义函数去获取当前文件夹下的所有图片文件路径
def get_file_list():
    # 定义空list
    file_list = []
    # os将所有文件路径列出
    file_pathlist = os.listdir(file_path)
    # 经判断为图片的文件路径添加到list
    for i in file_pathlist:
        # 通过后缀名判断文件是否为图片
        if ('.jpg' or '.png') in i:
            file_list.append(i)
        else:
            print(i + ' is not a picture')
    return file_list


# 处理图片
def modify_imgsize():
    for file in get_file_list():
        # 打开文件
        img = Image.open(file)
        # 设置高的最大值（以1920为例）题目中iPhone 5的分辨率为1136
        if max(img.size) > 1920:
            # 通过比例运算出高的值
            img_width = (1920 * min(img.size)) / max(img.size)
            # 对图片处理resize
            new_img = img.resize((1920, int(img_width)), Image.ANTIALIAS)
            # 保存文件并加入前缀
            new_img.save('new_' + file)
            print('new_' + file + ' is saved.')
        else:
            print(file + ' is available.')


if __name__ == '__main__':
    modify_imgsize()

```

## 总结

1. 学会使用os模块处理文件
2. 熟练使用PIL模块处理图片

