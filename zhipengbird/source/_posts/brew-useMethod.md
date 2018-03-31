---
title: homebrew 的使用写安装
date: 2016-09-23 11:46:18
tags:
---
简介：
  [Homebrew](http://brew.sh/index_zh-cn.html)
> Homebrew是Mac OSX上的软件包管理工具，能在Mac中方便的安装软件或者卸载软件，相当于linux下的apt-get、yum神器；Homebre可以在Mac上安装一些OS X没有的UNIX工具，Homebrew将这些工具统统安装到了 /usr/local/Cellar 目录中，并在 /usr/local/bin 中创建符号链接。

**Homebrew的安装**
打开终端窗口, 粘贴以下脚本：
```sh
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

**Homebrew的使用**
* 安装软件：
    ```
    brew install softname(软件名)
    brew install python3
    ```
* 搜索软件：
    ```
    brew search softname(软件名)
    brew search python3
    ```
* 卸载软件：
    ```
    brew uninstall softname(软件名)
    brew uninstall python3
    ```
* 更新软件：
    * 更新所有软件：
      ```
      brew update
      ```
    * 更新具体软件：
      ```
      brew upgrade softname(软件名)
      brew upgrade git
      ```
* 显示已安装软件：
  ```
  brew list
  ```
* 查看软件信息：
  ```
    brew info/home softname(软件名)
    brew info git /brew home git
  ```
  > brew home指令是用浏览器打开官方网页查看软件信息

* 查看那些已安装的程序并且需要更新的：
  ```
  brew outdated
  ```
* 显示包依赖：
  ```
  brew reps
  ```
