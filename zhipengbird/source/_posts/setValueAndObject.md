---
title: 可变字典中setValue:forKey和setObject:forKey的区别
date: 2019-08-28 10:04:02
tags:
---

# 可变字典中setValue:forKey和setObject:forKey的区别

在开发过程中，使用字典的频率也是非常高的，也经常会遇到一个类似
`-[__NSPlaceholderDictionary initWithObjects:forKeys:count:]: attempt to insert nil object from objects[1]`
crash,那发生这种crash的真实原因又是什么呢？在开发过程中我们调用`setValue:forKey`和`setObject:forKey`频率也是非常多，但大多数情况，后者居多。我们先来看下这两个函数有定义

```objc
@interface NSMutableDictionary<KeyType, ObjectType>(NSKeyValueCoding)

/* 
Send -setObject:forKey: to the receiver, unless the value is nil, in which case send -removeObjectForKey:
*/
- (void)setValue:(nullable ObjectType)value forKey:(NSString *)key;

@end
```

该方法内部分调用在`-setObject:forKey:`方法去给字典赋值，在`value`为空的情况下会调用`-removeObjectForKey:`移除已有key的值。这样在一定程度上避免了给字典赋空值crash的情况发生

```objc

@interface NSMutableDictionary<KeyType, ObjectType> : NSDictionary<KeyType, ObjectType>

- (void)removeObjectForKey:(KeyType)aKey;
- (void)setObject:(ObjectType)anObject forKey:(KeyType <NSCopying>)aKey;
@end
```

在API上我们发现没有注释，但在文档中其代码注释非常详细：
`- (void)setObject:(ObjectType)anObject forKey:(KeyType <NSCopying>)aKey;`

* `anObject`
The value for aKey. A strong reference to the object is maintained by the dictionary.

>Important
>Raises an NSInvalidArgumentException if anObject is nil. If you need to represent a nil value in the dictionary, use NSNull.
当anobject为空时会抛出NSInvalidArgumentException异常，如果需要保存一个空值，可以使用NSNull实例

* `aKey`
  
The key for value. The key is copied (***using copyWithZone:; keys must conform to the NSCopying protocol***). If aKey already exists in the dictionary, anObject takes its place.
> Important
> Raises an NSInvalidArgumentException if aKey is nil.


从上述API定义和文档注释中我们可以得出以下区别：
* `setObject:forKey:`中`object`不能为空，若为空则会抛出异常，若要展示一个空值，需要使用NSNull实例代替
* `setValue:forKey:`中`value`可以为空，若`value`为空，内部会调用`removeObjectForKey：`方法，移除该key所对应的值
`setValue:forKey:`可以认为是对`setObject:forKey:`再次封装，对异常情况做了处理。

>我们开发过程中遇到的字典的crash，多数是因为在调用`setObject:forKey:`时，传入的object为空导致的！在使用过程要尽可以有object做判空处理，若不想这么做，可以使用`setValue:forKey:`方法替代
