---
title: Python练习0000
date: 2018-06-17 16:40:37
tags: Python
author: 孙远博
---

# 第 0000 题

**将你的 QQ 头像（或者微博头像）右上角加上红色的数字，类似于微信未读信息数量那种提示效果。**
这个问题需要用到PIL库，涉及到一些基本的用法 。

```python

# 调用所需要的库
import PIL
from PIL import ImageFont, Image, ImageDraw

# 设置字体
font = ImageFont.truetype("arial.ttf",50)

# 打开要处理的文件
img = Image.open('img.jpg')

# 开始处理文件
draw = ImageDraw.Draw(img)

# 获取图片的宽高
width, height = img.size

# 设置处理的参数
draw.text((0, 0), '中文', font=font)

# 保存文件
img.save('out.jpg')



```

这是把字体直接复制到 py 文件所在目录了。本来还应该有Imagecolor这个模块，但是我没有使用，所以默认出来白色。之前使用一个白色背景的图片去测试，发现处理后的文件没有变化，当时还郁闷半天。

