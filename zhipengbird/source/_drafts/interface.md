---
title: gamelist
tags:
---

*******************************************************************************
修订记录
-------------------------------------------------------------------------------
版本    日期            类型    作者    描述
-------------------------------------------------------------------------------


*******************************************************************************
目录
1.摘要
2.通用约定
3.定义
4.项目的定义
5.接口说明

*******************************************************************************
1.摘要
  本文档包含以下内容：
    a) 客户端，WEB端通过服务器认证协议
    b) 客户端，WEB端直连认证协议
    c) WEB端获取客户端媒体文件列表协议
    d) WEB端下载客户端文件协议
    e) WEB端上传文件到客户端协议

*******************************************************************************
2.通用约定
    2.1 表述
         XXX   表示数字
        "XXX"  表示字符串

    2.2 字符编码
        所有最终用户可见的字符串，统一采用UTF8格式编码。无论在URL或json字段中。

    2.3 数据类型
        文件大小：非负整数(Int64)，单位为字节
        时间: 非负整数(Int64)

    2.4 对必选,可选字段的处理原则
       以下适用于所有协议实现方

       对于必选字段：
           对于生成方，一定不能省略该字段、类型必须正确、值必须有意义（不能有null、空字符串、负数的文件长度、为0的日期等等，除非有特别指明）；
           对于解析方，如果该字段不存在、值不合法，可以解析失败，忽略该条目，甚至整个消息。
       对于可选字段：
           对生成方，如果字段没有合法的值，忽略该字段，不要传null、空字符串、为0的日期等等。
           解析一方，如果遇到可选字段值不正确，忽略该字段，不要出错。

*******************************************************************************
3.定义
    项目: 传输的对象，包括应用/图片/音乐/视频/文件。
    缩略图：item的缩略图，同一item可以有多种预定义尺寸的缩略图。
    sid: WEB端和客户端每次逻辑连接的id
    status: 返回结果， 0：成功； 1：无权限； 2：失败
    媒体类型: photo: 图片； music：音乐； video：视频； app：应用； file：文件
    子媒体类型：图片(1: 相机； 2：图库)； 音乐(1: 所有； 2：艺术家; 3: 专辑; 4: 文件夹)；

*******************************************************************************
4. 项目定义
  cmmon
  {
      "type"          : "xxx" ,                       必选          // 项目类型
      "id"            : "xxx" ,                       必选          // 项目id
      "ver"           : "xxx" ,                       必须          // 项目的版本号

      "name"          : "xxx" ,                       必选          // 项目的显示名称
      "filepath"      : "xxx" ,                       必选          // 项目文件路径，联系人是""
      "filesize"      : xxx ,                         必选          // 项目文件的size
      "datemodified"  : xxx ,                         必选          // 文件的最后修改时间
      "has_thumbnail" : boolean ,                     可选          // 是否有头像，缺省是false
  }
  photo:
  {
      "albumid"       : xxx ,                         可选          // 图片相册的id
      "albumname"     : "xxx" ,                       可选          // 图片的相册的名称
      "orientation"   : xxx,                          可选          // 旋转角度
  }
  music:
  {
      "duration"      : xxx ,                         必选          // 音乐的播放时长
      "artist"        : "xxx" ,                       可选          // 音乐的艺术家
      "format"        : "xxx" ,                       必选          // 音乐的格式（mp3....）
  }
  video:
  {
      "duration"      : xxx ,                         必选          // 视频的播放时长
  }
  app:
  {
      "packagename"   : "xxx" ,                       必选          // 应用程序的包名
      "versioncode"   : xxx ,                         必选          // 应用程序的版本号
      "versionname"   : "xxx" ,                       必选          // 应用的版本名称
  }
  file:
  {
  }
  folder:
  {
    "filepath"  : "xxx",
    "isroot"  : boolean,
    "isvolume"  : boolean,
    "isloaded"  : boolean,
    "items"   : [file, file...]
    "containers": [folder, folder...]
  }
*******************************************************************************
5. 接口说明
  5.1 浏览器访问URL
      WebSocket GET: http://web.ushareit.com/wsconnect

      消息1：连上就发，而且只发一次。
      json字符串：{type:"init",cid":"XXX"}

      根据cid生成二维码，二维码包含的内容格式：
      json字符串：{"cid":"XXX"}


      二维码内容：json字符串：{"cid":"XXX"}

      手机扫码后服务器下发
      消息2：
      {

          "type":"connect"
          "os_type":"XXX",
          "sip":"xxx.xxx.xxx:xxxx",
          "ssid":"XXX",
      }

  5.2 手机客户端扫码后访问URL
      HTTP POST：http://web.ushareit.com/webshare
      参数：
           名称       类型   描述
           cid       ”xxx“  扫描二维码获取的浏览器唯一标志
           sip       ”xxx“  手机热点IP，形如xxx.xxx.xxx:xxxx
           ssid      ”xxx“  手机端网络名称,非必须参数
           os_type   ”xxx“  手机类型，安卓值为：Android；苹果手机值为：iOS

      返回值：
        {
          status:'0',
          data:{
            ua:{
              b: 'chrome'/'safari' .... //浏览器类型
              os: 'max os' 操作类型
              ip:""
            }
          }
        }

  5.3 WEB和客户端直连
    5.3.1 请求授权： host/ws_auth?cid="xxx"&name="xxx"&os=xxx&dev=xxx
      参数：
        cid:  web的唯一标示(必选)
        name:   浏览器昵称, 没有用ip替代 (可选)
        os:   操作系统(1:android, 2:ios, 3:mac, 4:win) (可选)
        dev:    设备类型(1: 手机， 2：PAD, 3：PC) (可选)

      结果：
      {
        status: 0;
        sid: "xxx"; // 浏览器和客户端本次连接的session id
        os: "xxx";  // "android/ios"
      }

    5.3.2 心跳保持，次/10s, 超过20s后结束session： host/ws_beat?sid="xxx"
      参数：
        sid: session id

    5.3.3 WEB端退出： host/ws_quit?sid="xxx"
      参数：
        sid: session id

    5.3.4 请求设备信息： host/ws_device?req="xxx"&sid="xxx"
      参数：
        req: 请求类型，"info", "storage"
        sid: session id

      结果：
        req=="info"
        {
          status: 0,
          info: {
            "id"    : "xxx",                  //device id
            "type"    : xxx,                      //设备类型(1: 手机， 2：PAD, 3：PC)
            "name"    : "DENNY"                     //手机名
            "model"   :""      //型号  HUAEI-P8   iPhone-6s
            "os"  : "xxx",                  //系统类型和版本
            "channel" : "xxx",                        //客户端的渠道
            "rom"   : "29977079808;59639656448",  //存储空间使用量和总量
            "sdcard"  : "0;0",                  //内存卡，使用量和总量
            "language"  : "zh-CN",                  //手机语言
            "basePath":''//茄子的默认存储路径
          }
        }

        req=="storage"
        {
          status: 0,
          data:[{
              "name"  : "xxx",            //存储器名称
              "path"  : "\/storage\/emulated\/0", //路径
              "type"  : xxx,            //存储器类型， 0：内部存储； 1：外部存储

            },
            ...]
        }

      5.3.5 请求媒体文件的数量: host/ws_media_count?sid="xxx"
          参数:
          sid: "xxx"  // 此次连接的session id
          结果：
          {
            status: 0,
            info: {
              "photo"     : xxx, //图片
              "video"     : xxx, //视频
              "music"     : xxx, //音乐
              "doc"       : xxx, //文档
              "app"       : xxx, //软件
            }
          }

      5.3.6 获取相册，专辑信息: host/ws_media_albums?type=xxx&subtype=xxx&sid="xxx"
          参数：
            type:  媒体类型；
            subtype: 子分类；   // 参数不存在为获取媒体类型的所有items
            sid: "xxx"      // 此次连接的session id

          结果：
            {
              status: 0,
              albums: [{
                "id"      :"xxxx"   //唯一标识一个相册的id
                "name"      : "xxx",  //名称
                "count"     : 77    //包含的图片数量
                "path"      : "" //上传时候需要的路径
                "data"      : "2016-03-11" (可选 ？？？)
                "thumb"     : "xxx",  //封面缩略图地址
              },{}]
            }

      5.3.7 获取相册/专辑下的item列表: host/ws_media_list?type=xxx&album_id="xxx"&start=xxx&count=xxx&sid="xxx"
          参数：
            type:  媒体类型；
            album_id: 相册/专辑id （可选，没有代表所有）  // type==file时， album_id="zip/ebook/doc"
            start: 起始位置 (0开始)
            count: item个数             // count = -1或没有这个参数，表示获取当前相册/专辑下得所有item
            sid: "xxx"                // 此次连接的session id

          结果：
            {
              status:0,
              list: [item, item]
            }

      5.3.8 获取目录列表： host/ws_folder/path?sid="xxx"
          参数：
            sid: "xxx"                // 此次连接的session id
          结果：
            {
              status: 0
              folder: folder
            }

    5.3.9 获取缩略图 host/ws_thumbnail?type=&id=&width=&height=&sid=
      参数：
              type:  媒体类型；
              id: 媒体文件id,如果是专辑缩略图， 就是thumb的值
              width: 缩略图宽
              height: 缩略图高
              sid: "xxx"
      5.3.10 下载文件： host/ws_download/path?cs="xxx"&sid="xxx";
          参数：
            cs: "xxx"; //验证码
            sid: "xxx"; //此次连接的session id

          cs生成算法：
            1. sid + path 做MD5
            2. MD5每4个字节一组
            3. 每组中每个字节相加后取256的余数
            4. 所有的余数生成一个字符串

      //5.3.11 获取静态资源： host/ws_res/path

      5.3.12 上传文件： post: host/ws_upload?sid="xxx"
          参数：
            part1: type: "xxx"; //mimeType
            part2: size: xxx;

            part3: path: //如果是文件夹/指定的图库带，其余不带此part
            part4: file:

          结果：
            {
              status: 0;
              type:
              item: {
              file item
              }
            }
