---
title: iOS 取整方式
date: 2017-03-10 17:30:27
categories: 
- iOS
tags: 
- 数值取整
---
***ios的向上，向下以及四舍五入的取整方式***
```swift
import UIKit

/**凑整*/
ceil(2)//2
ceil(2.3)//3
ceil(2.5)//3
ceil(2.8)//3
ceil(-2.1)//-2
ceil(-2.9)//-2

/**四舍五入取整*/
//rint(2)//2
rint(2.1)//2
rint(2.2)//2
rint(2.5)//2
rint(2.6)//3
rint(2.9)//3

rint(-2.1)//-2
rint(-2.5)//-2
rint(-2.8)//-3


/**舍掉小数取整*/
floor(2.1)//2
floor(2)//2
floor(2.5)//2
floor(2.9)//2

floor(-2)//-2
floor(-2.3)//-3
floor(-2.9)//-3

/**四舍五入*/
round(2.1)//2
round(2.5)//3
round(2.8)//3
round(-2.1)//-2
round(-2.5)//-3
round(-2.9)//-3


/**
 floor向下取整，ceil向上取整；round和rint四舍五入，取绝对值后舍入，然后加上符号，遇到.5的时候向绝对值小的方向舍之
 */

```
