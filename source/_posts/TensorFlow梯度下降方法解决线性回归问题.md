---
title: TensorFlow梯度下降方法解决线性回归问题
date: 2018-09-14 14:35:50
tags: 机器学习
author: 孙远博
---

# TensorFlow 梯度下降方法解决线性回归问题



这是 TensorFlow 最简单的问题了，入门级的。

```python
"""
使用梯度下降的优化方法快速解决线性回归问题
"""

import tensorflow as tf
import numpy as np
import matplotlib.pyplot as plt

# 构造输入数据
# 数据数量
points_num = 1000
vectors = []
# 使用 Numpy 的正态分布函数生成 100 个点
# 这些点的 (x, y) 的坐标值对应线性方程 y = 0.1 * x + 0.2
# 权重 (Weight) 0.1，偏差 (Bias) 0.2
for i in range(points_num):
    x1 = np.random.normal(0.0, 1.0)
    y1 = 0.1 * x1 + 0.2 + np.random.normal(0.0, 0.05)
    vectors.append([x1, y1])

x_data = [v[0] for v in vectors]
y_data = [v[1] for v in vectors]

# 构建线性回归模型
# 初始化 Weight 和 bias
w = tf.Variable(tf.random_uniform([1], -1.0, 1.0))
b = tf.Variable(tf.zeros([1]))
# 模型计算出来的 y
y = w * x_data + b

# 定义损失函数 (loss function)
# 求其方差
loss = tf.reduce_mean(tf.square(y - y_data))
# 使用优化器优化 loss function
# 设置学习率
optimizer = tf.train.GradientDescentOptimizer(0.05)
# 训练使其方差最小
train = optimizer.minimize(loss)

# 新建会话
with tf.Session() as sess:
    # 初始化所有 tensor
    init = tf.global_variables_initializer()
    sess.run(init)

    # 开始训练
    steps = 100
    for step in range(steps):
        sess.run(train)
        print('Step = %d, Loss = %f\n'
              '[Weight = %f, bias = %f]' % (step, sess.run(loss), sess.run(w), sess.run(b)))

    plt.plot(x_data, y_data, 'r.', label='Original data')
    plt.title('Linear Regression using Gradient Descent')
    # 训练出的拟合的一条曲线
    plt.plot(x_data, sess.run(w) * x_data + sess.run(b), 'b-', label='Fitted line', )
    plt.legend()
    plt.xlabel('x')
    plt.ylabel('y')
    plt.show()

```

结果如图：

![](https://i.loli.net/2018/09/14/5b9b59525ee69.png)

![](https://i.loli.net/2018/09/14/5b9b5acf81c9b.png)
已经非常接近真实值了