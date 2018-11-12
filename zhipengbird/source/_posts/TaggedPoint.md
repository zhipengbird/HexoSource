---
title: TaggedPoint
date: 2018-11-08 11:02:02
tags: 
- iOS 
---
Tagged Pointer 详细的内容可以看这里 [深入理解Tagged Pointer](http://www.infoq.com/cn/articles/deep-understanding-of-tagged-pointer)

* Tagged Pointer 是一个能够提升性能、节省内存的有趣的技术。
* Tagged Pointer 专门用来存储小的对象，例如 NSNumber 和 NSDate（后来可以存储小字符串）
* Tagged Pointer 指针的值不再是地址了，而是真正的值。所以，实际上它不再是一个对象了，它只是一个披着对象皮的普通变量而已。
* 它的内存并不存储在堆中，也不需要 malloc 和 free，所以拥有极快的读取和创建速度。