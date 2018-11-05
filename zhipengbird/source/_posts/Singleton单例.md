---
title: Singleton单例
date: 2018-10-08 19:52:12
tags:
---
> 单例类总是返回自己的同一个实例，它提供了对类的对象所提供资源的全局访问点。
单例模式的意图：
使得类的一个对象成为系统中的唯一实例。

单例版本1：

```objc
NS_ASSUME_NONNULL_BEGIN
@interface TestManager : NSObject
@property (nonatomic,copy) NSString * name;
+ (instancetype)shareInstance;
@end
NS_ASSUME_NONNULL_END

@implementation TestManager
static TestManager * manager = nil;
+ (instancetype)shareInstance{
    if (manager== nil) {
        manager = [[TestManager alloc] init];
    }
    return manager;
}
@end
```

这不是一个“严格”意义上的单例，要想在实际中使用，需要面对以下两个主要的障碍：

* 发起调用的对象不能以其他分配方式实例化单例对象。否则，就有可以创建单例类的多个实例
* 对单例对象实例化的限制应该与引用计数内存模型共存

以下测试代码对TestManager生成了多个不同的实例

```objc
    TestManager * test1 = [TestManager shareInstance];
    test1.name = @"1";
    TestManager * test2 = [[TestManager  alloc] init];
    test2.name = @"rewq";
    NSLog(@"%@:%@",test1.name,test2.name); // 1:rewq
```  
此模式下，调用test1的copy方法会crash ，`-[TestManager copyWithZone:]: unrecognized selector sent to instance 0x6000003b05f0` TestManager类未实现`NSCopying`协议 

版本2
```objc
@implementation TestManager
static TestManager * manager = nil;
+ (instancetype)shareInstance{
    static TestManager * manager = nil;
    static dispatch_once_t onceToken;
    dispatch_once(&onceToken, ^{
        manager = [[TestManager alloc] init];
    });
    return manager;
}
@end
```
此版本依然是一个非“严格“意义上的单例，但这个是线程安全的。

下面我们来实现一个严格意上的单例：
版本3：
```objc
NS_ASSUME_NONNULL_BEGIN

@interface TestManager : NSObject<NSCopying,NSMutableCopying>
@property (nonatomic,copy) NSString * name;
+(instancetype)shareInstance;
@end

NS_ASSUME_NONNULL_END
@implementation TestManager
+(instancetype)shareInstance{
    static TestManager * manager = nil;
    if (manager== nil) {
        manager = [[super allocWithZone:NULL] init];
    }
    return manager;
}

+(instancetype)allocWithZone:(struct _NSZone *)zone{
    return [self shareInstance];
}
//保证不返回实例的副本，通过返回self,返回同一个实例
-(id)copyWithZone:(NSZone *)zone{
    return self;
}
//保证不返回实例的副本，通过返回self,返回同一个实例
-(id)mutableCopyWithZone:(NSZone *)zone{
    return self;
}
@end
```
和前两个版本一样，首先检查类的唯一实例是否已创建，如果没有，就创建一个新的实例并将其返回。但是，这里没有使用alloc这样的方法，而是调用`[[super allocWithZone:NULL] init]`来生成新的实例。为什么是super而不是self？因为已经在self中重载了基本的对象分配方法，所以需要“借用”其父类的功能来帮助处理底层内存分配的杂务。
Ps:这里没有考虑到多线程问题，如果在多线程中使用的话需要考虑到线程安全
版本4（线程安全）
```objc
@implementation TestManager
+(instancetype)shareInstance{
    static TestManager * manager = nil;
    static dispatch_once_t onceToken;
    dispatch_once(&onceToken, ^{
        //以下代码有crash的机率
        //manager = [[TestManager alloc] init];
         manager = [[ super allocWithZone:NULL] init];
    });
    return manager;
}

+(instancetype)allocWithZone:(struct _NSZone *)zone{
    return [self shareInstance];
}
//保证不返回实例的副本，通过返回self,返回同一个实例
-(id)copyWithZone:(NSZone *)zone{
    return self;
}
//保证不返回实例的副本，通过返回self,返回同一个实例
-(id)mutableCopyWithZone:(NSZone *)zone{
    return self;
}
@end
```
版本5（线程安全):
```objc
+(instancetype)shareInstance{
    return [[[self class] alloc]init];
}
+(instancetype)allocWithZone:(struct _NSZone *)zone{
    static TestManager * manager = nil;
    static dispatch_once_t onceToken;
    dispatch_once(&onceToken, ^{
        //不能这样添加，否则会crash
        // manager = [[TestManager alloc] init];
         manager = [[ super allocWithZone:zone] init];
    });
    return manager;
}
//保证不返回实例的副本，通过返回self,返回同一个实例
-(id)copyWithZone:(NSZone *)zone{
    return self;
}
//保证不返回实例的副本，通过返回self,返回同一个实例
-(id)mutableCopyWithZone:(NSZone *)zone{
    return self;
}
@end
```

## 一劳永逸，单例模式的优化

如果想要一劳永逸，我们将面临两个问题

1. 如何写一份单例代码在ARC和MRC环境下都适用？
2. 如何使一份单例代码可以多个类共同使用  

为了解决这两个问题，我们可以在PCH文件使用代参数的宏和条件编译
利用条件编译来判断是ARC还是MRC环境
```objc
#if __has_feature(objc_arc)
//如果是ARC，那么就执行这里的代码1
#else
//如果不是ARC，那么就执行代理的代码2
#endif
```

将单例方法写成宏定义：
```objc
#define singleH(name) +(instancetype)shareInstance;

#if __has_feature(objc_arc)

#define singleM(name) static id _instance;\
+ (instancetype)allocWithZone:(struct _NSZone *)zone\
{\
static dispatch_once_t onceToken;\
dispatch_once(&onceToken, ^{\
_instance = [super allocWithZone:zone];\
});\
return _instance;\
}\
\
+ (instancetype)shareInstance\
{\
return [[self alloc]init];\
}\
- (id)copyWithZone:(NSZone *)zone\
{\
return _instance;\
}\
\
- (id)mutableCopyWithZone:(NSZone *)zone\
{\
return _instance;\
}


#else
#define singleM static id _instance;\
+ (instancetype)allocWithZone:(struct _NSZone *)zone\
{\
static dispatch_once_t onceToken;\
dispatch_once(&onceToken, ^{\
_instance = [super allocWithZone:zone];\
});\
return _instance;\
}\
\
+ (instancetype)shareInstance\
{\
return [[self alloc]init];\
}\
- (id)copyWithZone:(NSZone *)zone\
{\
return _instance;\
}\
\
- (id)mutableCopyWithZone:(NSZone *)zone\
{\
return _instance;\
}\
- (oneway void)release\
{\
}\
\
- (instancetype)retain\
{\
return _instance;\
}\
\
- (NSUInteger)retainCount\
{\
return MAXFLOAT;\
}
#endif
```
在.h文件中调用singleH(类名)
在.m文件中调用singleM(类名)