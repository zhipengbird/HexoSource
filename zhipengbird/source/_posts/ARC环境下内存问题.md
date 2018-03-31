---
title: ARC环境下内存问题
date: 2016-09-01 17:12:26
categories: iOS 
tags:
- 内存问题
---
```objc
NSMutableArray * array  = [NSMutableArray arrayWithArray:@[@"2",@"2",@"1",@"fad",@"23"]];
for(NSString* str  in array)
{
  if (str isEqualToString:@"fad"]) {
    [array removeObject:str];
  }
}
```
编译运行时出现崩溃：
```
Terminating app due to uncaught exception 'NSGenericException', reason: '*** Collection <__NSArrayM: 0x100103560> was mutated while being enumerated.'

```
```objc
NSMutableArray * array  = [NSMutableArray arrayWithArray:@[@"2",@"2",@"1",@"fad",@"23"]];
[array enumerateObjectsUsingBlock:^(NSString*  obj, NSUInteger idx, BOOL *  stop) {
           if ([obj isEqualToString:@"fad"]) {
               [array removeObject:obj];
           }
       }];   
```
```objc
NSMutableArray * arrayNum  = [NSMutableArray arrayWithArray:@[@(2),@(21),@(1),@(100),@(23)]];
[arrayNum enumerateObjectsUsingBlock:^(NSNumber *  obj, NSUInteger idx, BOOL *  stop) {
           if ([obj isEqualToString:@(2)]) {
               [arrayNum removeObject:obj];
           }
       }];   

```
