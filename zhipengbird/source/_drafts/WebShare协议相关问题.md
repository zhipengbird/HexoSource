---
title: WebShare协议相关问题 －－－iOS
tags:
---
###############################################################
 文件上传的主要类型：相片，视频，音乐，文件
1. 上传相片：
    如果有权限导入系统相册 ，导入系统相册，并返回对应的路径。  返回文件类型：photo
                         导入失败，以文件形存放到相册文件夹中。返回文件类型：file
    如果没有权限写入，以文件形式存入到相册文件夹中 返回文件类型：file

2. 上传视频：
    以文件形式存放到视频文件夹中，不导入到系统 ，可由用户手动导入（有待确认）。返回文件类型：file  

3. 上传音乐：
  以文件形式存在音乐文件夹中，并导入音乐列表里，返回文件类型：music

4. 上传文件：
  根据文件类型，以文件形式存放到对应的文件夹中，返回文件类型：file/music/photo
  在多层子文夹中上文件文件该如何保存？
  该如何区别出是在首页上传还是在“文件”中上传？

5. 上传文件夹
  创建新的文件夹，并将文件存到该文夹里，所有文件以文件形式处理

##############################################################
文件类型的获取：
  从contentpye中获取，相片以“img/”开头，音乐以“audio/”开头，视频以“video/”开头，其他都将视为文件


###########################################
  视频资源包括：系统视频和"视频"文夹里的视频

文件的查看：
  点击相册文件可以预览该传输过程的所有图片
  点击音乐，生成音乐列表，可以播放当前传输过程中的所有音乐
  点击视频，播放该视频

### 约定
  在传输中使用的根路径为空“ ”；
  在传输中相册使用的根路径为：'asset_library/0|1/ablumname'   0. 系统相册 1. 用户相册
  上传文件的路径：文件夹路径，不带文件名
