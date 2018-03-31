---
title: Framework制作
date: 2016-06-02 17:18:17
tags:
---
知识科谱：
 *什么是库?*  
 库是程序代码的集合，是共享程序代码的一种方式。
 依源代码的公开情况不同，库可以分为2种：  
 **开源库**  
  公开源代码，能看到具体实现（如我们常用的第三方库）  
 **闭源库**  
  不公开源代码，是经过编译后的二进制文件，看不到具体的实现 ，主要分为:
  *静态库* 、*动态库*

  **动/静态库存在的形式**  
  静态库： .a 和 .framework  
  动态库： .dylib 和 .framework

<!-- more -->
  **静态库和动态库在使用上的区别**  
    静态库：链接时，静态库会被完整地复制到可执行文件中， 被多次使用就有多份冗余拷贝  
![静态库](http://upload-images.jianshu.io/upload_images/1112684-e597bfd02e25d1f4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  
  动态库：链接时不复制，程序运行时由系统动态加载到内存，供程序调用，系统只加载一次，多个程序共用，节省内存    
    ![动态库](http://upload-images.jianshu.io/upload_images/1112684-575a9125c1100f1d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  **需要注意的是：项目中如果使用了自制的动态库，不能被上传到 AppStore！**


-------
#### .framework的制作
[iOS-制作Framework（最新）](http://www.jianshu.com/p/ef3d5b7e7006)
#### .a制作
[手把手教你制作.a静态库（iOS开发）](http://www.jianshu.com/p/a1dc024a8a15#)
#### .bundle的制作
[【Xcode小技巧】生成Bundle包](http://www.jianshu.com/p/58c3a27b2649)
