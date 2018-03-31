---
title: 'const与define'
date: 2016-05-18 16:46:08
categories: iOS
tags:
- const 
- define
---
const和#define使用
--------------------------------------
>1.什么是const?
2.什么是#define?
3.有什么用？
4.区别是什么？
5.应该怎么用？

<!-- more -->
# 什么是const?
    const 是c/c++中的一个关键字（修饰符），const一般用来定义一个常量。该常量的值不能修改。
```objc
      const int var  = 100;
      int var1 = var;
      int var2 = var;
      var = 2000;//这样会报如下错误
    //    Cannot assign to variable 'var' with const-qualified type 'const int'
```
# 什么是#define?
  而define, 宏定义, 则是一条预编译指令, 编译器在编译阶段会将所有使用到宏的地方简单地进行替换
```objc
  #define kconst 1000
  int defvar1 = kconst;
  int defvar2 = kconst;
  /*
   编译后生成以下代码：
   int defvar1 = 1000;
   int defvar2 = 1000;
   */
```
# 有什么用？
  1. const和define都可以定义一个常量，都能实现只改值一次，就可以所有用上该常量的地方同步改值，一句代码也都不用改。
  2. 使代码更易维护
  3. 提高代码的效率


# 有什么区别？
  相同点：
    * 都能定义常量
  不同点:
    * const定义常量从汇编的角度来看，只是给出了对应的内存地址，而不是象#define一样给出的是立即数,  
    所以，const定义的常量在程序运行过程中只有一份拷贝，而#define定义的常量在内存中有若干个拷贝??????????
```objc
            const int var  = 100;
           #define kconst 1000
           int var1 = var;//此时为var分配内存，以后不在分配
           int var2 = var;//不再分配内存

           int defvar1 = kconst;//编译时进行宏替换，分配内存
           int defvar2 = kconst;//编译时进行宏替换，再分配内存
```
   * 编译器通常不为普通const常量分配存储空间，而是将它们保存在符号表中，这使得它成为一个编译期间的常量没有了存储与读内存的操作，使得它的效率比宏定义要高

>既然宏定义能做的事const都能做, 那宏还有什么存在的必要么?
宏能做到const不能办到的事.
  1.宏能定义函数
  2.OC的单例模式用到宏
  3.宏还能根据传入的参数生成字符串


# 该怎么用？
const有条原则，那就是他右边是什么，什么就不可变，如下：
```objc
        int a = 2;
        const int b = 100;//b 不可变
        int  const c = 200;// c 不可变，const和数据类型 位置可换
        int const *p1 =&a;//*p1 不可变，p1 可变
        int * const p2 = &a;//p2 不可变， *p2 可变
        const int * const p3 = &a;//p3 不可变，*p3不可变
```
const 不可变原则：
用const修饰的函数形参，则能提高代码的安全性，同时减少程序员之间的沟通成本
```objc
int add(const int * x,const int * y){
    //不能对x,y进行修改
    return *x + *y;
}
```
宏多用于条件编译，如需要对于不同的情况下执行不同的代码块，则可以使用宏的条件编译来进行判断

>在Objective-C中, 随处可见const常量, 所以大家应该大胆地使用const, 它会带给你大大的益处. 同时, 只要某个数据是定义之后永远都不需要也不能修改的, 请使用const!
