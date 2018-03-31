---
title: 给MacOS X下的Python3安装requests模块
date: 2016-09-08 17:17:08
tags:
---
默认情况下直接用
```python
pip install requests
```
会安装到MacOS 自带的Python 2下面，Python 3还是不能使用requests模块。
解决方案

下载request源代码：

```sh
curl -OL https://github.com/kennethreitz/requests/zipball/master
```

加扩展名.zip后解压，从命令行进入这个目录下然后执行：
```sh
python3 setup.py install

```

这样requests模块就被安装在Python 3下面了。
