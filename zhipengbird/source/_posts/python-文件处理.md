---
title: python 文件处理
date: 2016-09-13 11:08:36
tags:
- python
---

# 文件的打开
1. open()语法
  函数参数
  ```
  open(file, mode='r', buffering=None, encoding=None, errors=None, newline=None, closefd=True)
  ```
  参数解析：
  `file` :文件位置，需要加引号
  `mode`:文件打开模式，
  `buffering`:其可取值有0，1，>1三个，0代表buffer关闭（只适用于二进制模式），1代表line buffer（只适用于文本模式），>1表示初始化的buffer大小；
  `encoding`:表示的是返回的数据采用何种编码，一般采用utf8或者gbk；
  `errors`:其取值一般有strict，ignore，当取strict的时候，字符编码出现问题的时候，会报错，当取ignore的时候，编码出现问题，程序会忽略而过，继续执行下面的程序。
  `newline`:可以取的值有 None, \n, \r, ”, ‘\r\n'，用于区分换行符，但是这个参数只对文本模式有效；
  `closefd`:其取值，是与传入的文件参数有关，默认情况下为True，传入的file参数为文件的文件名，取值为False的时候，file只能是文件描述符，什么是文件描述符，就是一个非负整数，在Unix内核的系统中，打开一个文件，便会返回一个文件描述符。

<!-- more -->

参数mode的取值：

| Character    | Meaning    |
| :------------- | :------------- |
|‘r'|	open for reading (default)|
|‘w'|	open for writing, truncating the file first|
|‘a'|	open for writing, appending to the end of the file if it exists|
|‘b'|	binary mode|
|‘t'|	text mode (default)|
|‘+'|	open a disk file for updating (reading and writing)|
|‘U'|	universal newline mode (for backwards compatibility; should not be used in new code)|

>r、w、a为打开文件的基本模式，对应着只读、只写、追加模式；
b、t、+、U这四个字符，与以上的文件打开模式组合使用，二进制模式，文本模式，读写模式、通用换行符，根据实际情况组合使用

>常见的mode取值组合
>r或rt 默认模式，文本模式读
rb   二进制文件

>w或wt 文本模式写，打开前文件存储被清空
wb  二进制写，文件存储同样被清空

>a  追加模式，只能写在文件末尾
a+ 可读写模式，写只能写在文件末尾

>w+ 可读写，与a+的区别是要清空文件内容
r+ 可读写，与a+的区别是可以写到文件任何位置

# 文件的读取
1.使用 read() 方法一次性读取整个文件。
  ```python
  with open('text.txt')as file:
    file.read()
  ```
  >read(size) 有一个可选的参数 size，用于指定字符串长度。如果没有指定 size 或者指定为负数，就会读取并返回整个文件。当文件大小为当前机器内存两倍时，就会产生问题。反之，会尽可能按比较大的 size 读取和返回数据。

2. readline() 能帮助你每次读取文件的一行
```python
with open('txt.txt') as file:
  file.readline()
```
3. 使用 readlines() 方法读取所有行到一个列表中
```python
with open('txt.txt') as file:
  file.readlines()

```
4. 使用write()方法写入数据
```python
with open('txt.txt','w') as writer:
  writer.writer("hello ,welcome the python world")
```
5. 使用writelines()写入多行
  ```python
  with open('txt.txt','w+') as writer:
    writer.writelines(['hello,python','hello,c++','hello objective-c']) #这样不会换行，在一行中写入

  with open('txt.txt','w+') as write:
    writer.writelines('\n'.join( ['hello,python', 'hello,c++', 'hello objective-c'] ))  #这样可以达到换行效果

  with open('txt.txt','w+') as writer:
    for line in ['hello,python', 'hello,c++', 'hello objective-c']:
      writer.write(line)
      writer.write('\n')  #这样也可以达到换行的效果

  ```
6. 使用flush()在写入完后，更新缓存
  ```python
  with open('txt.txt','w') as writer:
    writer.writer("hello ,welcome the python world")
    writer.flush() #清理缓存
  ```

7. 下载网络图片，并写入文件：
```python
from urllib.request import urlopen
url = "https://img3.doubanio.com/lpic/s3885296.jpg"
# 'http://www.to404.com/list/uploads/201609/thumb_a0d69ac3b7289b43a3986b423305d17d.jpg'
filename = "1.jpg"
imagedata = urlopen ( url ).read ( )
with open ( filename, 'wb' ) as filewriter:
    filewriter.write ( imagedata )
    filewriter.flush ( )

```
# 文件的关闭

 1. 使用close()方法关闭文件
 ```python
 file = open('txt.txt','w')
 file.write('txt......')
 file.close()
 ```
 2. 使用 with 语句处理文件对象，它会文件用完后会自动关闭
  ```python
  with open('txt.txt','w') as writer:
    writer.writer("hello ,welcome the python world")
  ```
