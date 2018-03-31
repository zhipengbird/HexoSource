---
title: python-email
date: 2017-05-05 17:10:08
categories: python
tags:
- email
---

# SMTP发送邮件

SMTP（Simple Mail Transfer Protocol）即简单邮件传输协议,它是一组用于由源地址到目的地址传送邮件的规则，由它来控制信件的中转方式。
邮件传送代理 (Mail Transfer Agent，MTA) 程序使用SMTP协议来发送电邮到接收者的邮件服务器。SMTP协议只能用来发送邮件，不能用来接收邮件。
大多数的邮件发送服务器 (Outgoing Mail Server) 都是使用SMTP协议。SMTP协议的默认TCP端口号是25。
SMTP协议的一个重要特点是它能够接力传送邮件。它工作在两种情况下：一是电子邮件从客户机传输到服务器；二是从某一个服务器传输到另一个服务器。

# POP3 (Post Office Protocol) & IMAP (Internet Message Access Protocol)
POP协议和IMAP协议是用于邮件接收的最常见的两种协议。几乎所有的邮件客户端和服务器都支持这两种协议。 

POP3协议为用户提供了一种简单、标准的方式来访问邮箱和获取电邮。使用POP3协议的电邮客户端通常的工作过程是：连接服务器、获取所有信息并保存在用户主机、从服务器删除这些消息然后断开连接。POP3协议的默认TCP端口号是110。

IMAP协议也提供了方便的邮件下载服务，让用户能进行离线阅读。使用IMAP协议的电邮客户端通常把信息保留在服务器上直到用户显式删除。这 种特性使得多个客户端可以同时管理一个邮箱。IMAP协议提供了摘要浏览功能，可以让用户在阅读完所有的邮件到达时间、主题、发件人、大小等信息后再决定 是否下载。IMAP协议的默认TCP端口号是143。  

# 邮件格式 (RFC 2822)
每封邮件都有两个部分：邮件头和邮件体，两者使用一个空行分隔。  
邮件头每个字段 (Field) 包括两部分：字段名和字段值，两者使用冒号分隔。有两个字段需要注意：From和Sender字段。  
    * From字段指明的是邮件的作者，  
    * Sender字段指 明的是邮件的发送者。  
如果From字段包含多于一个的作者，必须指定Sender字段；如果From字段只有一个作者并且作者和发送者相同，那么不应该再 使用Sender字段，否则From字段和Sender字段应该同时使用。
邮件体包含邮件的内容，它的类型由邮件头的Content-Type字段指明。RFC 2822定义的邮件格式中，邮件体只是单纯的ASCII编码的字符序列。

# MIME (Multipurpose Internet Mail Extensions) (RFC 1341)
　　MIME扩展邮件的格式，用以支持非ASCII编码的文本、非文本附件以及包含多个部分 (multi-part) 的邮件体等。


# 邮件头
用python发的整个邮件，每个元素都以/r/n结束（当然如果内容里有/r/n如何就不太清楚了）。其邮件内容都是被一个大的boundary包起来的，Content-Type为multipart/mixed。
header部分，大致有MIME-Version、From、To、Subject等，看情况而定（事实上，我观察注意到，很多邮件服务器在处理邮件的时候，都会自动会加上些邮件头，如Received等）
* From表示邮件来源，格式为：显示名 <邮件地址>，记得两个项目之间应使用空格隔开。其中显示名部分可以允许为中文，但如果是中文，就要注意编码的问题
* To里如果只有一个地址的话，就和From里的格式是完全一样的。如果有多个，那么每个之间用逗号及换行（,/r/n)分割就可以了。一般我们在python代码里可以只用逗号分隔就行了，底层库会自动加上/r/n的。
* Subject的内容，其实也和From里的“显示名”的编码方式一样的
* 邮件的正文和附件是在一起编码的，只是他们的Content-Type有区别罢了。比如正文的type一般是text/plain或text/html等，而附件的type一般是：application/octet-stream。但type不同，对应的内容头也会略有不同。

```python
from email.header import  Header
frm = Header('平华','utf-8')
frm.append('<yuanph@ushareit.com>','ascii')
print frm.encode()
# =?utf-8?b?5bmz5Y2O?= <yuanph@ushareit.com>
import  base64
base64.b64decode('5bmz5Y2O','utf-8')
# '\xe5\xb9\xb3\xe5\x8d\x8e'
```
解析：
=?utf-8?b?5bmz5Y2O?= <yuanph@ushareit.com> 这个内容里本身就有编码信息，因此反过来解析也很简单。因为=?、?、?=属于分隔符，用这三个分隔符简单的split后 。re.split(r'''=\?|\?=|\?''','''=?utf-8?b?5bmz5Y2O?=''')。  可以得到['', 'utf-8', 'b', '5bmz5Y2O', ''] ，根据python的源码显示，utf-8表示数据编码，b表示base64。那么对内容'5bmz5Y2O'进行base64.b64decode后，就可得到utf-8编码的“平华”二个字。

# Content-Type
**表明传递的信息类型**
Content-Type表明信息类型，缺省值为" text/plain"。它包含了主要类型（primary type）和次要类型（subtype）两个部分，两者之间用"/"分割。主要类型有9种，分别是application、audio、example、image、message、model、multipart、text、video。
每一种主要类型下面又有许多种次要类型，常见的有：
```html
text/plain：纯文本，文件扩展名.txt
text/html：HTML文本，文件扩展名.htm和.html
image/jpeg：jpeg格式的图片，文件扩展名.jpg
image/gif：GIF格式的图片，文件扩展名.gif
audio/x-wave：WAVE格式的音频，文件扩展名.wav
audio/mpeg：MP3格式的音频，文件扩展名.mp3
video/mpeg：MPEG格式的视频，文件扩展名.mpg
application/zip：PK-ZIP格式的压缩文件，文件扩展名.zip
```
"Content-Type: multipart/alternative;"表明这封信的内容，是纯文本和HTML文本的混合。另两个可能的值是`multipart/mixed`和`multipart/related`，分别表示`信件内容中有二进制内容`和`信件带有附件`









参考链接：
[MIME笔记](http://www.ruanyifeng.com/blog/2008/06/mime.html)