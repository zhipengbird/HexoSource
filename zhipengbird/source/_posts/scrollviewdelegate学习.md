---
layout: "post"
title: "ScrollViewDelegate学习"
date: "2016-04-19 12:59"
tags:
 - ScrollViewDelegate
 - iOS
---
**用户拖动界面时所触发的代理方法调用有如下：**
1. 当用户将手指放在界面中并开始拖动一定距离时会调用`scrollViewWillBeginDragging：`
2. 随之会触发`scrollViewDidScroll`方法的调用
3. 当用户手指离开界面时会触发`scrollViewWillEndDragging:withVelocity:targetContentOffset:`的调用，在这个代理方法的实现中我们可以去修改的目标的`contentOffset`
4. 随之系统会调用结束拖动方法`scrollViewDidEndDragging:willDecelerate:`的调用，其第二个参数用来说明是否需要做减速，这个值的主要来源于上个方法中的`Velocity`，如果这个值不为CGPointZero，那第二个参数的值为YES;如果为（0，0）则方法调用到此结束。
5. 如果将要减速为YES,则系统会继续调用`scrollViewWillBeginDecelerating:`方法，并开始减速滑动同时调用`scrollViewDidScroll:`方法
6. 当速度为0时会调用`scrollViewDidEndDecelerating:`方法结束减速滑动操作

**设置内容偏移量或显示某个区域时所触发的方法调用**
* 设置内容偏移量`setContentOffset:animated:`
* 滑动至某一区域`scrollRectToVisible:animated:`
* 当上述方法中的`animated`为yes时，会调用`scrollViewDidScroll:`和`scrollViewDidEndScrollingAnimation:`方法,如果animated的值为`NO`,只会调用`scrollViewDidScroll:`方法，
