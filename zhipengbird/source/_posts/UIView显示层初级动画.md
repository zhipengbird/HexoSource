---
title: UIView显示层初级动画
date: 2018-09-24 12:14:44
tags:
- 动画
---
## 显示层初级动画属性预览

```swift
extension UIView {

    open var frame: CGRect

    open var bounds: CGRect

    open var center: CGPoint

    open var transform: CGAffineTransform

    @NSCopying open var backgroundColor: UIColor?

    open var alpha: CGFloat
}
```

```objective-c
@property(nullable, nonatomic,copy)  UIColor *backgroundColor UI_APPEARANCE_SELECTOR; 

@property(nonatomic)CGFloat alpha;  // animatable. default is 1.0

// use bounds/center and not frame if non-identity transform. if bounds dimension is odd, center may be have fractional part

@property(nonatomic) CGRect bounds;// default bounds is zero origin, frame size. animatable

@property(nonatomic) CGPoint center; // center is center of frame. animatable

@property(nonatomic) CGAffineTransform transform;   // default is CGAffineTransformIdentity. animatable
```