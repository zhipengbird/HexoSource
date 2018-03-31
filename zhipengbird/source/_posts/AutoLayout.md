---
title: AutoLayout
date: 2017-03-08 22:57:09
categories: iOS
tags: 
- AutoLayout
---

一.名词解析
**intrinsicContentSize**：字面意思就是固有的大小。就是说在没有受到约束影响时本来应该有的大小。
**Content Hugging Priority**：字面意识是内容压缩优先级。就是说阻止view返回的实际尺寸比intrinsicContentSize大的优先级。
**Content Compression Resistance Priority**：字面意思就是内容抗压缩优先级。就是说阻止View返回的实际尺寸比intrinsicContentSize小的优先级。



## intrinsicContentSize 内在内容大小

使用AutoLayout 时，视图内容的大小通过每个视图的 intrinsicContentSize 属性表达，它描述了在数据未经压缩或剪裁的情况下表达视图全部内容所需的最小空间。该属性源于每个视图所呈现内容的自然属性。

对于图像视图，内在内容大小与其呈现的图像大小相符。图像越大，需要的内容大小也越大。
对于按钮，内在内容的大小随着按钮的名称而变化（title）。

通过视图的内在内容大小，Auto Layout将视图框架尽可能地与其自然内容相匹配。无歧义的布局通常需要给每个坐标轴设置两个属性，当视图有一个内在内容大小时，则只需设置两个属性中的一个。

当改变了视图的内在内容时，需要调用 invalidateIntrinsicContentSize 方法，让AutoLayout 知道在下次布局时重新计算。
