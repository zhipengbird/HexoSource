---
title: 引入GCDWebServer后编译出现的问题及其解决办法
date: 2016-06-15 17:22:10
tags:
---
```
Undefined symbols for architecture arm64:

  "_deflate", referenced from:

      -[GCDWebServerGZipEncoder readData:] in GCDWebServerResponse.o

  "_inflate", referenced from:

      -[GCDWebServerGZipDecoder writeData:error:] in GCDWebServerRequest.o

  "_deflateInit2_", referenced from:

      -[GCDWebServerGZipEncoder open:] in GCDWebServerResponse.o

  "_inflateEnd", referenced from:

      -[GCDWebServerGZipDecoder close:] in GCDWebServerRequest.o

  "_inflateInit2_", referenced from:

      -[GCDWebServerGZipDecoder open:] in GCDWebServerRequest.o

  "_deflateEnd", referenced from:

      -[GCDWebServerGZipDecoder open:] in GCDWebServerRequest.o

      -[GCDWebServerGZipEncoder open:] in GCDWebServerResponse.o

      -[GCDWebServerGZipEncoder close] in GCDWebServerResponse.o
```
解决办法：

在工程中添加 libz.dylib
