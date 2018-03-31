---
title: 对多个target使用不同的 Bundle Display Name
date: 2016-07-11 15:41:43
tags:
---
在项目多语言包xx.lproj里引入一个叫InfoPlist.strings的文件，可以对同一个App在不同系统语方中显示不同的Display Name 如：
```objc
InfoPlist.strings (English) - "CFBundleDisplayName" = "English Name";
InfoPlist.strings (Chinese) - "CFBundleDisplayName" = "中文";
```
在单个target中很容易做，多个target的时候需要做一点额外的处理，在项目目录下新建与target同名的文件夹（为了方便区分），然后将xx.lproj文件夹复制到各target 下面，目录结构会是这样的
```objc
./Target1/
          en.lproj/InfoPlist.strings
          zh-Hans.lproj/InfoPlist.strings
./Target2/
          en.lproj/InfoPlist.strings
          zh-Hans.lproj/InfoPlist.strings
```
复制后保持项目目录下还有 xx.lproj 文件夹，里面保留 Localizable.strings，因为多语言化一般是通用的，没必要针对每一个 Target 做多语言。复制后的 Target1/xx.lproj 下只有 InfoPlist.strings。然后添加到 Xcode 项目里，打开 Xcode - Views - Utilities （Command+Option+0），在 Target Membership 下针对不同的 Target 把对应文件夹下的 InfoPlist.strings 对应连接起来。
另外有一点需要注意的是：不同target对应着不同的info.plist文件，在不同的target中的`Build Settings`--->`Packaging`--->`Info.plist File`中设置对应的文件路路径，（最好使用相对路径如`$(SRCROOT)/文件夹名/Info.plist`）。
![infoplistRoote](http://7xw3wp.com1.z0.glb.clouddn.com/infoplistRoote.png)
