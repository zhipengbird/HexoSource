---
title: Python-tips
date: 2017-05-22 21:08:54
categories:
tags:
---
# Python 一些实用技巧知识点集合
1. python中的os.system()和os.popen()区别
python调用Shell脚本或者是调用系统命令，有两种方法：os.system(cmd)或os.popen(cmd),前者返回值是脚本的退出状态码，后者的返回值返回一个文件描述符号为fd的打开的文件对象。实际使用时视需求情况而选择。


2. Python 模块或者包名应该遵守以下的规则:
    * 全小写
    * 不要和pypi上已有的包名重复，即使你不想公开发布你的包，因为你的包可能作为其他包的依赖包
    * 使用下划线分隔单词或者什么都不用(不要使用连字符)

3. python 包的上传 [setuptools](http://setuptools.readthedocs.io/en/latest/setuptools.html)
    `python setup.py sdist register upload`