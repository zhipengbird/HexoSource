---
title: pip安装使用详解  
date: 2017-06-27 14:54:34  
categories: python  
tags: pip 
---

本节详细介绍pip的安装以及使用方法
# pip 下载/安装/更新
## 下载
* 从python官网下载 [pip](https://pypi.python.org/pypi/pip)
* 使用工具下载
```
wget 'https://pypi.python.org/packages/11/b6/abcb525026a4be042b486df43905d6893fb04f05aac21c32c638e939e447/pip-9.0.1.tar.gz#md5=35f01da33009719497f01a4ba69d63c9'
```
## pip 安装
```
 tar -xzvf pip-9.0.1.tar.gz # 解压
 cd pip-9.0.1 # 切换至目录
 sudo python setup.py install   # 安装
```
## pip 更新
```
 pip install -U pip
```

# pip 使用详解
## pip 安装包
```
pip install packagename
```
## 查看已安装的包详情
```
pip show  packagename
```
{% asset_img pip_show.png  查看安装包详情 %}

## pip 列出所有安装的包
```
pip list --format=columns
```
{% asset_img pip_list.png  查看安装包列表 %}
## pip 升级包
```
pip install -U  packagename
```
## pip 卸载包
```
pip uninstall packagename
```
## 检查需要更新的包
```
pip list --outdate --format=columns
```
{% asset_img pip_outdate.png  查看需要更新安装包列表 %}


# pip参数解释
```
 pip --help
 
Usage:   
  pip <command> [options]
 
Commands:
  install                     安装包.
  uninstall                   卸载包.
  freeze                      按着一定格式输出已安装包列表
  list                        列出已安装包.
  show                        显示包详细信息.
  search                      搜索包，类似yum里的search.
  wheel                       Build wheels from your requirements.
  zip                         不推荐. Zip individual packages.
  unzip                       不推荐. Unzip individual packages.
  bundle                      不推荐. Create pybundles.
  help                        当前帮助.
 
General Options:
  -h, --help                  显示帮助.
  -v, --verbose               更多的输出，最多可以使用3次
  -V, --version               现实版本信息然后退出.
  -q, --quiet                 最少的输出.
  --log-file <path>           覆盖的方式记录verbose错误日志，默认文件：/root/.pip/pip.log
  --log <path>                不覆盖记录verbose输出的日志.
  --proxy <proxy>             Specify a proxy in the form [user:passwd@]proxy.server:port.
  --timeout <sec>             连接超时时间 (默认15秒).
  --exists-action <action>    Default action when a path already exists: (s)witch, (i)gnore, (w)ipe, (b)ackup.
  --cert <path>               证书.
```




