---
title: CGRect相关方法的使用说明
date: 2018-03-05 17:25:17
tags:
- CGRect 
---

# CGRectInset和CGRectOffset。
两者的作用都是通过参数改变CGRect并返回一个CGRect类型的数据。
两者的区别：
* CGRectInset会进行平移和缩放两个操作。
* CGRectOffset做的只是平移。

## CGRectInset：
CGRectInset(CGRect rect, CGFloat dx, CGFloat dy),三个参数。
* rect：待操作的CGRect。
* dx：为正数时，向右平移dx，宽度缩小2dx。为负数时，向左平移dx，宽度增大2dx。
* dy：为正数是，向下平移dy，高度缩小2dy。为负数是，向上平移dy，高度增大2dy。

## CGRectOffset：
CGRectOffset(CGRect rect, CGFloat dx, CGFloat dy),三个参数。
* rect：待操作的CGRect。
* dx：为正数时，向右平移dx。为负数时，向左平移dx。
* dy：为正数时，想下平移dy。为负数时，向上平移dy。
