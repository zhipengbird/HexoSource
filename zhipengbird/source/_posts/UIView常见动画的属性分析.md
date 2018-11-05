---
title: UIView常见动画的属性分析
date: 2018-09-16 22:41:45
tags:
- 动画
---

# 实现动画的三步曲

* 设置视图的动画初始状态
* 添加视图的动画相应属性
* 设置视图的动画最终状态

# UIView常见动画的属性分析

UIView常见的属性有哪些？

## 位置属性 `frame` `bounds` `center`

`frame` `bounds` `center`都是描述`UIView`的位置属性，不同的是`frame`可以对`x` `y` `width` `height`四个属性进行操作，`frame`的`x` `y`是相对于父控件的原点来计算的，而`bounds`一般只能对`width` `height`进行操作，它的`x` `y` 坐标只相对于自身而言，`center`描述的是`x` `y`信息，即`UIView`的中心位置。

* swift版

```swift
public struct CGRect {

    public var origin: CGPoint

    public var size: CGSize

    public init()

    public init(origin: CGPoint, size: CGSize)
}
```

* OC版

```Objective-c
/* Rectangles. */

struct CGRect {
    CGPoint origin;
    CGSize size;
};
typedef struct CG_BOXABLE CGRect CGRect;
```

再来看三者的数据类型。`frame`是`CGRect`类型，它是一个结构体，在结构体中包含`origin`,`size`两个属性，其中`origin`描述UIView的`x`,`y`坐标起点位置信息。`size`描述UIView的`width`、`height`宽高信息，我们再来看看`origin`的`CGPoint`和`size`的`CGSize` 又是什么？

* swift版

```swift
/* Points. */
public struct CGPoint {
    public var x: CGFloat

    public var y: CGFloat

    public init()

    public init(x: CGFloat, y: CGFloat)
}
```

```swift
/* Sizes. */

public struct CGSize {

    public var width: CGFloat

    public var height: CGFloat

    public init()

    public init(width: CGFloat, height: CGFloat)
}
```

* OC版

```objective-c
/* Points. */
struct
CGPoint {
    CGFloat x;
    CGFloat y;
};
typedef struct CG_BOXABLE CGPoint CGPoint;
```

```objective-c
/* Sizes. */
struct CGSize {
    CGFloat width;
    CGFloat height;
};
typedef struct CG_BOXABLE CGSize CGSize;
```

`CGPoint`中包含了`UIView`的`x`,`y`坐标，而`CGSize`中包含了`UIView`的`widt`,`height`信息。通过对`frame`中的数据类型进行追本溯源，可以得到以下结论：
> `CGRecct`分别对应`x`坐标，`y`坐标、`width`、`height`四个属性，这四个属性表明当前UI在它的父视图上的位置
可能通过`x`,`y`坐标修改`UIView`的移动位置，还可以修改`width`,`height`来修改`UIView`的拉伸、收缩效果，对于`bounds`属性使用最多的还是`width`,`height`属必，`center`则经常使用`x`,`y`坐标属性。

## 透明属性:alpha (透明属性，范围0-1,浮点型)

`UIView`的`alpha`属性也可以作动画效果，当`alpha`值为0时，表明视图已经隐藏，当`alpha`值为1时，视图显示。结合这一属性可以通过修改`alpha`值在动画开始、结束时的值可以实现淡入淡出效果。

## Layer属性：圆角渐变，边框颜色，阴影，3D等高级动画效果

UIView是视图显示的容器，负责内容显示和事件响应。每个视图都有一个layer图层，在这个图层中承栽的是视图的内容。所以结合layer可以实现很多高级效果。当然除了这些之外，视图还有其他属性。