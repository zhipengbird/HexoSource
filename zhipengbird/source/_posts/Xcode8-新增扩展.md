---
title: Xcode8 新增扩展
date: 2017-02-14 10:47:59
categories: Xcode8
tags:
- tips
---

1. Xcode8 中新增文档注释功能
  实现方法：option+cmd+/
  ```swift
  /// 创建一个图片视图
  let imageview = UIImageView(image: #imageLiteral(resourceName: "friend"))
  ```
2. 新增颜色选择器
  实现方法: color+回车
```swift
  //color+回车
v.backgroundColor=#colorLiteral(red: 0.5568627715, green: 0.3529411852, blue: 0.9686274529, alpha: 1)
```
![颜色选择](http://7xw3wp.com1.z0.glb.clouddn.com/swift_select_color.png)  
3. Xcode8中取消预编译指令,原来的#pragma mark替换为//MARK:
实现方法: //MARK: -
```swift
//MARK: - 设置图片的frame
img.frame = CGRect(x: 0, y: 0, width: 200, height: 100)

//MARK: 设置图片的center
img.center = view.center

```


4. 原来的#warning也相应的替换成 //TODO: 和//FIXME:
```swift    
    //color+回车
      v.backgroundColor=#colorLiteral(red: 0.5568627715, green: 0.3529411852, blue: 0.9686274529, alpha: 1) //TODO: 设置需要的颜色

    view.addSubview(v)
        /// 创建一个图片视图
    let imageview = UIImageView(image: #imageLiteral(resourceName: "friend")) //FIXME: 设置对应的图像

```


5. 图片展示
![图片预览](http://7xw3wp.com1.z0.glb.clouddn.com/swift_image_select.png)
