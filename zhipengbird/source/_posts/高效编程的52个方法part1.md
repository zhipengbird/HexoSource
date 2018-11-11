---
title: 高效编程的52个方法part1
date: 2018-11-09 12:11:20
tags:
- OC2.0
---
本部分主要了解OC的基础，总结几个比较高效的编程窍门
# 了解OC的起源
* OC为C语言添加了面向对象特性，是其超集。OC使用动态绑定的消息结构，也就是说，在运行时才会检查对象类型。接收消息后，究竟应执行何种代码，由运行环境而非编译器决定。
* OC语言使用用“消息结构”而非“函数调用” 
    >消息与函数调用的区别：
     >使用消息结构的语言，其运行时所应执行的代码由运行环境决定；而使用函数调用的语言，则由编译器决定。如果代码中调用的函数是多态的，那么在运行时就要按照“虚方法表”来查找到底应该执行哪个函数实现。而采用消息结构的语言，不论是否多态，总是在运行时才会去查找所要执行的方法。实际上编译器甚至不关心接收消息的对象是何种类型。接收消息的对象问题也要在运行时处理，其过程叫做“动态绑定(dynamic binding)”

# 在类的头文件中尽量少引入其他头文件
在头文件中尽量使用“向前声明”要引用的类
{% codeblock 向前声明  lang:objc   %}
@class XXXXclassName;
{% endcodeblock %}
将引入头文件的进机尽量延后，只在确有需要进再引入，这样可以减少类的使用者所需要引入的头文件数量。减少一定的编译时间。
向前声明也也解决了两个类相互引用的问题。
> 最佳实践：
> 1. 除非确有必要，否则不要引入在头文件中引入其他头文件。一般来说，应在某个类的头文件中使用向前声明来提及别的类，并在实现文件中引入那些类的头文件。这样做可以尽量降低类之间的耦合（coupling）
> 2. 有时无法使用向前声明，比如要声明某个类要遵守一项协议。这种情况下。尽量把“该类遵守某协议”这条声明移至实现文件中的分类中。如果不行的话，就把协议单独放在一个头文件中，然后将其引入。

# 多用字面量语法(语法糖)以，少用与之等价的方法
`NSString` `NSNumber` `NSArray` `NSDictionary`这几个类是我们最常使用的。我们可以使用"字面量语法(literal systax)"更方便快捷的创建这些对象，缩减代码长度，使其更易读。
## 字面数值
在需要把整数，浮点数，布尔值转成OC对象时，我们可以使用NSNumber类，该类可以处理多种类型的数值。以下两种方式是等价的，但后一种方式更为推荐使用
{% codeblock 常规方法  lang:objc  %}
NSNumber *value = [NSNumber nubmerWithInt:1];
{% endcodeblock %}
{% codeblock 使用字面量语法  lang:objc %}
NSNumber *value = @1;
{% endcodeblock %}
以字面量来表示数值十分有用。这样做可以令NSNumber对象更为整洁，因为声明中只有包含数值，没有多余的语法成份
## 字面量数组
数组为常用的数据结构。下面依次以数组的创建、取值来举例，以及注意事项为序来学习下
{% codeblock 常规创建数组的方式  lang:objc   %}
NSArray * animals = [NSArray arrayWithObjects:@"cat",@"dog",@"mouse",@"badger",nil];
{% endcodeblock %}
{% codeblock 使用语法糖创建  lang:objc   %}
NSArray *animas = @[@"cat",@"dog",@"mouse",@"badger"];
{% endcodeblock %}

{% codeblock 常规数组取值  lang:objc   %}
NSString *dog =  [animas objectAtIndex:1];
{% endcodeblock %}
{% codeblock 使用字面量语法  lang:objc   %}
NSString *dog = animas[1];
{% endcodeblock %}
"取下标"操作(subscripting)，与使用字面量语法的其他情况一样，这种方式更为简洁，更易理解。而且与其他语方依下标来访问数组元素时所用的语法类似。

**用字面量语法创建数组时要注意，若数组元素对象中有nil,则分抛出异常。因为字面语法实际上一种语法糖(synatactic sugar)，其效果等同于先创建一个数组，然后把方括号中的所有对象添加至数组中** 会抛出的异常会是这样的：
{% codeblock 异常  lang:objc   %}
*** Terminating app due to uncaught exception 'NSInvalidArgumentException', reason: '*** -[__NSPlaceholderArray initWithObjects:count:]: attempt to insert nil object from objects[1]'
{% endcodeblock %}

我们来看以下代码：
{% codeblock 常规创建数组，数组在有空元素的处理  lang:objc  %}
   id object1 = @"cat";
    id object2  = nil;
    id object3  = @"dog";
    
    NSArray *arrayA = [NSArray arrayWithObjects:object1,object2,object3, nil];
    NSLog(@"%@",arrayA);
    // cat 
{% endcodeblock %}
{% codeblock 以字面量创建数组，有对象为空  lang:objc %}
    id object1 = @"cat";
    id object2  = nil;
    id object3  = @"dog";
    NSArray *arrayB =@[object1,object2,object3];
    NSLog(@"%@",arrayB);
{% endcodeblock %}
Q:如果object1 和object3都指向了有效的对象，object2是nil,那么会出现什么情况呢？
A：按字面量创建数组B会抛出异常，数组A虽然可以创建出对象，但其中只有一个元素。原因在于`arrayWithObjects:`方法会依次处理各个参数，直到发现nil为止，由于oject2是nil,所以该方法会提前结束。
>这个微妙的差别表明，使用字面量语法更为安全。抛出异常令程序终止执行，比创建数组之后才发现少了元素要好，向数组中插入nil,通常说明程序有错，而通过异常可以更快发现这个错误

## 字面量字典
字典（Dictionary）也是一种映射型数据结构，可向其添加键值对。在开发过程中使用字典的频率很高。
{% codeblock 常规方法创建字典  lang:objc   %}
 NSDictionary *person = [NSDictionary dictionaryWithObjectsAndKeys:@"Mat",@"firstName",@"Galloy",@"lastName",[NSNumber numberWithInt:28],@"age", nil];
{% endcodeblock %}
{% codeblock 使用语法糖创建字典  lang:objc %}
 NSDictionary *person2 = @{@"firstName":@"Mat",
                              @"lastName":@"Galloy",
                              @"age":@(28)
                              };
{% endcodeblock %}
第一种写法令人迷惑，因为其顺序是<对象><键><对象><键>。这与通常理解的顺序相反，我们一般认为把“键”映射到“对象”。
第二种写法更简明，而且键出现在对象之前，理解更为顺畅。
> 与数组一样，用字面量语法创建字典也有个问题，就是一旦有值为nil,便出抛出异常。

## 可变数组与字典
通过取下标操作，可以访问数组中某个下标或字典中某个键值所对应的元素。如果数组与字典对象是可变的，那么也可以通过下标修改其中的元素值。
{% codeblock 常规方法修改元素值  lang:objc  %}
[mutableArray replaceObjectAtIndex:1 withOject:@"dog2"];
[mutableDictionar  setObject:@"huahua" forKey:@"lastName"];
{% endcodeblock %}
{% codeblock 使用语法糖  lang:objc  %}
mutableArray[1] = @"dog2";
mutableDictionary[@"lastName"] = @"huahua";
{% endcodeblock %}

## 局限性
字面面语法有个小小的限制，就是除了字符串外，所创建出来的对象必须属于Foundation框架才行。如果自定义这些类的子类，则无法使用字面量语法创建其对象。

>总结
> 应该使用字面量来创建字符串、数值、数组、字典。与常规方法相比，这么做更为简明扼要。
> 应该通过取下标操作访问数组下标或字典中的键所对应的元素
> 用字面量创建数组或字典时，若值中有nil，则会抛出异常。因此，务必确保值不为nil。

# 多用类型常量，少用#define预处理指令
编写代码时经常要定义常量，这时你会怎么做？会是像下面一样？
{% codeblock 使用宏定义常量  lang:objc  %}
#define ANIMATION_DURATION 0.3
{% endcodeblock %}
这可能是你想要的效果。但是这样定义出来的常量没有类型信息。另外，预编译过程会把所有`ANIMATION_DURATION`替换成`0.3`,这样一来，假设指令声明在头文件中，那么所有引入这个头文件的代码，其`ANIMATION_DURATION`都会被替换。
要解决此类问题，我们需要利用编译器的某些特性，如下面这种方式
{% codeblock 使用静态常量  lang:objc   %}
static const NSTimeInterval kAnimationDuration = 0.3;
{% endcodeblock %}
这种方式定义的常量包含类型信息，其好处是清楚地描述了常量的含义。有助于为其编写开发文档。如果要定义许多常量，那么这种方式能为后面阅读代码的人更容易理解其意图。

>命名注意事项
> 1. 若常量局限于“某编译单元”（即实现文件类）则在其前面加上字母`k`;
> 2. 若常量在类之外可见，则通常以类名为前缀

在开发过程中我们都有喜欢在头文件中声明预处理指令的习惯，这并不是一个好的习惯。当常量名有可能相互冲突时更是如此。如`ANIMATION_DURATION`这个常量就不应该放在头文件中，当所有引入该文件的其他文件都会出现这个名字，就连`static const`定义的常量也不应该放在头文件中。在OC里没有命名空间这一说，这样做就等同于声明一个`kAnimationDuration`的全局变量。
在不打算公开某个常量时，应将其放在实现文件中。

**Q：变量为何一定要同时用static和const来声明？**
A：
1. 如果试图修改由const修饰符所声明的变量，那么编译器会报错。
2. static修饰符则意味着该变量仅在定义此变量的编译单元可见。
3. 如果一个变量同时声明为static、const,那么编译器根本不会创建符号，而会像#define预处理命令一样，把所有遇到的变量都替换成常量值。**这种方式定义的常量带有类型信息**

**Q：如何定义一个对外公开的常量**
如在代码中调用`NSNotificationCenter`以通知他们。用一个对象来派发通知，令其他欲接收通知的对象向该对象注册。这样就能实现此功能了。派发通知时，需要使用字符串来表示此项通知的名称，而这个名字就可以声明为一个外界可见的常值变量（constant variable）,这样注册者无须知道实际字符串的值，只需以常值变量来注册自己想接收的通知即可。
此类变量需要入在“全局符号表(global symbol table)”中，以便可以在定义该常量的编译单元之外使用
{% codeblock 定义一个外界可见的常量  lang:objc   %}
///In the header file
extern NSString *const QMPlayerDidStopNotification;
///In the implement file
NSString *const QMPlayerDidStopNotification = @"PlayerDidStop";
{% endcodeblock %}
这种常量在头文件中`声明`，在实现文件中`定义`. 注意`const`修饰符在常量类型中的位置。常量定义应从右至左解读。
> QMPlayerDidStopNotification是一个常量，而这个常量是一个指针，指向NSString对象

编译器看到头文件中的`extern`关键字，就能明白如可在引入该头件的代码中处理该常量了。`extern`关键字千诉编译器，在全局符号表中将有一个 `QMPlayerDidStopNotification`符号。 这类常量必须要定义，而且只能定义一次。通常将其定义在与声明该常量的头文件相关的实现文件里。由实现文件生成目标文件时，编译器会在“数据段(data section)”为字符串分配存储空间。链接器会把此目标文件与其他目标文件相链接，以生成最终的二进制文件。
> 命名注意事项
> 因符号在全局符号表中，所以命名要格外小心。为避免冲突，最好是用与之相关的类名做前缀。

> 总结
> 1. 不要使用预处理指令定义常量
>       这样定义出来的常量不含类型信息，编译器只是会在编译前据引执行查找和替换操作。即使有人重新定义了常量值，编译器也不会产生警告。这将导致各序的常量值不一致
> 2. 在实现文件中使用static const 来定义“只在编译单元可见的常量（translation-unit-specific constant)”.由于此类常量不在全局符号表中，所有无须为其添加前缀
> 3. 在头文件中使用extern来声明全局常量，并在相关的实现文件中定义其值。这种常量要出现在全局符号表中，所以其名称要加以区分，通常使用与之相关的类名做前缀

# 用枚举表示状态、选项、状态码
由于OC基于C语言，所以C语言有的功能它都有，其中之一就是枚举类型：`enum`,系统框架中使频繁使用到此类型，然而我们容易忽视它。在以一系列常量来表示错误状态码或可组合的选项时，极宜使用枚举为其命名。
枚举只是一种常量的命名方式。某个对象所经历的各种状态就可以定义为一个简单的枚举集（enumeration set）
{% codeblock AVPlayer播放状态  lang:objc   %}
typedef NS_ENUM(NSInteger, AVPlayerStatus) {
	AVPlayerStatusUnknown,
	AVPlayerStatusReadyToPlay,
	AVPlayerStatusFailed
};
{% endcodeblock %}
由于每个状态都用一个便于理解的值表示，所以这样写出来的代码更易懂。编译器会为枚举分配一个独有的编号，从0开始，每个枚举递增1。实现枚举所用的数据类型取决于编译器，不过其二进制位的个数必须完全表示下枚举编号才行。在上述例子中由于最大编号是2，所以使用1个字节的char类型就行（*一个字节含8个二进制位，所以最多可以表示256种枚举的枚举变量*）*我们也可以手动设置枚举的值*

还有另外一种情况我们应该使用枚举，就是定义选项的进候。若这些选项可以彼此组合，则更应如此。只要枚举定义的对，各选项间就可以通过“按位或操作符”来组合。如：
{% codeblock UIView的autResizing选项  lang:objc   %}
typedef NS_OPTIONS(NSUInteger, UIViewAutoresizing) {
    UIViewAutoresizingNone                 = 0,
    UIViewAutoresizingFlexibleLeftMargin   = 1 << 0,
    UIViewAutoresizingFlexibleWidth        = 1 << 1,
    UIViewAutoresizingFlexibleRightMargin  = 1 << 2,
    UIViewAutoresizingFlexibleTopMargin    = 1 << 3,
    UIViewAutoresizingFlexibleHeight       = 1 << 4,
    UIViewAutoresizingFlexibleBottomMargin = 1 << 5
};
{% endcodeblock %}
每个选项均可启用或禁用，使用上述方式定义的枚举值即可保证这一点，因为每个枚举值所对应的二进制位中只有一个二进制位的值是1。用“按位或操作符”可以组合多个选项。用“按位与操作符”即可判断出是否已启用某个选项。
{% codeblock 判断某个选项是否启用  lang:objc   %}
   UIViewAutoresizing resizing = UIViewAutoresizingFlexibleWidth|UIViewAutoresizingFlexibleHeight;
    if (resizing&UIViewAutoresizingFlexibleWidth) {
        ///是否启用UIViewAutoresizingFlexibleWidth
    }
{% endcodeblock %}
> 凡是需要以按位或操作来组合的枚举都应使用`NS_OPTIONS`定义。若枚举不需要进行组合，则应用`NS_ENUM`来定义。

枚举在switch中的使用。
我们习惯在switch语句中加上default分支。然而使用枚举来定义状态机，则最好不要使用default分支，这样如果稍后增加一种状态，那么编译器就会发出警告，提示新加入的状态未在switch分支中处理。若使用了default分支，那么这就会处理这个新的状态，从而编译器不会发出警告信息。

>总结
>1. 应用枚举来表示状态机的状态，传递给方法的选项以及状态码等值，给这个值取个易懂的名字
>2. 如果把传递给某个方法的选项表示为枚举，而多个选项又可同时使用，那么将各选项的值定义为2的幂，以便通过按位或操作将其进行组合
>3. 用`NS_ENUM`与`NS_OPTIONS`宏来定义枚举类型，并指明其底层数据类型。这样做可以确保枚举是用开发者所选的底层数据类型实现出来的，而不会采用编译器所选的类型。
>4. 在处理枚举类型的switch语句中不要实现default分支。这样在加入新的枚举后，编译器会提示开发者：switch语句中有未处理的枚举选项。 


