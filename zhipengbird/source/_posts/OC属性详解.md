---
title: 编写高质量代码的有效方法(6)-理解“属性”
date: 2018-11-17 11:16:43
tags:
- iOS
- 
---

在OC等面向对象语言编程时，“对象”就是“基本构造单元”，开发者可以通过对象来存储并传递数据。在对象之间传递数据并执行任务的过程就叫做“消息传递”。要编写出高效且易维护的代码。就一定要熟悉这两个特性的工作原理。
当应用程序运行起来以后，为其提供相关支持的代码叫“OC运行时环境”（objective-c runtime)。它提供了一些使得对象间能够传递消息的重要函数，并且包含创建实例所用的全部逻辑。在理解了运行环境中种个部分协同工作的原理后，开发水平会更上一层。

# 属性
> 属性（property）是OC的一项特性，用于封装对象中的数据。

属性（property）是OC的一项特性，用于封装对象中的数据。OC对象通常会把其所需要的数据保存为各种实例变量。实例变量一般通过“存取方法”来访问。其中“获取方法（getter）”用于读取变量值，而“设置方法(Setter)”用于写入值。OC2.0中“属性”成为OC的一种特性，开发者阿以令编译器自动编写与属性相关的存取方法。此特性引入了一种新的“点语法”，使用开发者可以更为容易地对类对象来访问存放于其中的数据。
我们来看下下面示例：
{% codeblock 作用域  lang:objc   %}
@interface Person : NSObject
{
    @public
    NSString *_firstName;
    NSString *_lastName;
    @private
    NSString *_age;
}
@end
{% endcodeblock %}

这种写法是java或者C++中常用的写法。用`@public` 、`@private` 来给变量加上作用域。在OC中很少这么做。为什么呢？ 这种写法存在以下问题：对象布局在编译期就已固定了，只要碰到`_firstName`变量的代码，编译器就把其替换成偏移量（offset）。这个偏移量是硬编码，表示该变量距离存放对象的内存区域的起始地址有多远。
那么在原有基础上再增加一个实例变量会有什么问题呢？
{% codeblock 增加实例变量  lang:objc   %}
@interface Person : NSObject
{
    @public
    NSDate * _dateOfBirth;
    NSString *_firstName;
    NSString *_lastName;
    @private
    NSString *_age;
}
@end
{% endcodeblock %}

原来表示`_firstName`的偏移量现在指向了`_dateOfBirth`.把偏移量硬编码于其中的那些代码都会读取到错误的值。
如下布局表

|  |Person ||  |Person|
|------|------|-----|------|-------|
|+0|_firstName||+0|_dateOfBirth|
|+4|_lastName||+4|_firstName|
|+8|_age||+8|_lastName|
||||+12|_age|

若代码使用了编译时期计算出来的偏移量，那么在修改类定义之后必须要重新编译，否则就会出错。
**OC是如何处理这种问题的呢？**
OC的做法是，把实例变量当做一种存储偏移量所用的“特殊变量”，交由“类对象”保管。偏移量会在运行期查找，如果类的定义发生了改变，那么存储的偏移量也就变量。这样无论何时访问实例变量，总能使用正确的偏移量。甚至可以在运行时向类新增实例变量。这就是稳固的“应用程序二进制接口（Application Binary Interface,ABI）”.ABI 定义了许多内容，其中一项就是生成代码时所遵循的规范。有了这种ABI，我们就可以在分类或都实现文件中定义实例变量了。

最好的解决方法是使用OC的属性。。在对象接口的定义中，使用属性是一种标准的写法，能够访问封装在对象里的数据。因此，也可把属性当作一种简称。其意思是说：“编译器会自动写出一套存取方法，用以访问给定类型中具有给定名称的变量。”
以下两种方式是等效的
{% codeblock 使用属性代替变量  lang:objc   %}
@interface Person : NSObject
@property (nonatomic,copy) NSString *firstName;
@property (nonatomic,copy) NSString *lastName;
@end

@interface Person : NSObject
- (NSString*)firstName;
- (void)setFirstName:(NSString*)firstName;
- (NSString*)lastName;
- (void)setLastName:(NSString*)lastName;
@end
{% endcodeblock %}
***如何访问属性？***
要访问属性，可以使用“点语法”，在纯C中，要想访问分配在栈上的结构体里的成员，也需要使用点语法。编译器会把“点语法”转换为对存取方法的调用。使用点语法的效果与直接调用存取方法相同。
以下是点语法与直接调用存取方法的示例：
{% codeblock 点语法与存取方法等效  lang:objc   %}
        Person * person = [[Person alloc] init];
        
        person.firstName = @"pinghua";
        //与下述方式效果一致
        [person setFirstName:@"pinghua"];
        
        NSString *firstName = person.firstName;
        //与下述方式效果一致
        NSString *firstName2 = [person firstName];
{% endcodeblock %}

**使用属性的其他优势**
1. 在对象接口中使用属性，那么编译器就会自动编写访问这些属性所需的方法。此过程叫“自动合成”（autosynthesis）.**这个过程由编译器在编译期执行，所以在编辑器中看不到这些合成方法的源码**
2. 编译器还会自动向类中添适当类型的实例变量，并在属性名前加下划线，以此作为实例变量的名字。
3. 可以在类的实现代码里通过`@synthesize`语法来指定实例变量的名字
    {% codeblock 指定变量名  lang:objc   %}
        @implementation Person
        //使用@synthesize指定变量的名字
        @synthesize firstName = _myFirstName;
        @synthesize lastName = _myLastName;
        @end
    {% endcodeblock %}
4. 在不想让编译器自动生成合成存取方法时，则可以自己来实现。或者使用`@dynamic`关键字。这个关键字会告诉编译器：不要自动创建实现属性所用的实例变量，也不要为其创建存取方法。这样在编译时访问属性的代码，即使编译器发生没有定义存取方法，也不会报错，因为他相信这些方法在运行时期会找到。
    那么在实现文件中我们需要实现类以下代码：
    {% codeblock 手动生成存取方法和实例变量  lang:objc  %}
        @implementation Person{
        //手动添加实例变量
        NSString *_fistName;
        NSString *_lastName;
        }
        //使用 @dynamic 让编译器不自动生成存取方法和属性
        @dynamic firstName,lastName;
        ///我们手动添加存取方法
        -(void)setFirstName:(NSString *)firstName{
            _fistName = firstName;
        }
        -(NSString *)firstName{
            return _fistName;
        }
        - (void)setLastName:(NSString *)lastName{
            _lastName = lastName;
        }
        -(NSString *)lastName{
            return _lastName;
        }
        @end
    {% endcodeblock %}
    如果我们只添加了`@dynamic firstName,lastName;`这句代码，没用手动添加存取方法，那么在编译时，编译器是不会发出警告信息的。但是在运行时会因找不到存取方法而发生crash.

# 属性的特性
属性具有的特性分为四类：原子性、读/写权限、 内存管理语义、方法名
## 原子性
在默认情况下，由编译器所合成的方法会通过锁定机制确保其原子性(atomicity)。如果属性具有`nonatomic`则不使用同步锁。**在属性声明中如果没有明确指出`nonatomic`,那么其就是`atomic`,如果我们手动定义存取方法，那么就应该遵从与属性特性相符的原子性** ps:原子性并不是线程安全的。

## 读/写权限
1. `readwrite`读写权限 ，具备该特性的属性具有“获取方法(getter)”与“设置方法(setter)”,若该属性由`@synthesize`实现，则编译器会自动生成这两个方法
2. `readonly`只读权限。具备该特性的属性具有“获取方法（getter）”,只有当该属性由`@synthesize`实现时，编译器才会为其合成获取方法。我们可以利用些特性，把某个属性对外公开为只读属性，然后在分类中将其重新定义为读写属性。

## 内存管理语义
属性用于封装数据，而数据则要有“具体的所有权语义”，以下特性仅会影响“设置方法”。
1. `assign` “设置方法”只会执行针对“纯量类型”（数值型）的简单赋值操作
2. `strong` 此特性表明该属性定义了一种“拥有关系”，为这种属性设置新值进，设置方法会先保留新值，并释放旧值，然后再把新值设置上去。
3. `weak`此特性表时该属性定义了一种“非拥有关系不”，为这种属性设置新值时，设置方法既不保留新值，也不会释放旧值。同`assign`类似，但在属性所指对象销毁时，属性值也会清空（nil out）。
4. `unsafe_unretained`，此特性的语义与`assign`相同，但它适用于“对象类型”，该特性表明一种“非拥有关系”，当目标对象销毁时，属性值不会自动清空。
5. `copy`此特性表达的是所属关系，与`strong`类似，但设置方法并不保留新值，而是将其“拷贝”。当属性类型为`NSString*`时，经常使用些特性来保护其封装性，因为传递给设置方法的新值很有可以是一个指向“NSMutableString”类的实例。这个类是`NSString`的子类，表示一种可以修改其值的字符串，此时若不拷贝字符串，那么设置完属性后，字符串的值很有可能在对象不知情的情况下被修改。所以这时拷贝一份“不可变”的字符串，确保对象中的字符串值不会无意间变动，只要实现属性所用的对象是可变的，那就应在设置新值进拷贝一份。

## 方法名
可以通过以下方法指定存取方法名：
1. `getter = <name>`指定`获取方法`的方法名。
2. `setter = <name>`指定“设置方法”的方法名。不太常用
{% codeblock 指定方法名示例  lang:objc   %}
@property (nonatomic,copy,getter=<#method#>,setter=<#method#>) NSString *firstName;
{% endcodeblock %}
通过以后方式可以微调编译器所合成的存取方法。**注意：如果是我们自己来实现这些存取方法，那么应该保证其具备相关属性所声明的特性**

**`atomic`与`nonatomic`的区别是什么？**
具备atomic特性和获取方法会通过锁定机制来确保其操作的原子性。也就是说如何两个线程读写同一属性，那么无论何时总能看到有效的属性值。若不加锁，那么当其中一个线程正在改写某属性值时，另一个线程突然进入，把尚未修改好的属性值读取出来，这时，线程读取的值可能不对。在开发过程中会发现大部分属性都声明为'nonatomic',为什么？因为在iOS中使用同步锁的开销很大，会带来性能问题。一般情况下并不要求属性必须是原子性，因为这并不能保证线程安全，若要实现线程安全，需要采用更为深层的锁定机制。

> 总结
> 1. 采用@property语法来定义对象中所封装的数据
> 2. 通过“特性”来指定存储数据所需要的正确语义
> 3. 在设置必属性对应的实例变量时，一定要遵从该属性所声明的语义
> 4. 在开发时应用nonatomic属性，因为atomic属性会严重影响性能

# 在对象内部尽量直接访问实例变量
在对象之外访问实例变量时，总是应该通过属性来做，那么在对象内部访问实例变量时，要怎么做呢？
我们来观察下列代码段：
{% codeblock 在对象内部使用属性来获取相应的值  lang:objc   %}
-(void)setFullName:(NSString *)fullName{
    NSArray *components = [fullName componentsSeparatedByString:@" "];
    self.firstName = [components firstObject];
    self.lastName = [components lastObject];
}

-(NSString *)fullName{
    return [NSString stringWithFormat:@"%@ %@",self.firstName,self.lastName];
}
{% endcodeblock %}
{% codeblock 直接使用实例变量  lang:objc   %}
-(void)setFullName:(NSString *)fullName{
    NSArray *components = [fullName componentsSeparatedByString:@" "];
    _firstName = [components firstObject];
    _lastName = [components lastObject];
}

-(NSString *)fullName{
    return [NSString stringWithFormat:@"%@ %@",_firstName,_lastName];
}
{% endcodeblock %}
**这两种写法有什么区别？**
1. 由于不经过OC的“消息派发”，所以直接访问实例变量的速度会比较快。这种情况下，编译器所生成的代码会直接访问保存对象实例变量的那块内存。
2. 直接访问实例变量时，不会调用其设置方法，这样绕过了相关属性所定义的内存管理语义。（在ARC下直接访问一个声明为Copy的属性，那么并不会拷贝该属性，，只会保留新值，释放旧值）
3. 直接访问实例变量，不会触发“键值观察（KVO）”通知。
4. 通过属性来访问有助于排查与之相关的错误。（可以在存取方法中添加断点，监控该属性值的调用及其访问时机）

***有没有一种即能提高读写操作的速度，又能控制对属性的写入操作的方案：***
在写入实例变量时，通过其设置方法来做，而在读取实例变量时，直接访问实例变量。
>使用这种方法需要注意以下两点：
    1. 在初始方法中应该如何设置属性值。
    2. 在使用“懒加载初始化时”，需要通过属性的获取方法来访问属性，否则实例变量永远不会初始化

>小结
    1. 在对象内部读取数据时应该直接通过实例变量获取，而写入数据时，应通过属性来写。
    2. 在初始化及dealloc方法中，应直接通过实例变量来读写数据。
    3. 有时会使用懒加载初始化技术配置某份数据，这种情况下，需要通过属性来读取数据
