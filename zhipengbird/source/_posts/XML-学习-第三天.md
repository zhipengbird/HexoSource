---
title: XML 学习 第三天
date: 2016-10-01 23:44:59
categories: XML
tags:
- XML
---
今天要学习的XML的验证，验证器及流览器 ,初略DOM
<!-- more -->
# XML 验证

>拥有正确语法的 XML 被称为“形式良好”的 XML.  
  通过 DTD 验证的 XML 是“合法”的 XML.

## 形式良好的 XML 文档
  “形式良好”或“结构良好”的 XML 文档拥有正确的语法。
  “形式良好”（Well Formed）的 XML 文档会遵守前面介绍过的 XML 语法规则：
    * XML 文档必须有根元素
    * XML 文档必须有关闭标签
    * XML 标签对大小写敏感
    * XML 元素必须被正确的嵌套
    * XML 属性必须加引号
```xml
<?xml version="1.0" encoding="UTF-8"?>
<note>
  <date>2016-10-1</date>
  <weather>sunny </weather>
  <title>Learning xml</title>
  <body>xml is very funny and easy</body>
</note>
```
## 验证XML文档
合法的 XML 文档是“形式良好”的 XML 文档，同样遵守文档类型定义 (DTD) 的语法规则：
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE note SYSTEM "note.dtd">
<note>
  <date>2016-10-1</date>
  <weather>sunny </weather>
  <title>Learning xml</title>
  <body>xml is very funny and easy</body>
</note>
```
DOCTYPE 声明是对外部 DTD 文件的引用。下面的段落展示了这个文件的内容
* XML DTD
DTD 的作用是定义 XML 文档的结构。它使用一系列合法的元素来定义文档结构：
```xml
<!DOCTYPE note[
<!ELEMENT note(date,weather,title,body)>
<!ELEMENT date (#PCDATA)>
<!ELEMENT weather (#PCDATA)>
<!ELEMENT title (#PCDATA)>
<!ELEMENT body (#PCDATA)>
]>
```
[DTD教程](http://www.w3school.com.cn/dtd/index.asp)

* XML Schema
W3C 支持一种基于 XML 的 DTD 代替者，它名为 XML Schema：
```xml
<xs:element name="note">
<xs:complexType>
  <xs:sequence>
    <xs:element name="date"      type="xs:string"/>
    <xs:element name="weather"    type="xs:string"/>
    <xs:element name="title" type="xs:string"/>
    <xs:element name="body"    type="xs:string"/>
  </xs:sequence>
</xs:complexType>

</xs:element>

```
[XML Schema 教程](http://www.w3school.com.cn/schema/index.asp)

# XML 验证器
## XML 错误会终止您的程序
XML 文档中的错误会终止你的 XML 程序。
W3C 的 XML 规范声明：如果 XML 文档存在错误，那么程序就不应当继续处理这个文档。理由是，XML 软件应当轻巧，快速，具有良好的兼容性。

如果使用 HTML，创建包含大量错误的文档是有可能的（比如你忘记了结束标签）。其中一个主要的原因是 HTML 浏览器相当臃肿，兼容性也很差，并且它们有自己的方式来确定当发现错误时文档应该显示为什么样子。
注释：只有在 Internet Explorer 中，可以根据 DTD 来验证 XML。Firefox, Mozilla, Netscape 以及 Opera 做不到这一点。

# XML 浏览器支持
  几乎所有的主流浏览器均支持 XML 和 XSLT。


# DOM

DOM （Document Object Model，文档对象模型）定义了访问和操作文档的标准方法。

## XML DOM
XML DOM (XML Document Object Model) 定义了访问和操作 XML 文档的标准方法。
DOM 把 XML 文档作为树结构来查看。能够通过 DOM 树来访问所有元素。可以修改或删除它们的内容，并创建新的元素。元素，它们的文本，以及它们的属性，都被认为是节点。
[XML DOM 教程](http://www.w3school.com.cn/xmldom/index.asp)

## HTML DOM
HTML DOM (HTML Document Object Model) 定义了访问和操作 HTML 文档的标准方法。
[ HTML DOM 教程](http://www.w3school.com.cn/htmldom/index.asp)
