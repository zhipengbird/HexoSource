---
title: 创建block姿势
date: 2016-05-17 19:01:43
categories: 
- iOS
tags:
- block
- 代码块

---
如何在OC中声明一个block?
--------------------------------------

<font color = "#2E73D6"/> 作为一个本地变量：</font>
```objc
returnType (^blockName)(parameterTypes) = ^returnType(parameters) {...};
```


<font color ="#2E73D6"/> 作为一个属性：</font>

```objc
@property (nonatomic, copy, nullability) returnType (^blockName)(parameterTypes);
```

<!-- more -->
<font color ="#2E73D6"/>作为一个方法参数：</font>

```objc
- (void)someMethodThatTakesABlock:(returnType (^nullability)(parameterTypes))blockName;
```


<font  color ="#2E73D6"/>作为参数传递：</font>

```objc
[someObject someMethodThatTakesABlock:^returnType (parameters) {...}];
```

<font color ="#2E73D6">作为自定义类型：typedef</font>

```objc
typedef returnType (^TypeName)(parameterTypes);
TypeName blockName = ^returnType(parameters) {...};
```
