---
title: pod command
date: 2017-03-07 19:50:46
categories: cocoapods
tags:
- command

---
# pod 基础使用命令
1. 创建Podfile文件
  ```sh
    pod init
  ```
2. 使用命令打开Podfile文件
```sh
open -a Xcode Podfile
```
3. 搜索pod 库
```sh
pod search 库名
```
4. 更新本地Repo库
```sh
pod repo update
```
5. 安装pod 库
```sh
pod install
pod install --verbose --no-repo-update
```

6. 升级pod 库
```sh
pod update
pod upadte --verbose --no-repo-update
```
<!-- more -->

# cocoapods库安装命令



* 检查ruby源
```sh
gem sources -l
```

* 删除原有ruby源
```sh
gem sources -remove https://rubygems.org/
```

* 添加你找到的可用镜像源
```sh
gem sources -a http://rubygems-china.oss.aliyuncs.com
```
* 安装cocoapods
 ```sh
 sudo gem install cocoapods
 pod  setup
 ```
* 查看cocoapods当前版本号
 ```sh
 pod --version
 ```

* 更新gem到最新版本
```sh
sudo gem update --system
```

* 更新cocoapods版本
```sh
sudo gem update cocoapods

```
# 查看已安装的cocoapods库

>说明Cocoapods在将它的信息下载到 ~/.cocoapods里；
>cd 到该目录里

14. 查看 repo list
```sh
pod repo list
```
15. 查看文件大小
```sh
du -sh
```
16. 查看安装列表
```sh
pod list
```
