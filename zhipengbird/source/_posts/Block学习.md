---
title: Block学习
date: 2016-08-28 10:21:29
tags:
---
# Blocks概要
## 什么是Blocks
Blocks是Ｃ语言的扩充功能。有一句话来表示Blocks的扩充功能：带有自动变量（局部变量）值的匿名函数。
 > "匿名函数"：不带名称的函数

<!-- more -->
1. Ｃ语言中函数的声明与使用
```c
int function(int count);//声明名为func的函数
int res = function(10);//使用函数名称调用
```
2. Ｃ中使用函数指针调用函数
```c
  int (* funptr)(int) = &function;//定义函数指针，取得函数的地址
  int res = (* funptr)(10);//通过函数指针调用函数
```
通过Blocks，源代码中就能够使用匿名函数。
>对于开发人员而言，命名是工作的本质，函数名、变量名、方法名、属性名、类名和框架名等都必须具备。能够编写不带名称的函数对研发人员相当具有吸引力。

3. 回顾Ｃ语言的函数中可能使用的变量
  * 自动变量（局部变量）
  * 函数的参数
  * 静态变量（静态局部变量）
  * 静态全局变量
  * 全局变量  
  其中，在函数的多次调用之间能够传递值的变量有：
  * 静态全局变量
  * 静态变量（静态局部变量）
  * 全局变量  
  这些变量的作用域不同，但在整个程序当中，一个变量总保持在一个内存区域。因此，当多次调用函数时，该变量值总能保持不变，在任何时候以任何状态调用，使用的都是同样的变量值。

“带有自动变量值的若名函数”这一概念并不仅指Blocks，它还存在其他程序语言中。在计算机科学中，称为“闭包（Closure）”、lambda计算 等；
如下表：

| 程序语言      | Block名称    |
| :------------- | :------------- |
| C+Block      | Block      |
| SmallTallk      | Block      |
| Ruby      | Block      |
| LISP      | lambda      |
| Python      | lambda      |
| C++11      | lambda      |
| javascript      | Anonymous function      |



# Blocks模式
## Block语法
block块：
```objc
//使用block 定义一个加操作
^(int a,int b){
       NSLog(@"%d",a+b);
   };
// 其完整的形式是
^void(int a,int b){
       NSLog(@"%d",a+b);
   };
```
C语言函数
```c
//定义一个加方法
void add(int a,int b){
    NSLog(@"%d",a+b);
}
```
将上面两个代码进行比较，我们发现完整的Block语法与一般的Ｃ语言函数相比，有以下同点：
  * 没有函数:Block是匿名函数
  * 带有"^": 返回值前带有“^”(插入记号).  

1. Block的完整语法
```
  ^ 返回值类型 (参数列表){表达式};
```
示例：
```objc
//定义一个加方法
int  add(int a,int b){
    return a+b ;
}
```
>"返回值类型"同Ｃ语言函数的返回值类型，“参数列表”同Ｃ语言的参数列表，“表达式”同Ｃ语言函数中允许使用的表达式。当然与Ｃ语言函数一样，表达式中含有return语句时，其类型必须与返回值类型相同。

2. 省略返回值类型
```
    ^ 返回值类型 (参数列表){表达式};
==> ^ (参数列表){表达式};
```
示例：  
```objc
^(int a,int b){
      NSLog(@"%d",a+b);
  };
  ^(int a,int b){
      return a+b;
  };
```
> 省略返回值类型，如果表达式中有return 语句就使用该返回值类型，如果表达式没有return语句，使用void类型。表达工中含有多个return语句时，所有return的返回值类型必须相同。

3. 省略返回值类型和参数列表
>如果不使用参数，参数列表也可以省略  

  ```
  ^ 返回值类型 (参数列表){表达式};
  ==> ^{表达式};
  ```
  示例：
  ```objc
  ^{
         //TODO:: do something
         NSLog(@"hello");
     };

  ^{
       //TODO::do something
      return @"hello";
   };
  ```

## Block类型变量
>从Block语法的表述方式上来看，除了没有名称及带有“^”以外，其他都与Ｃ语言函数定义一样。在定义Ｃ函数时，可以将所定义的函数地址赋值给函数指针类型变量中。

```c
// 定义名为add的一个加法
int add(int add1,int add2){
  return add2+add1;
}
int (*funcPtr)(int,int)=&add;//定义函数指针类型变量，并将变量指向add函数的地址
```
同样地，在Block语法下，可以将Block语法赋值给声明为Block类型的变量中。
声明Block类型变量示例：

```objc
int (^add)(int add1,int add2);
```
对比函数指针的定义，会发现声明Block类型变量仅仅是将声明函数指针类型变量的“＊”变成了“^”.Block类型变量与一般Ｃ语言变量完全相同，可以作以下用途使用：
* 自动变量
* 函数参数
* 静态变量
* 静态全局变量
* 全局变量


使用Block语法将Block赋值为Block类型变量：
```objc
int (^add)(int add1,int add2)=^(int add1,int add2){
       return add1+add2;
   };
```
由“^”开始的Block语法生成Block被赋值给变量add中，Block变量与一般的变量一样，所以，也可以由Block类型变量向Block类型变量赋值。
```objc
    int (^add1)(int,int)= add;
    int (^add2)(int,int);
    add2= add1;
```
在函数参数中可以使用Block变量向函数传递Block:
```objc
void addFunc(int (^add)(int)){
    add(12);
}
```
在函数返回值中指字Block类型，可以将Block作为函数的返回值返回：
```objc
int (^func())(int) {
    return^(int count){
        return count+1;
    };
};
```
使用typedef简化Block的声明和使用：
```objc
// typedef 的语法规则
typedef <#returnType#>(^<#name#>)(<#arguments#>);
```
通过typedef,我们将上述函数中使用的Block,和函数返回值指定Block返回类型重写：
```objc
typedef int (^addBlock)(int);//定义一个block类型
// 在函数参数中传递block
void addFunc(addBlock add) {
  add(12);
}
// 返回block类型返回值
addBlock func(){
    return ^(int number){
        return number+1;
    };
};
```
> 在Ｃ语言中可以使用函数指针，那么block是否也可以使用Block指针类型变量呢？
答案：那是肯定的 。block类型变量可以像Ｃ语言中其类型变量一亲使用。

```objc
typedef int (^pointBlock)(int);
pointBlock block = ^(int count){
  return count+1;
};
pointBlock * pointB= &block;
(*pointB)(10);
```

## 截获自动变量值
> 通过前面的block语法和block类型变量的说明，我们已经知道，block是“带有自动变量值的匿名函数”，与Ｃ语言函相比中，我们对匿名函数的理解会更加深入，“带有自动变量值”在block中是怎么样的表现形式？＝＝＝> “截获自动变量值”

```objc
  int val = 100;
  const char *fmt = "val = %d\n";
  void (^block)() =^{
    printf(fmt,val );
  };
  val = 1000;
  fmt ="val value was changed .val = %d\n";
  block();
```
**Block语法的表达式中使用的是在它之前声明的自动变量fmt和val.在Block中Block表达式截获所使用的局部变量的值，即何存该局部变量的瞬间值。因为Block表达式保存了局部变量的值，所以在执行Block语法后，改动block表达式中使用的局部变量的值也不会影响Block执行时局部变量的值**


## `__block`说明符
>局部变量值截获只能保存执行Block语法瞬间的值，保存后不能改写该值

尝试改写截获的局部变量的值：
```objc
int val = 100;
void (&block)()=^ {
  val = 10000;//给局部变量赋上新值
};
block();
printf("val = %d\n",val );
```
尝试编译该段代码，Xcode6会给一个大大的红色错误警告，警告内容为：`  Variable is not assignable (missing __block type specifier)`  

若想在Block语法的表达式中将值赋值给在Block语法外声明的局部变量，需要在自动变量上附加`__block`修饰符。将上述代码进行改正后如下：
```objc
__block int val = 100;
void (^block)()=^ {
  val = 10000;//给局部变量赋上新值
};
block();
printf("val = %d\n",val );
```
该代码段可以正常运行。
>使用附有 `__block`修饰符的局部变量可以Block中赋值。该变量称为`__block`变量


## 截获的自动变量
在前面__block修饰符一小节中，我们了解到，在没有加__block修饰符，直接去修改block语法表达式中截获的自动变量的值时，会产生编译错误。那么截获OC对象会产生编译错误吗？
```objc
NSMutableArray *array = [NSMutableArray array];
void (^block)()=^  {
  id obj = [[NSObject alloc]init];
  [array addObject:obj];
}
block();
```
代码编译运行没有问，若向变量赋上新的值就会产生编译错误了，代码如下：
```objc
NSMutableArray *array = [NSMutableArray array];
void (^block)()=^  {
  array = [NSMutableArray array];

}
block();
```
上述代码同样会产生`//  Variable is not assignable (missing __block type specifier)`错误提示
解决办法就是在array前添加__block修饰符
```objc
__block NSMutableArray *array = [NSMutableArray array];
void (^block)()=^  {
  array = [NSMutableArray array];
}
block();
```


使用C语言数组时的指针的坑：
```c
const char text[]= "hello";
void (^block)() =^  {
printf("%c\n", text[2]);
};
```
**使用Ｃ语言的字符串数组，而没有向截获的自动变量赋值 肯定没有什么问题？**
编译代码后得到：`Cannot refer to declaration with an array type inside block`错误提示
因为现在block中，截获的自动变量的方法并没有实现对Ｃ语言数组的截获。使用指针可以解决这个问题
```c
const char *text = "hello";
void (^block)() =^  {
printf("%c\n", text[2]);
};
```


# Blocks实现
## Block的实质
>Block是带有自动变量值的匿名函数，但block究竟是什么？

Clang(LLVM编译器)具有转换为可读源码的功能。通过“-rewrite-objc”选项能将Block语法的源码转成C++源码（struct结构体）。
使用方法：
```c
clang -rewrite-objc 源码文件名
```
以最为简单的block代码为例：
```objc
#include"stdio.h"
int main(){
    void(^block)()=^{
        printf("hello");
    };
    block();
    return 0;
}
```
将以上代码Clang成源码：
```objc
struct __block_impl {
    void *isa;
    int Flags;
    int Reserved;
    void *FuncPtr;
};
struct __main_block_impl_0 {
  struct __block_impl impl;
  struct __main_block_desc_0 * Desc;

  __main_block_impl_0(void *fp, struct __main_block_desc_0 *desc, int flags=0) {
    impl.isa = &_NSConcreteStackBlock;
    impl.Flags = flags;
    impl.FuncPtr = fp;
    Desc = desc;
  }
};
static void __main_block_func_0(struct __main_block_impl_0 *__cself) {
    printf("hello");
}

static struct __main_block_desc_0 {
  size_t reserved;
  size_t Block_size;
} __main_block_desc_0_DATA = { 0, sizeof(struct __main_block_impl_0)};

int main(){
void(*block)()=((void ( *)())&__main_block_impl_0((void *)__main_block_func_0, &__main_block_desc_0_DATA));
((void ( *)(__block_impl * ))((__block_impl *)block)->FuncPtr)((__block_impl *)block);
return 0;
}
static struct IMAGE_INFO { unsigned version; unsigned flag; } _OBJC_IMAGE_INFO = { 0, 2 };
```
接下来我们来细读下这段代码：
最初的block语法：
```objc
    ^{
      printf("hello");
    };
```
转换后：
```objc
static void __main_block_func_0(struct __main_block_impl_0 *__cself) {
    printf("hello");
}
```
由上述代码所示，Block使用的匿名函数实际上被作为简单的C语言函数来处理。另外，根据Block语法所属的函数名（这里是main）和Block语法在该函数出现的位置顺序值来给经clang变换的函数命名。

### 了解clang源码中的结构体
  先看该Ｃ函数的参数的声明：
  ```c
  struct __main_block_impl_0 *__cself
  ```
  参数`__cself`是`__main_block_impl_0`结构体指针，该结构体的声明如下：
  ```c
  struct __main_block_impl_0 {
    struct __block_impl impl;
    struct __main_block_desc_0 * Desc;
  };
  ```
  为简单明了，此处将构造函数给去了。第一个成员变量是`impl`,是`__block_impl`的一个变量，其结构体的声明如下：
  ```c
  struct __block_impl {
      void *isa;
      int Flags;
      int Reserved;
      void *FuncPtr;//函数指针
  };

  ```
  第二个成员变量是`Desc`指针，是`__main_block_desc_0`结构体的一个变量，其声明如下：
  ```c
  static struct __main_block_desc_0 {
    size_t reserved;//保留区域
    size_t Block_size;//Block的大小
  };
  ```
  接下来我们来看下初始化含有这些结构体的`__main_block_impl_0`结构体构造函数:
  ```c
  __main_block_impl_0(void *fp, struct __main_block_desc_0 *desc, int flags=0) {
    impl.isa = &_NSConcreteStackBlock;
    impl.Flags = flags;
    impl.FuncPtr = fp;
    Desc = desc;
  }
  ```
以上是初始化`__main_block_impl_0`结构体成员的源码，`_NSConcreteStackBlock`用于初始化`__block_impl`结构体的`isa`成员，在后面会对`_NSConcreteStackBlock`进行讲解。

### 调用结构体构造函数
  以下是`__main_block_impl_0`结构体构造函数的调用：
  ```c
  void(*block)()=((void (*)())&__main_block_impl_0((void *)__main_block_func_0, &__main_block_desc_0_DATA));
  ```
  什么鬼，看不懂，分解他：
  ```c
  struct __main_block_impl_0 temp =__main_block_impl_0(__main_block_func_0,&__main_block_desc_0_DATA);//生成`__main_block_impl_0`结构体实例的变量
  struct __main_block_impl_0 *blk = &temp;//将生成的实例变量赋值给结构体指针
  ```
  上述代码对应的源码：
  ```objc
  void(^block)()=^{
         printf("hello");
     };
  ```
  > 将block语法生成的block赋给block类型的变量block，等同于将`__main_block_impl_0`结构体实例的指针赋给变量blk.该源码中的Block就是`__main_block_impl_0`结构体类型的自动变量blk.

  ### 深入`__main_block_impl_0`构造函数
  构造函数：
  ```objc
  __main_block_impl_0(__main_block_func_0,&__main_block_desc_0_DATA);

  ```
第一个参数是由Block语法转换成Ｃ语言函数指针。第二个参数是作为静态全局变量初始化的`__main_block_desc_0`结构体实例指针。
以下为`__main_block_desc_0`结构体实例化部分代码：
```c
static struct __main_block_desc_0   __main_block_desc_0_DATA = {
  0,
  sizeof(struct __main_block_impl_0)
};
```
该源码中使用Block,即__main_block_impl_0结构体实例的大小进行初始化。

### 展开后的`__main_block_impl_0`
我们来看下栈上的`__main_block_impl_0`结构体实例如何根据参数进行初始化。将结构体中的`__block_impl`展开，会是如下形式：
```C
struct __main_block_impl_0{
  void *isa;
  int Flags;
  int Reserved;
  void *FuncPtr;
  struct __main_block_desc_0 * Desc;
}
```
该构造函数会像下面一样进行初始化：
```C
isa= & _NSConcreteStackBlock;
Flags= 0;
Reserved= 0;
FuncPtr= __main_block_func_0;
Desc= & __main_block_desc_0_DATA;

```
### block的调用
说了这么久，还没有看到block的调用，接下来我们分析下block的调用
```objc
   block();
```
转换后的源码：
```objc
((void ( * )(__block_impl *))((__block_impl *)block)->FuncPtr)((__block_impl *)block);
```
转换部分太多太杂，去掉他：
```objc
（*block->impl.FuncPtr）(block);
```
这就是简单的使用函数指针调用函数。正如我们所确认的,由Block语法转换的`__main_block_func_0`函数的指针被赋值给成员变量`FuncPtr`.另外，`__main_block_func_0`的参数`__cself`指向`Block`。在调用该函数的源代码中可以看出block正是作为以数进行了传递。

###  解密`isa`
在刚才我们看到这样的一句代码：
 ```objc
 isa= &_NSConcreteStackBlock;
 ```
 将Block指针赋给Block的结构体成员变量`isa`.要理解`isa`是何方神圣，我们需要先了解下OC的类和对象的实质。Block就是OC对象。
 我们将一个简单person类，clang下它的源码：
 ```objc
 @interface Person : NSObject
{
    int a ;
    int age;
}
@end
 ```
 clang后的代码，我将分散的代码集中到一块了：
 ```objc
 #ifndef _REWRITER_typedef_Person
#define _REWRITER_typedef_Person
typedef struct objc_object Person;
typedef struct {} _objc_exc_Person;
#endif

struct objc_object {
    Class isa __attribute__((deprecated));
};
typedef struct objc_object *id;

//这个是在runtime.h文件中找到的
struct objc_class {
    Class isa  OBJC_ISA_AVAILABILITY;
};

typedef struct objc_class *Class;
struct NSObject_IMPL {
    Class isa;
};

struct Person_IMPL {
	struct NSObject_IMPL NSObject_IVARS;
	int a;
	int age;
};

```
“id”类型变量主要用于存放OC对象。我们可以像使用 `void *` 那样使用id.由上述clang中的代码我们发现id的声明如下：
```objc
  typedef struct objc_object *id;
```
`id`是`objc_object`结构体的一个指针类型变量，`objc_object`其声明如下：
```objc
  struct objc_object {
      Class isa __attribute__((deprecated));
  };
```
该结构体中只有一个`isa`成员变量，`Class`又是什么呢？
```objc
typedef struct objc_class *Class;
```
`Class`是`objc_class`的指针类型变量，`objc_class`又是什么东西，在runtime.h文件中，我找到了如下代码：
```objc
//这个是在runtime.h文件中找到的
struct objc_class {
    Class isa  OBJC_ISA_AVAILABILITY;
};
```
与`objc_object`结构体相对比，我们发现，它们的结构体成员都是一样的。`objc_object`和`objc_class`结构体都是各个对象和类的实现中使用的最基本的结构体。

我们回过头来看下，刚才声明`Person`类：
```objc
@interface Person : NSObject
{
   int a ;
   int age;
}
@end
```
clang转换后的结构体为：
```objc
struct Person_IMPL {
	struct NSObject_IMPL NSObject_IVARS;
	int a;
	int age;
};
```
将结构体中使用到的`NSObject_IMPL`结构体：
```objc
struct NSObject_IMPL {
    Class isa;
};
```
展开后，得到：
```objc
struct Person_IMPL {
  Class isa;
	int a;
	int age;
};
```
类中的a ,age实例变量被直接转换成结构体的成员。
“OC中由类生成的对象”意味着，像该结构体“生成由该类生成的对象的结构体实例”。生成的各个对象（生成由该类生成的对象的结构体实例）通过成员变量isa保持该类的结构体实例指针。

> 各类的结构体是基于`objc_class`结构体的`class_t`结构体。`class_t`结构体在objc4运行库中的声明如下：[runtime](https://opensource.apple.com/source/objc4/objc4-437/runtime/objc-runtime-new.h)
```objc
typedef struct class_t {
    struct class_t *isa;
    struct class_t *superclass;
    Cache cache;
    IMP *vtable;
    class_rw_t *data;
} class_t;
```
在ＯＣ中，如NSMutableArray的class_t实例和NSObject的class_t实例，均生成并保持各个类的class_t结构体实例，该实例持有声明的成员变量，方法的名称，方法的实现（函数指针），属性及父类的指针，并被OC 运行时库使用。

回到__main_block_impl_0结构体：
```C
struct __main_block_impl_0{
  void *isa;
  int Flags;
  int Reserved;
  void *FuncPtr;
  struct __main_block_desc_0 * Desc;
}
```
该__main_block_impl_0结构体相当于基于objc_object结构体的OC类对象的结构体。对成员变量isa的初始化如下：
```objc
isa =&_NSConcreteStackBlock;
```
`_NSConcreteStackBlock`相当于class_t结构体实例。在将Block作为OC对象处理里，关于该类的信息放_NSConcreteStackBlock中。

> Block 实质就是OC对象。





## 截获自动变量值
## `__block`说明符
## Block存储域
##  `__block`变量存储域
##  截获对象
## `__block`变量和对象
## Block循环引用
## copy/release
