---
title: NSIndexSet
date: 2017-07-06 11:13:46
categories: iOS
tags:
- NSIndexSet
---

{% blockquote 苹果官方的定义 %}
The NSIndexSet class represents an immutable collection of unique unsigned integers, known as indexes because of the way they are used. This collection is referred to as an index set. Indexes must be in the range 0 .. NSNotFound - 1.
{% endblockquote %}

NSIndexSet (以及它的可修改子类, NSMutableIndexSet) 是一个排好序的，无重复元素的整数集合。它看上去有点像 支持离散整数的 NSRange .它能用于快速查找特定范围的值的索引，也能用于快速计算交集, 同时，Foundation collection class 提供了很多好用的方法，方便你使用 NSIndexSet.

Foundataion framework 里面到处可以看到 NSIndexSet 的影子。 任何从已排序容器(比如 array, 或者 table view 的 data source)里面获取多个元素的方法都会用到 NSIndexSet 做为参数。

* NSIndexSet方法可以划分成以下几部分
    * 创建索引
    * 查询索引
    * 枚举索引内容
    * 比较索引
    * 获取子索引
    * 枚举索引

* NSMutableIndexSet的方法划分：
    * 增加／删除一个索引集合
    * 增加／删除单个索引
    * 增加／删除一个子范围

# 创建索引
{% codeblock 创建索引  lang:objc   %}
//创建一个空的索引集合。
+ (instancetype)indexSet;
//创建一个索引集合，根据索引值
+ (instancetype)indexSetWithIndex:(NSUInteger)value;
//创建一个索引集合，根据一个NSRange对象
+ (instancetype)indexSetWithIndexesInRange:(NSRange)range;

//创建一个索引集合，根据一个NSRange对象
- (instancetype)initWithIndexesInRange:(NSRange)range NS_DESIGNATED_INITIALIZER;
//用一个已有集合创建一个新的集合
- (instancetype)initWithIndexSet:(NSIndexSet *)indexSet NS_DESIGNATED_INITIALIZER;
//根据索引值创建一个索引集合
- (instancetype)initWithIndex:(NSUInteger)value;
{% endcodeblock %}

# 查询索引

{% codeblock 包含索引  lang:objc   %}
// ndicates whether the index set contains a specific index.
- (BOOL)containsIndex:(NSUInteger)value;

// Indicates whether the receiving index set contains a superset of the indexes in another index set.
- (BOOL)containsIndexes:(NSIndexSet *)indexSet;

// Indicates whether the index set contains the indexes represented by an index range.
- (BOOL)containsIndexesInRange:(NSRange)range;

//判断集合中的索引是否有一个或多个在Range中
- (BOOL)intersectsIndexesInRange:(NSRange)range;
{% endcodeblock %}

{% codeblock block查询  lang:objc   %}
- (NSUInteger)indexPassingTest:(BOOL (NS_NOESCAPE ^)(NSUInteger idx, BOOL *stop))predicate NS_AVAILABLE(10_6, 4_0);
- (NSUInteger)indexWithOptions:(NSEnumerationOptions)opts passingTest:(BOOL (NS_NOESCAPE ^)(NSUInteger idx, BOOL *stop))predicate NS_AVAILABLE(10_6, 4_0);
- (NSUInteger)indexInRange:(NSRange)range options:(NSEnumerationOptions)opts passingTest:(BOOL (NS_NOESCAPE ^)(NSUInteger idx, BOOL *stop))predicate NS_AVAILABLE(10_6, 4_0);

- (NSIndexSet *)indexesPassingTest:(BOOL (NS_NOESCAPE ^)(NSUInteger idx, BOOL *stop))predicate NS_AVAILABLE(10_6, 4_0);
- (NSIndexSet *)indexesWithOptions:(NSEnumerationOptions)opts passingTest:(BOOL (NS_NOESCAPE ^)(NSUInteger idx, BOOL *stop))predicate NS_AVAILABLE(10_6, 4_0);
- (NSIndexSet *)indexesInRange:(NSRange)range options:(NSEnumerationOptions)opts passingTest:(BOOL (NS_NOESCAPE ^)(NSUInteger idx, BOOL *stop))predicate NS_AVAILABLE(10_6, 4_0);
{% endcodeblock %}


{% codeblock 获取大小  lang:objc   %}
// 获取索引集合在某一范围中的个数
- (NSUInteger)countOfIndexesInRange:(NSRange)range NS_AVAILABLE(10_5, 2_0);
//索引集合中元素的个数
@property (readonly) NSUInteger count;

{% endcodeblock %}

# 枚举内容
{% codeblock 枚举集合内容  lang:objc   %}
- (void)enumerateRangesUsingBlock:(void (NS_NOESCAPE ^)(NSRange range, BOOL *stop))block NS_AVAILABLE(10_7, 5_0);
- (void)enumerateRangesWithOptions:(NSEnumerationOptions)opts usingBlock:(void (NS_NOESCAPE ^)(NSRange range, BOOL *stop))block NS_AVAILABLE(10_7, 5_0);
- (void)enumerateRangesInRange:(NSRange)range options:(NSEnumerationOptions)opts usingBlock:(void (NS_NOESCAPE ^)(NSRange range, BOOL *stop))block NS_AVAILABLE(10_7, 5_0);
{% endcodeblock %}

# 两集合的比较
{% codeblock 集合的比较  lang:objc   %}
//比较两集合是否相同
- (BOOL)isEqualToIndexSet:(NSIndexSet *)indexSet;
{% endcodeblock %}

# 获取索引
{% codeblock 获取索引  lang:objc  %}
//The following six methods will return NSNotFound if there is no index in the set satisfying the query. 
@property (readonly) NSUInteger firstIndex;
@property (readonly) NSUInteger lastIndex;
- (NSUInteger)indexGreaterThanIndex:(NSUInteger)value;
- (NSUInteger)indexLessThanIndex:(NSUInteger)value;
- (NSUInteger)indexGreaterThanOrEqualToIndex:(NSUInteger)value;
- (NSUInteger)indexLessThanOrEqualToIndex:(NSUInteger)value;
{% endcodeblock %}
{% codeblock 获取索引  lang:objc %}
//Fills up to bufferSize indexes in the specified range into the buffer and returns the number of indexes actually placed in the buffer;  
// also modifies the optional range passed in by pointer to be "positioned" after the last index filled into the buffer.  
// Example: if the index set contains the indexes 0, 2, 4, ..., 98, 100, for a buffer of size 10 and the range (20, 80)   
// the buffer would contain 20, 22, ..., 38 and the range would be modified to (40, 60).
- (NSUInteger)getIndexes:(NSUInteger *)indexBuffer maxCount:(NSUInteger)bufferSize inIndexRange:(nullable NSRangePointer)range;
{% endcodeblock %}



# 枚举索引集合
{% codeblock 枚举集合  lang:objc  %}
- (void)enumerateIndexesUsingBlock:(void (NS_NOESCAPE ^)(NSUInteger idx, BOOL *stop))block NS_AVAILABLE(10_6, 4_0);
- (void)enumerateIndexesWithOptions:(NSEnumerationOptions)opts usingBlock:(void (NS_NOESCAPE ^)(NSUInteger idx, BOOL *stop))block NS_AVAILABLE(10_6, 4_0);
- (void)enumerateIndexesInRange:(NSRange)range options:(NSEnumerationOptions)opts usingBlock:(void (NS_NOESCAPE ^)(NSUInteger idx, BOOL *stop))block NS_AVAILABLE(10_6, 4_0);
{% endcodeblock %}


# 可变集合的操作
## 增加／删除一个索引集合
{% codeblock 增加／删除一个索引集合  lang:objc   %}
- (void)addIndexes:(NSIndexSet *)indexSet;
- (void)removeIndexes:(NSIndexSet *)indexSet;
- (void)removeAllIndexes;
{% endcodeblock %}

## 增加／删除单个索引
{% codeblock 增加／删除单个索引  lang:objc   %}
- (void)addIndex:(NSUInteger)value;
- (void)removeIndex:(NSUInteger)value;
{% endcodeblock %}

## 增加／删除一个子范围
{% codeblock 增加／删除一个子范围  lang:objc  %}
- (void)addIndexesInRange:(NSRange)range;
- (void)removeIndexesInRange:(NSRange)range;
{% endcodeblock %}






