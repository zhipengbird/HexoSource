---
title: bs4学习
date: 2016-09-30 14:21:59
tags:
---

>Beautiful Soup 是一个可以从HTML或XML文件中提取数据的Python库.它能够通过你喜欢的转换器实现惯用的文档导航,查找,修改文档的方式.Beautiful Soup会帮你节省数小时甚至数天的工作时间.

<!-- more  -->
#bs4 的安装
使用系统软件包管理工具brew/pip来安装
```sh
brew install beautifulsoup4
pip install beautifulsoup4
```
安装解析器
Beautiful Soup支持Python标准库中的HTML解析器,还支持一些第三方的解析器,其中一个是 lxml .根据操作系统不同,可以选择下列方法来安装lxml:

```sh
$ apt-get install Python-lxml

$ easy_install lxml

$ pip install lxml
```

另一个可供选择的解析器是纯Python实现的 html5lib , html5lib的解析方式与浏览器相同,可以选择下列方法来安装html5lib:
```sh

$ apt-get install Python-html5lib

$ easy_install html5lib

$ pip install html5lib
```

| 解析器    | 使用方法     |优势|劣势|
| :------------- | :------------- |:------------- |:------------- |
| Python标准库     | BeautifulSoup(markup, "html.parser")   |  Python的内置标准库    执行速度适中  文档容错能力强|  Python 2.7.3 or 3.2.2)前 的版本中文档容错能力差|
|lxml HTML 解析器 |BeautifulSoup(markup, "lxml") |速度快    文档容错能力强|需要安装C语言库|
|lxml XML 解析器 	|BeautifulSoup(markup, ["lxml-xml"])  BeautifulSoup(markup, "xml")|速度快  唯一支持XML的解析器|需要安装C语言库|
|html5lib|BeautifulSoup(markup, "html5lib") |最好的容错性  以浏览器的方式解析文档  生成HTML5格式的文档|  速度慢  不依赖外部扩展|

**推荐使用lxml作为解析器,因为效率更高. 在Python2.7.3之前的版本和Python3中3.2.2之前的版本,必须安装lxml或html5lib, 因为那些Python版本的标准库中内置的HTML解析方法不够稳定.**

# 使用
将一段文档传入BeautifulSoup 的构造方法,就能得到一个文档的对象, 可以传入一段字符串或一个文件句柄.
```Python
from bs4 import BeautifulSoup
soup = BeautifulSoup(open("index.html"))
soup = BeautifulSoup("<html>data</html>")
```
首先,文档被转换成Unicode,并且HTML的实例都被转换成Unicode编码
然后,Beautiful Soup选择最合适的解析器来解析这段文档,如果手动指定解析器那么Beautiful Soup会选择指定的解析器来解析文档

示例内容：
```Python
html_doc = """
<html><head><title>The Dormouse's story</title></head>
<body>
<p class="title"><b>The Dormouse's story</b></p>

<p class="story">Once upon a time there were three little sisters; and their names were
<a href="http://example.com/elsie" class="sister" id="link1">Elsie</a>,
<a href="http://example.com/lacie" class="sister" id="link2">Lacie</a> and
<a href="http://example.com/tillie" class="sister" id="link3">Tillie</a>;
and they lived at the bottom of a well.</p>

<p class="story">...</p>
"""
```

## 对象的种类
Beautiful Soup将复杂HTML文档转换成一个复杂的树形结构,每个节点都是Python对象,所有对象可以归纳为4种: `Tag` , `NavigableString` , `BeautifulSoup` , `Comment` .

## Tag
**Tag 对象与XML或HTML原生文档中的tag相同:**

```Python
soup =BeautifulSoup('<b class="boldest">Extremely bold</b>',"lxml")
tag = soup.b
print(type(tag)) # <class 'bs4.element.Tag'>
```
### tag中最重要的属性: name和attributes
#### name
每个tag都有自己的名字,通过 .name 来获取:
```Python
print(tag.name)# b
```
如果改变了tag的name,那将影响所有通过当前Beautiful Soup对象生成的HTML文档:
```Python
tag.name="yph"
print(tag)
# <yph class="boldest">Extremely bold</yph>
```
#### Attributes
一个tag可能有很多个属性. tag <b class="boldest"> 有一个 “class” 的属性,值为 “boldest” . tag的属性的操作方法与字典相同:
```Python
tag['class']
# u'boldest'
```
也可以直接”点”取属性, 比如: .attrs :

```Python
tag.attrs
# {u'class': u'boldest'}
```
Tag的属性可以被添加,删除或修改. 再说一次, tag的属性操作方法与字典一样
```Python
tag['class']='tstclass'
#添加
tag['id']='yph'
print(tag) #<yph class="tstclass" id="yph">Extremely bold</yph>
# 删除
del  tag['class']
del  tag['id']
print(tag) #<yph>Extremely bold</yph>

```
#### 多值属性
HTML 4定义了一系列可以包含多个值的属性.在HTML5中移除了一些,却增加更多.最常见的多值的属性是 `class` (一个tag可以有多个CSS的class). 还有一些属性 `rel` , `rev` , `accept-charset` , `headers` , `accesskey` . 在Beautiful Soup中多值属性的返回类型是`list`:
```Python
css_soup=BeautifulSoup('<p class="body strikeout"></p>','lxml')
print(css_soup.p['class']) #['body', 'strikeout']

css_soup=BeautifulSoup('<p class="body"></p>','lxml')
print(css_soup.p['class']) #['body']

```
 如果某个属性看起来好像有多个值,但在任何版本的HTML定义中都没有被定义为多值属性,那么Beautiful Soup会将这个属性作为字符串返回
```Python
id_soup=BeautifulSoup('<p id="my id"></p>','lxml')
print(id_soup.p['id']) # my id
```
将tag转换成字符串时,多值属性会合并为一个值
```Python
rel_soup = BeautifulSoup('<p>Back to the <a rel="index">homepage</a></p>','lxml')
print(rel_soup.a['rel'])  #['index']
rel_soup.a['rel']=['index','contents']
print(rel_soup.p) #<p>Back to the <a rel="index contents">homepage</a></p>
print(rel_soup.a['rel']) #['index', 'contents']
```
**如果转换的文档是XML格式,那么tag中不包含多值属性**
```Python
xml_soup = BeautifulSoup('<p class="body strikeout"></p>', 'xml')
print(xml_soup.p['class']) #body strikeout
```
### 可以遍历的字符串

字符串常被包含在tag内.Beautiful Soup用 NavigableString 类来包装tag中的字符串:
```Python
soup =BeautifulSoup('<b class="boldest">Extremely bold</b>',"lxml")
tag =soup.b
print(tag.string) #Extremely bold
print(type(tag.string)) #<class 'bs4.element.NavigableString'>
```
### 注释及特殊字符串

Tag , NavigableString , BeautifulSoup 几乎覆盖了html和xml中的所有内容,但是还有一些特殊对象.容易让人担心的内容是文档的注释部分:

```Python
markup = "<b><!--Hey, buddy. Want to buy a used parser?--></b>"
soup=BeautifulSoup(markup,'html.parser')
comment=soup.b.string
# Comment 对象是一个特殊类型的 NavigableString 对象:
print(comment) #Hey, buddy. Want to buy a used parser?
print(type(comment)) #<class 'bs4.element.Comment'>
print(soup.b.prettify())
```
## 遍历文档树
### 子节点
一个Tag可能包含多个字符串或其它的Tag,这些都是这个Tag的子节点.Beautiful Soup提供了许多操作和遍历子节点的属性.
**注意: Beautiful Soup中字符串节点不支持这些属性,因为字符串没有子节点**

tag的名字

操作文档树最简单的方法就是告诉它你想获取的tag的name.如果想获取 <head> 标签,只要用 soup.head :
```Python
soup.head
# <head><title>The Dormouse's story</title></head>
soup.title
# <title>The Dormouse's story</title>
```
通过点取属性的方式只能获得当前名字的第一个tag:
```Python
soup.a
# <a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>
```

## .contents 和 .children

tag的 .contents 属性可以将tag的子节点以列表的方式输出:
```Python
head_tag= soup.head
print(head_tag) #<head><title>The Dormouse's story</title></head>
print(head_tag.contents) #<title>The Dormouse's story</title>] 返回一个列表
title_tag= head_tag.contents[0]
print(title_tag) #<title>The Dormouse's story</title>
print(title_tag.contents)
```
BeautifulSoup 对象本身一定会包含子节点,也就是说<html>标签也是 BeautifulSoup 对象的子节点:
字符串没有 .contents 属性,因为字符串没有子节点:
```Python
text = title_tag.contents[0]
text.contents
# AttributeError: 'NavigableString' object has no attribute 'contents'

```
通过tag的 .children 生成器,可以对tag的子节点进行循环:
```Python
for child in title_tag.children:
    print(child)
    # The Dormouse's story
```

## .descendants
  .contents  和.children 属性仅包含tag的直接子节点.例如 , < head > 标签只有一个直接子节点 < title >
  .descendants 属性可以对所有tag的子孙节点进行递归循环  :
```Python
for child in head_tag.descendants:
    print(child)

print(soup.html.head.title.string)

```
## .strings 和 stripped_strings
如果tag中包含多个字符串  ,可以使用 .strings 来循环获取:
```Python
for string in soup.strings:
    print(string)
```
 输出的字符串中可能包含了很多空格或空行,使用 .stripped_strings 可以去除多余空白内容:
```Python
print('**************************')
for string in soup.stripped_strings:
    print(string)
```
## 父节点
继续分析文档树,每个tag或字符串都有父节点:被包含在某个tag中
### .parent
 通过 .parent 属性来获取某个元素的父节点.

```Python
title_tag= soup.title
print(title_tag.parent) #<head><title>The Dormouse's story</title></head>
print(title_tag.string.parent) #<title>The Dormouse's story</title>
```

### .parents
 通过元素的 .parents 属性可以递归得到元素的所有父辈节点,
```Python
link= soup.a
print(link)
for parent in link.parents:
    if parent is None:
        print(parent)
    else:
        print(parent.name)
```
### 兄弟节点

```Python
sibling_soup= BeautifulSoup("<a><b>text1</b><c>text2</c></b></a>",'html.parser')
print(sibling_soup.prettify())

print(sibling_soup.b.next_sibling)
print(sibling_soup.c.previous_sibling)
```


#### .next_siblings 和 .previous_siblings
 通过 .next_siblings 和 .previous_siblings 属性可以对当前节点的兄弟节点迭代输出:

```Python
print('*********************')
for sibling in soup.a.next_siblings:
    print(repr(sibling))
print('*********************')
for sibling in soup.find(id="link3").previous_siblings:
    print(repr(sibling))

```

### .next_element 和 .previous_element
 .next_element 属性指向解析过程中下一个被解析的对象(字符串或tag),结果可能与 .next_sibling 相同,但通常是不一样的.

```Python
last_a_tag= soup.find('a',id='link3')
print(last_a_tag)
print(last_a_tag.next_element)
print(last_a_tag.previous_element)
print(last_a_tag.previous_element.next_element)
```


### .next_elements 和 .previous_elements
 通过 .next_elements 和 .previous_elements 的迭代器就可以向前或向后访问文档的解析内容,就好像文档正在被解析一样:
```Python
for element in last_a_tag.next_elements:
    print(repr(element))
```
## 搜索文档树
 find() 和 find_all() .其它方法的参数和用法类似,请读者举一反三.
### find_all()
`find_all( name , attrs , recursive , string , **kwargs )`
find_all() 方法搜索当前tag的所有tag子节点,并判断是否符合过滤器的条件.
* name参数
name 参数可以查找所有名字为 name 的tag,字符串对象会被自动忽略掉
搜索 name 参数的值可以使任一类型的 过滤器 ,字符窜,正则表达式,列表,方法或是 True .
```Python
print(soup.find_all('title'))
```

* keyword 参数
如果一个指定名字的参数不是搜索内置的参数名,搜索时会把该参数当作指定名字tag的属性来搜索,如果包含一个名字为 id 的参数,Beautiful Soup会搜索每个tag的”id”属性.

```Python
print(soup.find_all(id='link2'))
```
 如果传入 href 参数,Beautiful Soup会搜索每个tag的”href”属性:

```Python
print(soup.find_all(href=re.compile('elsie')))
```

搜索指定名字的属性时可以使用的参数值包括 字符串 , 正则表达式 , 列表, True .

```Python
print(soup.find_all(id=True))
print(soup.find_all(href=re.compile('elsie'),id='link1'))
```

有些tag属性在搜索不能使用,比如HTML5中的 data-* 属性:

```Python
data_soup = BeautifulSoup('<div data-foo="value">foo!</div>','html.parser')
# print(data_soup.find_all( data-foo="value"))
print(data_soup.find_all(attrs={'data-foo':'value'}))
```
* 按CSS搜索

 按照CSS类名搜索tag的功能非常实用,但标识CSS类名的关键字 class 在Python中是保留字,使用 class 做参数会导致语法错误.从Beautiful Soup的4.1.1版本开始,可以通过 class_ 参数搜索有指定CSS类名的tag:
```Python
print('*********************')
res =soup.find_all('a',class_='sister')
for alinke  in res :
    print(alinke)
```
 class_ 参数同样接受不同类型的 过滤器 ,字符串,正则表达式,方法或 True
```Python
print('*********************')
def has_six_characters(css_class):
    return css_class is not None and len(css_class) == 6
print(soup.find_all(class_ =has_six_characters))
```
tag的 class 属性是 多值属性 .按照CSS类名搜索tag时,可以分别搜索tag中的每个CSS类名:
```Python
css_soup = BeautifulSoup('<p class="body strikeout"></p>')
css_soup.find_all("p", class_="strikeout")
# [<p class="body strikeout"></p>]

css_soup.find_all("p", class_="body")
# [<p class="body strikeout"></p>]
```
* string 参数
通过 string 参数可以搜搜文档中的字符串内容.与 name 参数的可选值一样, string 参数接受 字符串 , 正则表达式 , 列表, True . 看例子:

```Python
soup.find_all(string="Elsie")
# [u'Elsie']

soup.find_all(string=["Tillie", "Elsie", "Lacie"])
# [u'Elsie', u'Lacie', u'Tillie']

soup.find_all(string=re.compile("Dormouse"))
[u"The Dormouse's story", u"The Dormouse's story"]

def is_the_only_string_within_a_tag(s):
    ""Return True if this string is the only child of its parent tag.""
    return (s == s.parent.string)

soup.find_all(string=is_the_only_string_within_a_tag)
# [u"The Dormouse's story", u"The Dormouse's story", u'Elsie', u'Lacie', u'Tillie', u'...']

```
虽然 string 参数用于搜索字符串,还可以与其它参数混合使用来过滤tag.Beautiful Soup会找到 .string 方法与 string 参数值相符的tag.下面代码用来搜索内容里面包含“Elsie”的<a>标签:

实例操作：

```Python
html= """
<div id="pages">
	<a href="/fenlei/15.html">&lt;</a><a class="curr">1</a><a href="/fenlei/15_2.html" target="_self">2</a><a href="/fenlei/15_3.html" target="_self">3</a><a href="/fenlei/15_4.html" target="_self">4</a><a href="/fenlei/15_5.html" target="_self">5</a><a href="/fenlei/15_6.html" target="_self">6</a><a href="/fenlei/15_7.html" target="_self">7</a><a href="/fenlei/15_8.html" target="_self">8</a><a href="/fenlei/15_2.html">&gt;</a></div>
"""
page_soup= BeautifulSoup(html,'html.parser')
rs =page_soup.find_all(string='>')
print(rs[0].previous_element.string )
print(page_soup.find(string='>').previous_element['href'])
print('*********************')
for element in page_soup.find(string ='>').previous_element.previous_elements:
    print(element)
print('*********************')
for element in page_soup.find(string ='>').previous_element.previous_siblings:
    print(element)

next_page_url = page_soup.find(string='>').previous_element['href']
print(next_page_url)
print('*********************')
for child in page_soup.find(id="pages").contents:
    print(child)

print('*********************')
for child in page_soup.find(id="pages").children:
    print(child)

print('*********************')
curr = page_soup.find(class_='curr')
print(curr)

```
* limit 参数

find_all() 方法返回全部的搜索结构,如果文档树很大那么搜索会很慢.如果我们不需要全部结果,可以使用 limit 参数限制返回结果的数量.效果与SQL中的limit关键字类似,当搜索到的结果数量达到 limit 的限制时,就停止搜索返回结果.
```Python
print(page_soup.find_all('a',limit=3))
```
* recursive 参数
调用tag的 find_all() 方法时,Beautiful Soup会检索当前tag的所有子孙节点,如果只想搜索tag的直接子节点,可以使用参数 recursive=False .
```Python
print(page_soup.find_all('a',recursive=True))

```
```Python
# 以下两方法等价
print(page_soup.find_all("a"))
print('*********************')
print(page_soup("a"))

```
## CSS选择器
 Beautiful Soup支持大部分的CSS选择器 http://www.w3.org/TR/CSS2/selector.html [6] , 在 Tag 或 BeautifulSoup 对象的 .select() 方法中传入字符串参数, 即可使用CSS选择器的语法找到tag:

```Python
print('*********************')
print(soup.select('body a'))
print(page_soup.select('a'))
```
* 通过tag标签逐层查找:
```Python
print(soup.select('html head title'))
```
* 找到某个tag标签下的直接子标签 [6] :
```Python
print(soup.select('head > title'))
print(soup.select('p > a'))
print(soup.select('p > a:nth-of-type(2)'))
print(soup.select('p > #link1'))
print(soup.select('p > a:nth-of-type(3)'))
# print(soup.select('div > a'))
```


* 找到兄弟节点标签:

```Python
print(soup.select('#link1 ~ .sister'))
print(soup.select('#link1 + .sister'))
```


* 通过CSS的类名查找:
```Python
print(soup.select('.sister'))
print(soup.select('[class~=sister]'))
```

* 通过tag的id查找:
```Python
print('*********************')
print(soup.select('#link1'))
print(soup.select('a#link2'))
```

* 同时用多种CSS选择器查询元素:
```Python
print('*********************')
print(soup.select('#link1,#link2'))

print('*********************')
```
* 通过是否存在某个属性来查找:
```Python
print(soup.select('a[href]'))
```

* 通过属性的值来查找:
```Python
print(soup.select('a[href="http://example.com/elsie"]'))
print(soup.select('a[href^=http://example.com]'))
print(soup.select('a[href$="tillie"]'))
print(soup.select('a[href*=".com/el"]'))
```
* 返回查找到的元素的第一个
```Python
print(soup.select_one('.sister'))
```
