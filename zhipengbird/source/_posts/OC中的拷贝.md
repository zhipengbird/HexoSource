---
title: OC中的拷贝
date: 2016-05-19 16:14:13
categories: iOS
tags:
- copy
- multableCopy
---

>内存的栈区 : 由编译器自动分配释放, 存放函数的参数值, 局部变量的值等. 其 操作方式类似于数据结构中的栈.

>内存的堆区 : 一般由程序员分配释放, 若程序员不释放, 程序结束时可能由OS回 收. 注意它与数据结构中的堆是两回事, 分配方式倒是类似于链表.

# 用copy和multableCopy对外象
  当我们要创建一个与源对象内容相同的对象时，可以考虑使用copy和multableCopy。
  ```objc
      NSString * string = @"yuanph";
      NSString * copyString = [string copy];//拷贝出内容为yuanph的NSString类型的字符串
      NSString * multiString = [string mutableCopy];//拷贝出内容为yuanph的NSMutableString类型的字符串

      NSArray * array = @[@"yuanph"];
      NSArray * copyArray = [array copy];//拷贝出内容与array相同的NSArray类型的数组
      NSArray * multiArray  =[array mutableCopy];//拷贝出内容与array相同的NSMutableArray类型的数组

      NSDictionary * dict = @{@"name":@"yuanph"};
      NSDictionary * copyDict = [dict copy];//拷贝出内容与dict相同的NSDictionary类型的字典
      NSDictionary * multiDict = [dict mutableCopy];//拷贝出内容与dict相同的NSMutableDictionary类型的字典
```
>1.copy拷贝出来的对象类型总是不可变类型(例如, NSString, NSDictionary, NSArray等等)
 2.mutableCopy拷贝出来的对象类型总是可变类型(例如, NSMutableString, NSMutableDictionary, NSMutableArray等等)


# 深拷贝与浅拷贝
深拷贝 : 拷贝出来的对象与源对象地址不一致! ＝＝＝>修改拷贝对象的值对源对象的值没有任何影响.

浅拷贝 : 拷贝出来的对象与源对象地址一致!   =====>修改拷贝对象的值会直接影响到源对象.


<font color="ff0000"/>copy都是浅拷贝, mutableCopy都是深拷贝???????</font>

用copy从一个可变对象拷贝出一个不可变对象 =====>深拷贝


 不完全拷贝与完全拷贝
