---
title: URL的组成部分
date: 2018-01-10 17:20:12
tags:
---

# URL的组成部分
URL(统一资源定位符)是URI(通用资源标识)的特定类型，URL通常在因特网上查找现有资源。当Web客户机向服务器发出对资源的请求时，使用URL。
URI 和 URL 的概念由因特网协会和 IETF（因特网工程任务组织）请求评论文档 RFC 2396 统一资源标识（URI）：一般语法定义（http://www.ietf.org/rfc/rfc2396.txt）。简要地说，***URI 是定义为识别资源的任何一个字符串***。**URL 定义为按资源的位置或用户访问它的方式，而不是按资源的名称或其他属性来识别资源的那些 URI。**

HTTP（HTTPS）的 URL 通常由三或四个组成部分组成：
* **规则**
    * 规则识别用于访问因特网上的资源协议
* **主机**
    * 主机名识别拥有资源的主机
* **路径** 
    * 路径识别主机中Web客户机要访问的特定资源
* **查询字符** 
    * 如果使用查询字符串，那么它跟随路径部分，并且提供一串字符串，资源使用这些字符串可以完成某些操作（例如，作为用于搜索的参数或用于处理的数据）。 查询字符串通常是一串名称和值对，例如，q=bluebird。

URL 的规则和主机部分不定义为区分大小写，但是路径和查询字符串是区分大小写的。通常，整个 URL 指定为小写字母。
URL 的组成部分如下所示进行组合和定界：
```
scheme://host:port/path?query
```
* 规则后跟冒号和两个正斜杠。
* 如果指定端口号，那么主机名后面是号码，并用冒号分隔。
* 路径名以单正斜杠开始。
* 如果指定查询字符串，那么在它的前面加个问号。

图 1. HTTP URL 语法
阅读语法图跳过直观语法图
                            .-:80-----.                      
>>-http://--+-host name--+--+---------+--/--path component------>
            '-IP address-'  '-:--port-'                      

>--+-----------------+-----------------------------------------><
   '-?--query string-'

URL 的后面可以跟片段标识。URL 与片段标识之间使用的分隔符是字符 #。**片段标识用于使 Web 浏览器指向它刚检索的项中的引用或函数**。 例如，如果 URL 标识 HTML 页面，那么可使用片段标识，以子节的标识来指示页面中的子节。对于这种情况，Web 浏览器通常向用户显示页面， 以使用户可以看到子节。根据项的介质类型以及为该介质类型的片段标识所定义含义的不同，Web 浏览器为片段标识所采取的操作也会不同
```
https://item.jd.com/11818781778.html#crumb-wrap
```

# OC中如何获取URL的中各个组成部分

为获取URL中的各个组成部分，Foundation框架为我们提供了一个`NSURLComponents`类，通过该类，可以获取到URL中和各个组成部分

```objc
    // NSURLComponents 相关字段
    @property (nullable, copy) NSString *scheme; // 规则， Attempting to set the scheme with an invalid scheme string will cause an exception.
    @property (nullable, copy) NSString *user;//用户
    @property (nullable, copy) NSString *password;//密码
    @property (nullable, copy) NSString *host;//主机
    @property (nullable, copy) NSNumber *port; // 端口 Attempting to set a negative port number will cause an exception.
    @property (nullable, copy) NSString *path;//路径
    @property (nullable, copy) NSString *query;//查询字符
    @property (nullable, copy) NSString *fragment;//片段标识
    @property (nullable, copy) NSArray<NSURLQueryItem *> *queryItems; //查询字段数组
```
示例
```objc
    NSString * urlString = @"http://54.223.156.84:9120/relayserver/spring/commands/command?commandType=cmd_type_ad&commandName=&commandStatus=&code=10001&id=test_snapshow_ios_first_test&page=&restrict=false";
    urlString = @"https://item.jd.com/11818781778.html#crumb-wrap";
    // 创建组件对象
    NSURLComponents *components = [NSURLComponents componentsWithString:urlString];
    
    //规则
    NSString * scheme = components.scheme;
    // 路径
    NSString * path = components.path;
    //端口
    NSString * port = components.port;
    //查询字符串
    NSString * query  = components.query;
    //片段标识
    NSString * fragment = components.fragment;
    //获取查询参数
    NSMutableDictionary *paramsDic = [NSMutableDictionary dictionary];
    for (NSURLQueryItem *item in components.queryItems) {
        [paramsDic setValue:item.value forKey:item.name];
    }
```