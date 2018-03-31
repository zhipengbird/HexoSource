---
title: 监听NSMutableArray的变化 
date: 2017-07-10 10:19:00
categories: iOS
tags: 
- NSMutableArray
---
平时工作中我们接触最多的容器，可能就数字典与数组了。有时我们在使用这些容器时有这样一个困惑，容器中的数据改变了，我们却无法立即知道。近日小编因为这个感到有些小苦恼，思来想去发现还是有途径可以做到监听容器的变化。
接下来我将会用文字将我的想法叙述出来，供大家参考。
要想监听一个容器的变化，首先我们需要对这个容器的属性及方法有着深入的了解。要监听容器的元素改变，那肯定是针对于可变容器。我们先着手于可变数组的监听工作。
接下来的工作将分成三部分：
1. 可变数组容器的分析
2. 设计一套监听回调机制
3. 使用runtime机制替换或者交换对应的函数指针IMP

现在我们开始着手分析可变数组的方法。
# 可变数组的分析
## 可变数组的定义
**NSMutableArray**定义了一套接口用于管理可变数组对象，该类的添加／插入／删除基础操作继承于不可变数组**NSArray**

## 可变数组的方法分类
通过查阅苹果的官方文档，我们大致可以将可变数组的方法分为以下几部分：
1. 创建和初始化一个可变数组
2. 数组元素的添加操作
3. 数组元素的删除操作
4. 数组元素的替换操作
5. 数组内容的筛选操作
6. 数组内容的排序操作

下面我将各部分的相关函数贴出，以便大家更好的直观了解。
### 创建和初始化一个可变数组
{% codeblock 类方法   lang:objc   %}
+ arrayWithCapacity:
+ arrayWithContentsOfFile:
+ arrayWithContentsOfURL:
{% endcodeblock %}
{% codeblock 实例方法  lang:objc   %}
- init
- initWithCapacity:
- initWithContentsOfFile:
- initWithContentsOfURL:
{% endcodeblock %}

### 数组元素的添加操作

{% codeblock 元素的添加  lang:objc   %}
 - addObject:
 - addObjectsFromArray:
{% endcodeblock %}

{% codeblock 元素的插入  lang:objc   %}
- insertObject:atIndex:
- insertObjects:atIndexes:
{% endcodeblock %}
> ps:批量元素的插入会调用多次单个元素的插入方法，***在以后的监听过程中需要做些特殊的处理***
{% codeblock 批量元素的插入  lang:objc  %}
- void insertObjects:(NSArray *)additions atIndexes:(NSIndexSet *)indexes
{
    NSUInteger currentIndex = [indexes firstIndex];
    NSUInteger i, count = [indexes count];
    for (i = 0; i < count; i++)
    {
        [self insertObject:[additions objectAtIndex:i] atIndex:currentIndex];
        currentIndex = [indexes indexGreaterThanIndex:currentIndex];
    }
}
{% endcodeblock %}

### 数组元素的删除操作
{% codeblock 删除单个元素  lang:objc   %}
//删除元素的删除
- removeLastObject
- removeObjectAtIndex:

{% endcodeblock %}
{% codeblock 批量删除  lang:objc   %}
// 批量元素的删除
- removeAllObjects
- removeObject:
- removeObject:inRange:
- removeObjectsAtIndexes:
- removeObjectIdenticalTo:
- removeObjectIdenticalTo:inRange:
- removeObjectsInArray:
- removeObjectsInRange:
{% endcodeblock %}
>ps： 以下方法会多次调用`- removeObjectAtIndex:`方法：,在监听过程中需要处理多次调用问题。
{% codeblock 多次调用- removeObjectAtIndex:  lang:objc  %}
- removeObject:
- removeObject:inRange:
- removeObjectIdenticalTo:
- removeObjectIdenticalTo:inRange:
- removeObjectsInArray:
{% endcodeblock %}

### 数组元素的替换操作

{% codeblock 单个元素的替换  lang:objc  %}
- replaceObjectAtIndex:withObject:
- setObject:atIndexedSubscript:
{% endcodeblock %}
>ps：`- setObject:atIndexedSubscript:`这个方法我们最好不要直接使用而是使用语法糖的形式调用。
```objc
mutableArray[3] = @"someValue"; 
// equivalent to 
[mutableArray replaceObjectAtIndex:3 withObject:@"someValue"];
```
>若替换对象为空会抛出`NSInvalidArgumentException`异常，若索引下标越界会抛出` NSRangeException`异常

{% codeblock 批量元素的替换  lang:objc   %}
- replaceObjectsAtIndexes:withObjects:
- replaceObjectsInRange:withObjectsFromArray:range:
- replaceObjectsInRange:withObjectsFromArray:
- setArray:
{% endcodeblock %}
>ps: 以下方法会调用多次`- replaceObjectAtIndex:withObject:`方法
{% codeblock 多次调用- replaceObjectAtIndex:withObject:  lang:objc   %}
- replaceObjectsAtIndexes:withObjects:
//PS: objects的个数必须与indexes的个数相同，否则会抛出异常
{% endcodeblock %}


### 数组元素的筛选
{% codeblock 筛选  lang:objc   %}
- filterUsingPredicate:
{% endcodeblock %}
>ps： 会在数组中直接筛选出符合条件的数组元素，不符合的会直接删除

### 数组元素的排序
{% codeblock 下标元素的交换  lang:objc  %}
- exchangeObjectAtIndex:withObjectAtIndex:
{% endcodeblock %}
{% codeblock 排序操作  lang:objc   %}
- sortUsingDescriptors:
- sortUsingComparator:
- sortWithOptions:usingComparator:
- sortUsingFunction:context:
- sortUsingSelector:
{% endcodeblock %}
>ps:数组的排序方法会递归调用 `- replaceObjectAtIndex:withObject:`**在监听数组排序时需要处理多次回调的问题**


通过对数组方法的分类及分析，我们对数组的方法有了大概的了解。接下来我们来制定一套协议，用来做回调操作。

# 监听机制的协议商定
## 协议行为的划分
从数组元素的变化行为来看，协议主要可以划分为以下几部分：
1. 元素增加
    * 单个元素的增加
    * 多个元素的增加
2. 元素删除
    * 单个元素的删除
    * 多个元素的删除
3. 元素的替换
    * 单个元素的替换
4. 数组改变了
    * 删除所有元素
    * 批量元素的替换
    * 数组的排序操作
    * 数组元素的筛选
5. 数组元素的交换
    * 元素的交换

## 参数商定
监听数组变化，哪些数据需要被回调回来呢？
通过参考表格协议和同事进行商讨决定

对于数组元素的添加／删除操作需要将以下数据回调回来：
* 监听的数组本身
* 添加删除的元素对象
* 及对应的索引返回。


对于数组的替换操作将以下数据回调回来：
* 监听数组对象本身
* 替换的对象
* 替换的新对象
* 对象的索引

对于数组改变了的相关操作，考虑到期复杂度相对较高，先采用较简单的方式处理，简洁明了的告诉协议的遵守者，说数组变了，回调回监听的数组对象本身。
对于数组元素的交换，将以下数据回调：
* 监听的数组本身
* 交换的下标1
* 将交的下标2

## 协议确认
{% codeblock 数组监听协议  lang:objc   %}
@protocol NSmutableArrayOberver <NSObject>
@optional
#pragma mark - 增加
-(void)mutableArray:(NSMutableArray*)array didAddObject:(id)anObject atIndex:(NSInteger)index;
-(void)mutableArray:(NSMutableArray*)array didAddObjects:(NSArray *)objects atIndexes:(NSIndexSet*)indexSet;

#pragma mark - 删除
-(void)mutableArray:(NSMutableArray *)array didDeleteObject:(id)anObject atIndex:(NSInteger)index;

-(void)mutableArray:(NSMutableArray *)array didDeleteObjects:(NSArray*)objects atIndexes:(NSIndexSet*)indexes;

#pragma mark  - 替换

-(void)mutableArray:(NSMutableArray*)array replaceObject:(id )object withObject:(id)anObject atIndex:(NSUInteger)index;

#pragma mark  - 改变（排序）

-(void)mutableArrayhasChanged:(NSMutableArray*)array;

#pragma mark - 位置交换
-(void)mutableArray:(NSMutableArray*)array exchangeObjectAtIndex:(NSUInteger)index1 withObjectAtIndex:(NSUInteger)index2;
@end

{% endcodeblock %}

# 使用runtime机制替换或者交换对应的函数指针IMP
1. 创建一个数组的扩展，并添加代理属性（使用**关联属性**添加）
2. 创建相应的函数指针，并替换数组对应的方法IMP
这里以- insertObject:atIndex:为例：
创建C函数指针：
{% codeblock 插入一个元素的函数指针  lang:c %}
typedef void (*insertObject_atIndex_IMP)(id self, SEL _cmd ,id anObject ,NSUInteger index);
static insertObject_atIndex_IMP origin_insertObject_atIndex = nil;
static void replace_insertObject_atIndex(id self, SEL _cmd ,id anObject ,NSUInteger index){
    NSMutableArray * array = self;
    if ([array.delegate conformsToProtocol:@protocol(NSmutableArrayOberver) ]&& [array.delegate respondsToSelector:@selector(mutableArray:didAddObject:atIndex:)]) {
        
        NSInteger number = addCount();
        origin_insertObject_atIndex(self,_cmd,anObject,index);
        if (number==1) {
            [array.delegate mutableArray:self didAddObject:anObject atIndex:index];
        }
        decreaseCount();
        
    }else{
        origin_insertObject_atIndex(self,_cmd,anObject,index);
    }
}
{% endcodeblock %}
{% codeblock 替换IMP  lang:objc %}
+(void)load{
     Method method;
    Class  class = NSClassFromString(@"__NSArrayM");
#pragma mark - 添加
    method = class_getInstanceMethod(class, @selector(insertObject:atIndex:));
    origin_insertObject_atIndex = (insertObject_atIndex_IMP)method_setImplementation(method, (IMP)replace_insertObject_atIndex);
}
{% endcodeblock %}

>PS:这里使用对一个线程变量用于统计函数的调用次数，进入函数时进行加1操作，退出时进行减1操作。如果当前调用次数为1,则在当前方法中进行回调，如果不在则不回调，由上一级调用函数进行回调。该原理类似于引用计数。

至一个基础版本的数组监听工作已经完成，后续将兼容block的工作模式，以便提高代码的高聚合。


参考博客：
1. [轻松学习之 IMP指针的作用](http://www.cocoachina.com/ios/20150717/12623.html)
2. [Objective-C Runtime 运行时之三：方法与消息](http://southpeak.github.io/2014/11/03/objective-c-runtime-3/)

