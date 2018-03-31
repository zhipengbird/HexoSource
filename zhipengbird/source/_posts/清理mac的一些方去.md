---
title: 清理mac的一些方去
date: 2016-07-11 11:42:02
tags:
---
### 一． 禁用SafeSleep休眠模式（可逆转操作）
SafeSleep的大小基本等同于设备的实际内存大小，例如Macbook Air是4GB的内存，那么SafeSleep的大小也是4GB左右。
禁用SafeSleep的弊端：如果电脑断电了，（没电）将无法恢复到断电前的状态。
禁用SafeSleep后需要：确保在电池将没电前保存好需求存储的内容。
具体操作：
1.开启Terminal
2.禁用SafeSleep，输入：
sudo pmset -a hibernatemode 0
3.然后定位到/private/var/vm/删除已经存在的sleepimage文件，输入：
cd /private/var/vm/
4.删除该文件，输入：
sudo rm sleepimage
5.制作一个替身，阻止系统新建SafeSleep文件，输入：
touch sleepimage
chmod 000 /private/var/vm/sleepimage
6.如果想恢复SafeSleep服务，请输入：
sudo pmset -a hibernatemode 3
sudo rm /private/var/vm/sleepimage
### 二．清理系统噪音文件（不可逆转操作）
Mac OS X内置了很多噪音文件，如果日常并不使用文字转换语音这个功能，这些文件可以删除以获取更多的磁盘空间。
具体操作：
1.开启 Terminal
2.定位到文件位置，输入：
cd /System/Library/Speech/
3.执行删除命令，输入：
sudo rm -rf Voices/*
### 三．删除所有系统日志
系统日志是用来调试和校验系统故障的，其文件会随着使用时间增长而增加，通常，此部分文件对于一般用户而言没有太多意义，可以删除。
具体操作：
1.开启Terminal
2.输入删除命令：
sudo rm -rf /private/var/log/*
请注意：系统日志文件是会不断的产生的，所以如果想删除，请定期操作。
### 四．删除QuickLook缓存文件
Quicklook 这个内置预览功能非常的好用，但是随着使用的增多，对应的缓存文件也会不断的增多，所以定期清理，也可以让磁盘空间得到很好的释放。
具体操作：
1.开启Terminal
2.输入删除命令：
sudo rm -rf /private/var/folders/
### 五．删除临时文件
可以尝试在Finder下，按Command＋Option＋G，然后前往：/private/var/tmp/ 这个文件夹，这里面是用来存储临时文件的。
对于这个路径下的内容，其实，系统在重启的时候一般都会做清理，但是鉴于现在很多用户都不怎么关机，导致这个里面的内容越来越多，所以有时候，我们还是有必要手动清理一下的。
具体操作
1.开启Terminal
2.定位到对应路径，请输入：
cd /private/var/tmp/
3.清理，请输入：
rm -rf TM*
### 六．清除缓存文件
具体操作：
1.开启Terminal
2.定位到对应路径（当前用户的路径），请输入：
cd ~/Library/Caches/
3.清理，请输入：
rm -rf ~/Library/Caches/*
其上只是部分可以清理的mac垃圾，这些操作需要花费一定的时间和精力，而且有一定的难度，最重要的是并不可能完全清理mac。
