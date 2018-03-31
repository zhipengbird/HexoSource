---
title: Interface Builder 
date: 2018-03-06 10:14:02
tags:
- xib
---
# Interface Builder 简介
Interface Builder（IB）源于NeXT Interface Builder,从iOS系统的问世到现在，一直伴随着它共同成长。
## IB是什么？
IB是苹果公司给开发者提供的可视化UI开发工具。
>IB之于程序员，就像ps之于设计师，它们都是在可视化编辑UI
## xib是什么？
xib是IB中的一种文件类型，也就是`.xib`扩展名的文件。 xib是文本文件，以xml的形式存储UI相关数据。
xib在项目中作为资源文件存在，**和普通的图片，音频，视频等资源文件有所不同**。其在`Copy Bundle Resource`中显示。
> xib之于程序员，就像psd之于设计师，它们都是一种文件格式，大部分工作都是在可视化地编辑这些文件。

## [Bundle](https://developer.apple.com/documentation/foundation/bundle)
> Bundle： 一种标准化的层次结构，保存了可执行代码及代码所需要的资源。
> A representation of the code and resources stored in a bundle directory on disk.

Bundle的两种表现形式：
* 保存可执行代码
    我们的应用（App）就是一个bundle。因为每个app内部结构都是固定的，而且app这个bundle就是在开发中经常使用的`main bundle`.

* 保存所需要的资源 
    这里的资源包括xib,storyboard，图片，音频，视频，字体等

添加到工程里的资源文件在编译时会被复制到main bundle中，可以在`Copy bundle resources`中查看所有被打包到`main bundle`中的资源文件。只有在`Copy bundle resources`中显示的文件在编译时才会被复制到`main bundle`中，直接向工程里添加的文件和通过`Asset Catalog`方式添加的资源都会在`Copy Bundle resources`中显示。如果向工程中添加了资源，但未显示在`Copy Bundle resources`中显示，我们可以通过`+`按钮，手动添加。否则，在使用该文件时可能会出现问题。

## storyboard  是什么
`storyboard（sb）`是iOS5 的新特性，它提供了一种全新的方式来可视化编辑UI，最早在xcode4.2中出现。sb是基于IB的一种文件类型，就是`.storyboard`扩展名的文件，sb也是文本文件，以`XML`的形式存储着UI相关的数据。IB和SB的这一特性很重要，这使得它们可以以`XML`形式查看，也为解决IB/SB引起的冲突提供了一个突破口。
SB可以理解成一组与VC"关联"的xib集合。该集合中同时也保存了各个VC之间跳转的数据，可以在sb文件上查看每个VC的布局样式、跳转关系等信息，利用sb可以自动完成页面的跳转。
> sb 是xib2.0,即增强版

## [nib](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/LoadingResources/CocoaNibs/CocoaNibs.html) 是什么
xib文件随着工程编译之后发生变化，并且在bundle中产生与之对应的nib文件
> A nib file describes the visual elements of your application’s user interface, including windows, views, controls, and many others. It can also describe non-visual elements, such as the objects in your application that manage your windows and views

>nib 文件描述应用外观的视觉元素，其中包括了窗口、视图、控制对象和其他元素，它也可以描棕非视觉元素，例如应用中管理窗口和对象的对象。

nib名字来源`NeXT Interface Builder`,xib是IB编辑过程的一种文件类型，而nib是工程编译后，由xib生成的一种文件类型。xib文件编译后要被序列化为二进制文件的nib文件，使用nib文件就是进行反序列化。

* 为什么xib在项目中作为资源文件存在，但与其他资源文件（图片，音频，视频等）有所不同
    因为xib文件是需要编译的，编译后xib文件将不存在，而bundle中生成与之对应的nib文件，其他资源文件在编译时仅仅是被复制到bundle中。加载其他资源文件（图片，音频，视频等），可以使用bundle获取对应的path,然后通过path,使用不同的api加载不同的资源。

xib和nib都属于plist文件，plist文件有三种格式： 
* XML
* 二进制
* json
在开发过程中使用的.plist,xib,sb等文件都是XML格式的文件，但是xib经过编译后的nib文件是二进制的plist文件，苹果不允许查看并再次编辑nib文件，所能通过path来加载IB没有意义。
> nib 是序列化的、 加密的xib

## storyboardc是什么
理解了nib,再理解storyboardc就行简单了，storyboardc 是编译后被序列化的storyboard,与nib相似。
简单说：
> IB开发过程中的xib,storyboard属于XML格式的plist文件，当工程被编译后，它们就变成了相应的nib、storyboardc文件，nib、storyboardc属于二进制格式的plist文件，不允许再次查看和编辑。
> storyboardc是序列化的、加密的storyboard
