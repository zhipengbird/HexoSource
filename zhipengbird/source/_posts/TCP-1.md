---
title: TCP 基础入门  
date: 2017-05-16 17:05:23  
categories:   TCP  
tags:  
- TCP 
---


* TCP连接的端点是由一个IP地址和一个端口号来唯一标识的 
* TCP 是一个可靠的协议，也就是说，除非整个网络出现问题，数据将被完好地按原样正确的传送到另一外一端。这个可靠性是通过以下几个规则来实现的：

    * 为了防止数据在传输过程中被损坏，每个信息包都包含一个**校验码**。这个校验码就是一个用来保证信息包在传输过程中没有被更改的代码。当信息包到达目的地的时候，接收方会对比校验码和收到的信息中的数据，如果校验码不对，该信息包被省略。
    
    * 为了防止信息包丢失，TCP会要求接收方每收到一个信息包都反馈一下，如果接收方没有提供反馈，发送方会自动重发一次。由于系统自动处理这个问题，所以程序的开发者根本不用知道问题的出现。TCP会一直试着发送信息包，一直到接收者收到为止，或者它会判断网络连接断了，并在程序中返回一个错误提示。

    * 为了防止信息包重复或顺序列错误，TCP每传送一个信息包都会发送一个序号。接收方会检查这个序号，确保收到该信息包，并把全部信息包按顺序重新合并。同时，如果接收方看到了一个已看过的序号，则该信息包就会被丢弃。

## TCP 与 UDP 选择：
* 用TCP
    * 您需要一个可靠的数据传输，以确保您的数据完整无缺地到达目的地
    * 您的协议需要不止一个请求和服务器的回答
    * 您要发送较多的数据
    * 初始连接出现短暂的延迟是可以容忍的

* 用UDP
    * 您不太关心数据包是否到达或者不太在意信息包到达的顺序是否正确，再或者您可以自己察觉这些问题并自己解决
    * 您的协议只包括基本请求和回答
    * 您需要尽快建立网络会话
    * 只传送很少部分数据。UDP 的限制是一个信息包不超过64KB的数所据，通常只用UDP传送1KB以下的数据

# Socket
* socket 是操作系统中I／O系统的延伸部分，它可以使用进程和机器之间的通信成为可能。

## 建立Socket
* 客户端建立一个socket需要两个步骤：
    * 建立一个实际的socket对象
    * 把它连接到远程服务器上。

* 建立Socket对象时，需要告诉系统两件事：
    * 通信类型
        * 指明用什么协议传输数据 （IPV4,IPV6,IPX／SPX，AFP）
    * 协议家族
        * 定义数据如何被传输

> 通信类型基本上都是AF_INET(IPv4)
  协议家族一般表示：
  1. TCP通信的SOCK_STREAM
  2. UDP通信的SOCK_DGRAM

建立一个实际的socket对象:
`s = socket.socket(socket.AF_INET,socket.SOCK_STREAM)`
连接socket，提供一个tupe（远程主机名／IP地址 , 远程端口）
`s.connect(('www.baidu.com',80))`

>Ps: C语言的connect()函数需要远程机器的IP地址，在Python中socket对象的connect()函数会根据需要利用DNS把域名自动转换为IP地址，但对端口号则不是这样.

## 利用socket通信

利用socket发送和接收数据，python提供了两种方法：socket对象和文件类对象
1. socket对象
    * socket对象提供了操作系统的send(),sendto(),recv(),和recvfrom()调用的接口
    * 使用场景：
        * 读写数据、需要协议可以详细的控制时、使用二进制传送固定大小数据时、数据超时需要特殊处理时，
        * 任何不止需要简单读写时，编写UDP程序时
2. 文件类对象
    * 文件类对象提供了read(),write(),readline()接口
    * 文件类对象一般用于面向线性的协议，因为它能够通过提供的readline()函数自动为您处理大多数的解析。

    > 文件类对象一般只对TCP连接工作表现很好，对UDP连接反而不好。原因如下：  
    1. TCP连接的行为更像是标准的文件，它们保证数据接收的精确性，并且和文件一样是以字节流形式运转  
    2. UDP是一种基于信息包的通信。文件类对象没法操作每个基本的信息包，因而建立、接收、发送UDP信息包的基本机制是不能工作的，并且检查也非常困难



## socket异常
socket模块实际上定义了4种可能出现的异常：
1. 与一般I/O和通信问题有关的socket.error
2. 与查询地址信息有关的socket.gaierror
3. 与其他地址错误有关的socket.herror
4. 与在一个socket上调用settimeout()后，处理超时有关的socket.timeout(需要python2.3或更高版本)

