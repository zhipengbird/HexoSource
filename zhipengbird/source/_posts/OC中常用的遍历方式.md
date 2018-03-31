---
title: OC中常用的遍历方式
date: 2016-05-08 21:49:26
categories: iOS 
tags:
- 循环
- 遍历
---
&emsp;&emsp;在开发过程中经常需要列举collection中的元素，在ＯＣ语言中可以有多种方法实现该功能，标准的Ｃ语言循环，OC 1.0的枚举器（ＮSNumerator）,OC 2.0中的快速遍历。在引入块这一特性后，又新增了几种新的遍历方式，采用这几种新方式遍历collection时，可以传入块，而collection中的每个元素都可能会放在块里运行一遍，这样可以大幅度简化编码过程。
<!-- more -->
### for循环  
&emsp;&emsp;遍边数组的第一种方式就是采用老式的for循环。在作为OC的根基的Ｃ语言中就有了些特征。通常的代码样式：
```objc
void traditionalForArray(NSArray * array){

    for (int i = 0; i < array.count; i++) {
        id object = array[i];
        //TODO::to do something with object
    }
}
```
&emsp;&emsp;那么遍历字典跟set集合可以会更复杂些：  
&emsp;&emsp;_直接遍历字典的所有值_
```objc
void traditionalForDictonary(NSDictionary * dict){
    NSArray * allValues = [dict allValues];
    for (int i = 0 ; i < allValues.count; i++) {
        id value = allValues[i];
        //TODO::to do something with value
    }
}
```
&emsp;&emsp;_直接遍历字典的所有键_
```objc
void traditionalForDictonary(NSDictionary * dict){
    NSArray * allKeys = [dict allKeys];
    for (int i = 0 ; i < allKeys.count; i++) {
        id key = allKeys[i];
        id value = dict[key];
        //TODO::to do something with  key and value
    }
}
```
&emsp;&emsp;_遍历set中所有对象_
```objc
void traditionalForSet(NSSet * set){
    NSArray * allObject = [set allObjects];
    for (int i = 0 ; i < allObject.count; i++) {
        id object = allObject[i];
        //TODO::to do something with object
    }
}
```
&emsp;&emsp;根据定义，字典和set都是“无序的（unordered）”所以无法根据特定的下标来获取其中的值，为此，需要先获取字典里所有的键/值或set中所有的对象，在获取到的有序数组里就可以使用for循环获取对应下标的键/值或对象了.但创建这个附加的数组会有额外的开销，而且还会多创建一个数组对象，它会保留collection中的所有以元素对象，在释放数组时这些附加对象也会释放，这些操作看起来有点多余，因为其他方式都可以不用这么做。
&emsp;&emsp;for循环可以实现反向遍历，计数器的值从“元素的个数减一”开始，每次迭代时递减直至为0结束。执行反向遍历时，会比其他方式简单。

>总结优缺点：
优点：被广泛使用，容易接受，操作简单；
缺点：遍历字典和set是比较繁琐，会占用比较多的系统资源。

### 枚举器（`NSEnumerator`）  
&emsp;&emsp;`NSEnumerator` 是个抽象基类，其只定义了两个方法，供其具体子类实现:   

```objc
- (nullable ObjectType)nextObject;
- ( NSArray<ObjectType> *)allObjects;
```
&emsp;&emsp;`nextObject`方法是关键，它返回枚举里的下一个对象，每次调用都会更新内部结构，使得下次调用时能返回下一个对象。枚举里的全部对象返回后，再调用将会返回nil.这表示已经枚举完了。下面是各容器使用Enumerator的样式：  
_正向枚举数组_
```objc
void enumeratorForArray(NSArray * array ){
    NSEnumerator * enumerator =[array objectEnumerator];
    id object ;
    while (( object =  [enumerator nextObject])!= nil) {
         //TODO::to do something with object
    }
}
```
_逆向枚举数组_
```objc
void enumeratorForReverseArray(NSArray * array ){
    NSEnumerator * enumerator =[array reverseObjectEnumerator];
    id object ;
    while (( object =  [enumerator nextObject])!= nil) {
        //TODO::to do something with object
    }
}
```
_使用key枚举字典_
```objc
void enumeratorForDictionaryAllKeys(NSDictionary * dict){
    NSEnumerator * keyEnumerator = [dict keyEnumerator];
    id key ;
    while ((key = [keyEnumerator nextObject])!=nil) {
        id value = dict[key];
        //TODO::to do something with value
    }
}
```
_使用value 枚举字典_
```objc
void enumeratorForDictionaryAllValues(NSDictionary * dict){
    NSEnumerator * valueEnumerator = [dict objectEnumerator];
    id value ;
    while ((value = [valueEnumerator nextObject])!=nil) {

        //TODO::to do something with value
    }
}
```
_枚举set集合_
```objc
void enumeratorForSet(NSSet * set){
    NSEnumerator * objectEnumerator = [set objectEnumerator];
    id object;
    while ((object = [objectEnumerator nextObject])!= nil) {
        //TODO::to do something with object
    }
}

```
枚举的写法跟标准的for循环相似，但代码多了一点，其优势在于：不论遍历哪种collection，都可以采用这种相似的语法。
>总结优缺点：
优点：代码更易读，不需要定义额外的数组；
缺点：
    1. 无法直接获取遍历操作的下标，需要另外声明变量记录；
    2. 需要自行创建NSEnumerator对象，稍显麻烦。

### 快速遍历 (forin)  
字典
```objc
void forinDic(NSDictionary * dict){
    for (id key in dict) {
        id value = dict[key];
         //TODO::to do something with key && value
    }
}
```
数组
```objc
void forinArray(NSArray * array){
    for (id object in array) {
       //TODO::to do something with object
    }
}
```
SET集合
```objc
void forinSet(NSSet * set){
    for (id object in set) {
         //TODO::to do something with object
    }
}
```
>forin小结：
优点：  
  语法简洁，使用方便，效率高；
缺点：
    1. 无法方便获取当前遍历的下标；
    2. 无法在遍历过程中修改被遍历的collection，否则会导致崩溃。

### 块方法（block）
数组
```objc
void blockEnumeratorArray(NSArray * array){

#pragma mark － 对数组整个区间进行遍历
    [array enumerateObjectsUsingBlock:^(id  _Nonnull obj, NSUInteger idx, BOOL * _Nonnull stop) {
        //TODO::to do something with object  & index  & stop
        //TODO::可以增删除改？？
    }];

//    typedef NS_OPTIONS(NSUInteger, NSEnumerationOptions) {
//        NSEnumerationConcurrent = (1UL << 0),并发
//        NSEnumerationReverse = (1UL << 1),逆序
//    };
#pragma mark - 对数组进行遍历，可设置遍历顺序，并发或反序
    [array enumerateObjectsWithOptions:NSEnumerationConcurrent usingBlock:^(id  _Nonnull obj, NSUInteger idx, BOOL * _Nonnull stop) {
        //TODO::to do something with object  & index  & stop
    }];

#pragma mark － 对数组的某个区间进行遍历操作
    NSIndexSet * indexset  =[NSIndexSet indexSetWithIndexesInRange:NSMakeRange(0, array.count)];
    [array enumerateObjectsAtIndexes:indexset options:NSEnumerationReverse usingBlock:^(id  _Nonnull obj, NSUInteger idx, BOOL * _Nonnull stop) {
        //TODO::to do something with object  & index  & stop
    }];
}
```
字典
```objc
void blockEnumeratorDict(NSDictionary *dict){

    [dict enumerateKeysAndObjectsUsingBlock:^(id  _Nonnull key, id  _Nonnull obj, BOOL * _Nonnull stop) {
        //TODO::to do something with object  & key  & stop

    }];
#pragma mark  设置遍历顺序方式（正序或逆序)
    [dict enumerateKeysAndObjectsWithOptions:NSEnumerationReverse usingBlock:^(id  _Nonnull key, id  _Nonnull obj, BOOL * _Nonnull stop) {
        //TODO::to do something with object  & key  & stop
    }];
}
```
SET集合
```objc
void blockEnumeratorSet(NSSet * set){
    [set enumerateObjectsUsingBlock:^(id  _Nonnull obj, BOOL * _Nonnull stop) {
         //TODO::to do something with object    & stop
    }];
    #pragma mark  设置遍历顺序方式（正序或逆序)
    [set enumerateObjectsWithOptions:NSEnumerationReverse usingBlock:^(id  _Nonnull obj, BOOL * _Nonnull stop) {
         //TODO::to do something with object    & stop
    }];
}
```
<strong/>若提前知道遍历的集合含有何种对象，则应修改块签名，指出对象的具体类型。</strong>
>小结：
优点：
  1. 可以完美实现for循环的所有功能；
  2. 可以方便获取集合中的每一项元素；
  3. 提供了循环遍历的参数，NSEnumerationReverse用来实现倒序循环。NSEnumerationConcurrent用来实现并发遍历，两个参数可以同时使用；
  4. 这种循环方式效率高，能够提升程序性能，开发者可以专注于业务逻辑，而不必担心内存和线程的问题；
  5. 当开启NSEnumerationConcurrent选项时，可以实现for循环和快速遍历无法轻易实现的并发循环功能，系统底层会通过GCD处理并发事宜，这样可以充分利用系统和硬件资源，达到最优的遍历效果；
  6. 可以修改块签名，当我们已经明确集合中的元素类型时，可以把默认的签名id类型修改成已知类型，比如常见的NSString，这样既可以节省系统资源开销，也可以防止误向对象发送不存在的方法是引起的崩溃。

>缺点：
  1. 很多开发者不知道这种遍历方式；
  2. 这里使用了block，需要注意在block里容易引起的保留环问题，比如使用self调用方法时，把self转化成若引用即可打破保留环。如：__weak __typeof(self)weakSelf = self 或者 __weak MyController *weakSelf = self; 在block里使用weakSelf即可。
