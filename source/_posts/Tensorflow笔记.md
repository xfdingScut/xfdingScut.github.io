---
title: Tensorflow笔记
toc: true
date: 2018-11-08 15:32:03
tags: [tensorflow]
categories: [深度学习]
---

# 基本概念

## Tensorflow特点

1. 用 graph 表示计算任务
2. 在被称之为 session 的上下文 context 中执行 graph
3. 用 tensor 表示数据
4. 通过 variable 维护状态
5. 用 feed 和 fetch 为操作赋值或者取值

## Tensorflow基本使用

1. 创建一个图

   - 创建源op，源op不需要任何输入，其输出被传递给其他op做运算。Tensorflow-python有一个默认图，op构造器可以为其增加节点。为默认图增加三个节点，两个 constant() op 和一个 matmul() op。

2. 在一个会话中启动图

   - 创建一个session，如果无任何创建参数，会话构造器将启动默认图

3. 会话在使用完后需要关闭以释放资源，除了显式调用 close 以外，也可以使用 with 代码块，来自动完成关闭动作。

   ```python
   with tf.Session() as sess:
       result = sess.run()
       print result
   ```

**注意：**tensorflow不需要指定在cpu还是gpu上执行，会自动检测，若存在gpu，则自动利用检测到的第一个gpu执行；若存在多个gpu，则需明确将op指派给特定的gpu。



