---
title: 添加pch文件
tags:
---
参考链接：http://www.jianshu.com/p/f6bdd4e3f097


使用pch的好处：
1、存放一些配置字符串便于全局修改
2、用来包含一些常用的头文件（避免工程中反复添加）
3、可以写一些功能方法 比如 一些好用的宏

添加过程
1. 创建并使用pch  
  Command+N，打开新建文件窗口：ios->other->PCH file，创建一个pch文件
  作为资源配置文件，建议放到Supporting Files下。
2. 设置路径
  在build setting中，搜索prefix header，如图。
  首先设置precompile prefix header为yes，预编译后pct文件会被缓存起来，提高编译速度。
  然后添加刚刚创建的pch文件的工程路径，添加格式：“$(SRCROOT)/项目名称/pch文件名”，  
  $(SRCROOT)的意思就是工程根目录的意思。如果不清楚具体路径的话，可以选择该文件右键show in finder：
3. Command+b 编译运行
