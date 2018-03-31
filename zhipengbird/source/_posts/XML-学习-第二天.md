---
title: 'XML 学习 第二天 '
date: 2016-10-01 00:30:33
categories: XML
tags:
- XML
---

今天我们主要学习XML的语法规,元素，属性
<!-- more -->
# XML语法规则

* 所有XML元素都必须有关闭标签  
&emsp;在hmtl中经常会看到没有关闭标签的元素
```HTML
<p>This is a paragraph
<p>This is a another paragraph
```
&emsp;在XML中，省略关闭标签是非法的。所有元素都必须有关闭标签
```XML
<p>This is a paragraph</p>
<p>This is a another paragraph</p>
```
* XML标签对大小写敏感  
&emsp;XML元素使用XML标签进行定义。
XML 标签对大小写敏感。在 XML 中，标签 <Letter> 与标签 <letter> 是不同的。
```XML
<Message>This is a error</message>
<message>This is right</message>
```
>注释：打开标签和关闭标签通常被称为开始标签和结束标签。不论您喜欢哪种术语，它们的概念都是相同的。

* XML 必须正确地嵌套  
&emsp;在 HTML 中，常会看到没有正确嵌套的元素：
```html
<b><i>This text is bold and italic</b></i>
```
&emsp;在 XML 中，所有元素都必须彼此正确地嵌套：
```XML
<b><i>This text is bold and italic</i></b>
```
&emsp;在上例中，正确嵌套的意思是：由于 <i> 元素是在 <b> 元素内打开的，那么它必须在 <b> 元素内关闭。


* XML 文档必须有根元素  
&emsp;XML 文档必须有一个元素是所有其他元素的父元素。该元素称为根元素。
```xml
<root>
  <child>
    <subchild>.....</subchild>
  </child>
</root>
```
* XML 的属性值须加引号  
&emsp;与 HTML 类似，XML 也可拥有属性（名称/值的对）。
在 XML 中，XML 的属性值须加引号。请研究下面的两个 XML 文档。第一个是错误的，第二个是正确的：
```xml
<note date=08/08/2008>
<to>George</to>
<from>John</from>
</note>
```
```xml
<note date="08/08/2008">
<to>George</to>
<from>John</from>
</note>
```
在第一个文档中的错误是，note 元素中的 date 属性没有加引号。

* 实体引用  
在 XML 中，一些字符拥有特殊的意义。
在 XML 中，有 5 个预定义的实体引用：

|实体引用  |  字符    |
| :------------- | :------------- |
| &lt;      | <       |
|&glt;|>|
|&amp;|&|
|&apos|'|
|&quot|"|

>注释：在 XML 中，只有字符 "<" 和 "&" 确实是非法的。大于号是合法的，但是用实体引用来代替它是一个好习惯。


* XML 中的注释  
&emsp;在 XML 中编写注释的语法与 HTML 的语法很相似：
```XML
<!-- This is a comment -->
```
* 在 XML 中，空格会被保留  
&emsp;HTML 会把多个连续的空格字符裁减（合并）为一个：
在 XML 中，文档中的空格不会被删节。

* XML 以 LF 存储换行  
&emsp;在 Windows 应用程序中，换行通常以一对字符来存储：回车符 (CR) 和换行符 (LF)。这对字符与打字机设置新行的动作有相似之处。在 Unix 应用程序中，新行以 LF 字符存储。而 Macintosh 应用程序使用 CR 来存储新行。


# XML 元素
## 什么是 XML 元素？
&emsp;XML 元素指的是从（且包括）开始标签直到（且包括）结束标签的部分。
元素可包含其他元素、文本或者两者的混合物。元素也可以拥有属性。
```xml
<?xml version="1.0" encoding="UTF-8"?>
<bookstore>
  <book category='child'>
    <title>Harry Potter</title>
    <author> J K. Rowling</author>
    <year>2005</year>
    <price>29.9</price>
  </book>
  <book category='WEB'>
    <title>Learning xml</title>
    <author> Erik T.ray </author>
    <year>2005</year>
    <price>29.9</price>
  </book>
</bookstore>
```
## XML 命名规则
XML 元素必须遵循以下命名规则：

    * 名称可以含字母、数字以及其他的字符
    * 名称不能以数字或者标点符号开始
    * 名称不能以字符 “xml”（或者 XML、Xml）开始
    * 名称不能包含空格

可使用任何名称，没有保留的字词。


## 最佳命名习惯

* 使名称具有描述性。使用下划线的名称也很不错。

* 名称应当比较简短，比如：<book_title>，而不是：<the_title_of_the_book>。

* 避免 "-" 字符。如果您按照这样的方式进行命名："first-name"，一些软件会认为你需要提取第一个单词。

* 避免 "." 字符。如果您按照这样的方式进行命名："first.name"，一些软件会认为 "name" 是对象 "first" 的属性。

* 避免 ":" 字符。冒号会被转换为命名空间来使用（稍后介绍）。

&emsp;XML 文档经常有一个对应的数据库，其中的字段会对应 XML 文档中的元素。有一个实用的经验，即使用数据库的名称规则来命名 XML 文档中的元素。

&emsp;非英语的字母比如 éòá 也是合法的 XML 元素名，不过需要留意当软件开发商不支持这些字符时可能出现的问题。

## XML 元素是可扩展的

  XML 元素是可扩展，以携带更多的信息。
  ```xml
<?xml version="1.0" encoding="UTF-8"?>
<note>
  <to>George</to>
  <from>John</from>
  <body>Don't forget the meeting!</body>
</note>
  ```
  增加字段
  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <note>
      <date>2008-08-08</date>
      <to>George</to>
      <from>John</from>
      <heading>Reminder</heading>
      <body>Don't forget the meeting!</body>
</note>
  ```

# XML 属性

> XML 元素可以在开始标签中包含属性，类似 HTML。
属性 (Attribute) 提供关于元素的额外（附加）信息。

## XML属性
 从 HTML，你会回忆起这个：`<img src="computer.gif">`。`"src"` 属性提供有关 `<img> `元素的额外信息。
 在 HTML 中（以及在 XML 中），属性提供有关元素的额外信息：
 ```HTML
 <img src="computer.png">
 <a href="index.html">
 ```
 属性通常提供不属于数据组成部分的信息。在下面的例子中，文件类型与数据无关，但是对需要处理这个元素的软件来说却很重要：
 ```XML
 <?xml version="1.0" encoding="UTF-8"?>
 <file type='gif'>computer.gif</file>
 ```
 * XML 属性必须加引号
 属性值必须被引号包围，不过单引号和双引号均可使用。比如一个人的性别，person 标签可以这样写：
 ```xml
 <?xml version="1.0" encoding="UTF-8"?>
 <person sex='man'>tom</person>
 <!-- 下面是一样的效果 -->
 <!-- <person sex="man">tom</person> -->
 ```
 **注释：如果属性值本身包含双引号，那么有必要使用单引号包围它，就像这个例子：**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<gangster name='George "Shotgun" Ziegler'></gangster>
<!-- 同样的效果 -->
<gangster name="George &quot;Shotgun&quot; Ziegler">
```
* XML 元素 vs. 属性
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!-- 属性 -->
<person sex='boy'>
  <firstname> Alan</firstname>
  <lastname>Yuan</lastname>
</person>
<!-- 无素 -->
<person>
  <sex>boy</sex>
  <firstname>Alan</firstname>
  <lastname>Yuan</lastname>
</person>
```
>没有什么规矩可以告诉我们什么时候该使用属性，而什么时候该使用子元素。我的经验是在 HTML 中，属性用起来很便利，但是在 XML 中，您应该尽量避免使用属性。如果信息感觉起来很像数据，那么请使用子元素吧。

* 避免 XML 属性？

因使用属性而引起的一些问题：
    * 属性无法包含多重的值（元素可以）
    * 属性无法描述树结构（元素可以）
    * 属性不易扩展（为未来的变化）
    * 属性难以阅读和维护
请尽量使用元素来描述数据。而仅仅使用属性来提供与数据无关的信息。
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!-- 错误的使用 -->
<note day="08" month="08" year="2008"
to="George" from="John" heading="Reminder"
body="Don't forget the meeting!">
</note>

```
>在此极力向您传递的理念是：元数据（有关数据的数据）应当存储为属性，而数据本身应当存储为元素。
