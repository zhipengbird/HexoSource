---
title: cocoapods系列教程1
date: 2017-06-02 10:21:47
categories: cocoapods
tags:
---

# cocoapods 打包类库
需要使用一个cocoapods的插件cocoapods-packager来完成类库的打包。当然也可以手动编译打包，但是过程会相当繁琐。
* 安装打包插件
终端执行以下命令
```sh
sudo gem install cocoapods-packager
```

* 打包
命令很简单，执行
```sh
pod package XXXX.podspec --library --force
```
命令参数
```sh 
//强制覆盖之前已经生成过的二进制库 
--force

//生成静态.framework 
--embedded

//生成静态.a 
--library

//生成动态.framework 
--dynamic

//动态.framework是需要签名的，所以只有生成动态库的时候需要这个BundleId 
--bundle-identifier

//不包含依赖的符号表，生成动态库的时候不能包含这个命令，动态库一定需要包含依赖的符号表。 
--exclude-deps

//表示生成的库是debug还是release，默认是release。--configuration=Debug 
--configuration
```


参考链接
[pod 二进制化](https://www.zybuluo.com/qidiandasheng/note/595740)

[use_frameworks!](https://segmentfault.com/a/1190000007076865)