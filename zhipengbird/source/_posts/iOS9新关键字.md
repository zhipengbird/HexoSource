---
title: iOS9新关键字
date: 2016-07-07 22:00:41
categories: iOS 
tag:
 - keywords
---
在iOS9 中苹果推出了很多新的关键字，目的其实很明确，主要是提高开发人员的效率，有益于程序员之间的沟通与交流，在开发中代码更加规范。

## 关键字： nullable 与 nonull
   nullable:表示可以为nil  
   nonull: 表示不可以为nil  
   这两关键字只能修改对象，不能修饰基本数据类型，可以用在属性、方法的参数、方法的返回值中使用，在默认情况下，不加nonull，setter和getter都是可以为空nil的
   在定义属性中可以像如下一样使用：
```objc
<!-- more -->
//这样定义属性会得到以下的警告
// Pointer is missing a nullability type specifier (_Nonnull, _Nullable, or _Null_unspecified)
@property(nonatomic,strong)NSArray * array;
//以下属性值不能为空
@property(nonatomic,strong,nonnull) NSArray * array1;
@property(nonatomic,strong)NSArray * _Nonnull array2;
@property(nonatomic,strong)NSArray * __nonnull array3;
//以下属性值可以为空
@property(nonatomic,nullable)NSArray * array4;
@property(nonatomic,strong)NSArray * __nullable array5;
@property(nonatomic,strong)NSArray * _Nullable array6;
```
在方法中可以与如下示例一样使用：
```objc
//以下方法中参数不能为空，如若为空，系统会有警告提示
//nonnull ,_Nonnull,__nonnull 这三个关键字可以起到同样的效果，具体区别是什么呢？？？
-(nonnull NSString * )testString:(nonnull NSString  * )string;
-(NSString * _Nonnull)testString1:(NSString* _Nonnull)string;
-(NSString * __nonnull)testString2:(NSString*__nonnull)string;
//以下方法中参数，返回值可以为空
//__nullable,nullable ,_Nullable 这三个关键字可以起到同样的效果，具体区别是什么？？？
-(nullable NSString * )testString3:(nullable NSString  * )string;
-(NSString * _Nullable)testString4:(NSString* _Nullable)string;
-(NSString * __nullable)testString5:(NSString*__nullable)string;
```
在以下两个宏之间定义的所有对象属性都默认为nonull
```objc
NS_ASSUME_NONNULL_BEGIN
NS_ASSUME_NONNULL_END

```

```objc
@interface LocationDataController : NSObject
NS_ASSUME_NONNULL_BEGIN
//在这之间的所有属性的是非空的
@property (nonatomic, readonly) NSArray *locations;
@property (nullable, nonatomic, readonly) NSObject *latestLocation;
//方法里的参数默认也是非空的
- (void)addPhoto:(NSObject *)photo forLocation:(NSObject *)location;
- (nullable NSObject *)photoForLocation:(NSObject *)location;

NS_ASSUME_NONNULL_END
@end
```

## 关键字: null_resettable
getter :不可以为nil
setter :可以为nil
如果使用 null_resettable 就必须重写 getter或者setter方法. 目的是为了处理值为空的情况
使用方法如下
```objc
//如果使用下null_resettable，没有实现setter方法或getter 方法会得到如下的警告
// Synthesized setter 'setObjc:' for null_resettable property 'objc' does not handle nil
@property(nonatomic,strong,null_resettable)NSObject * objc;

```

## 关键字: _Null_unspecified
这个关键字作用感觉不是那么大，因为默认情况下，都是不确定
```objc
  //在不确定对象中是否为空的情况下可以使用null_unspecified关键字
  @property(nonatomic,null_unspecified)NSObject * objc1;
  @property(nonatomic)NSObject* _Null_unspecified objec2;
  @property(nonatomic)NSObject * __null_unspecified objec3;
```

## 泛型
通过使用泛型, 我们可以非常容易地获取其中的元素，并访问其特有的属性和方法, 一般使用在集合中使用(例如:数组,字典), 当方法调用的时候才有效果, 我们来看看如何使用:

```objc
//如下定义属性与方法会得到下面的警告
// Pointer is missing a nullability type specifier (_Nonnull, _Nullable, or _Null_unspecified)
@property(nonatomic,strong)NSMutableArray<NSString*> * mutableArray;
-(NSArray * )testArray:(NSArray * )array;
-(NSArray<NSString*>*)testArray2:(NSArray<NSString*>*)array;


//加上_Nonnull、_Nullable、_Null_unspecified这几个关键字中的一个，都不会出现以上警告
-(NSArray<NSString*>* __nonnull)testArray3:(NSArray<NSString*>*__nonnull)array;
-(__kindof NSArray<NSString*>* __nonnull)testArray4:(NSArray<NSString*>*__nonnull)array;
```
### 带泛型的容器
  有了泛型后可以指定容器类中对象的类型了
  ```objc
  NSArray<NSString *> * strings= @[@"sun",@"moon"];
  //这什么这样定义的数组不会报提示呢，它们的类型不是不一样的吗？？
  NSArray <NSString * >* string = @[@"su",@"fasd",@10,@{@10:@10}];

  NSMutableArray <NSString*>* array = [NSMutableArray array];

//如下加入一个NSNumber会得到以下提示
// Incompatible pointer types sending 'NSNumber *' to parameter of type 'NSString * _Nonnull'
  [array addObject:@(10)];
  //为什么我在泛型中指定值的类型为NSString，在实际给值中没有给string而是NSNumber，却没有给出提示信息，这是为什么呢？？
  NSDictionary<NSNumber *,NSString*> * dic =@{@100:@100};

//    指定字典键值类型
  NSMutableDictionary<NSString *,NSString*> * mDic =[NSMutableDictionary dictionary];
//    为什么在可变字典里，设置不同类型的键值，会有如下提示？
//    Incompatible pointer types sending 'NSNumber *' to parameter of type 'NSString * _Nullable'
  [mDic setValue:@10 forKey:@"age"];
  [mDic setObject:@10 forKey:@10];
  [mDic setValue:@"yuan" forKey:@10];
```
系统中常用的一系列容器类型都增加了泛型支持，甚至连 NSEnumerator 都支持了，这是非常 Nice 的改进。和 Nullability 一样，我认为最大的意义还是丰富了接口描述信息，对比下面两种写法：

```objc
@property (readonly) NSArray *imageURLs;
@property (readonly) NSArray<NSURL *> *imageURLs;
```
不用多想就清楚下面的数组中存的是什么，避免了 NSString 和 NSURL 的混乱。
### 自定义泛型类
比起系统的泛型容器，更好 玩的是自定义一个泛型类
```objc
@interface Stack<ObjectType> : NSObject
- (void)pushObject:(ObjectType)object;
- (ObjectType)popObject;
@property (nonatomic, readonly) NSArray<ObjectType> *allObjects;
@end

```
  这个 ObjectType 是传入类型的 placeholder，它只能在 @interface 上定义（类声明、类扩展、Category），如果你喜欢用 T 表示也 ok，这个类型在 @interface 和 @end 区间的作用域有效，可以把它作为入参、出参、甚至内部 NSArray 属性的泛型类型，应该说一切都是符合预期的。我们还可以给 ObjectType 增加类型限制，比如：
  ```objc
  // 只接受 NSNumber * 的泛型
  @interface Stack<ObjectType: NSNumber *> : NSObject
  // 只接受满足 NSCopying 协议的泛型
  @interface Stack<ObjectType: id<NSCopying>> : NSObject
```
若什么都不加，表示接受任意类型 ( id )；当类型不满足时编译器将产生 error。
![logo.jpg](imagesource/logo.jpg)
## 关键字： __kindof
表示当前类, 或者它的子类(__kindof使用: 放在类型前面, 表示修饰此类型)
__kindof 这修饰符还是很实用的，解决了一个长期以来的小痛点，拿原来的 UITableView 的这个方法来说：

```objc
- (id)dequeueReusableCellWithIdentifier:(NSString *)identifier;
```
使用时前面基本会使用 UITableViewCell 子类型的指针来接收返回值，所以这个 API 为了让开发者不必每次都蛋疼的写显式强转，把返回值定义成了 id 类型，而这个 API 实际上的意思是返回一个 UITableViewCell 或 UITableViewCell 子类的实例，于是新的 __kindof 关键字解决了这个问题：

```objc
- (__kindof UITableViewCell *)dequeueReusableCellWithIdentifier:(NSString *)identifier;
```
既明确表明了返回值，又让使用者不必写强转。再举个带泛型的例子，UIView 的 subviews 属性被修改成了：

```objc
@property (nonatomic, readonly, copy) NSArray<__kindof UIView *> *subviews;
```
这样，写下面的代码时就没有任何警告了：

```objc
UIButton *button = view.subviews.lastObject;
```
