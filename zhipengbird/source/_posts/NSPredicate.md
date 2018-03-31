---
title: NSPredicate
date: 2017-07-12 11:35:48
categories: iOS
tags:
- NSPredicate
---
# NSPredicate是什么？
NSPredicate是一个Foundation类，它指定数据被获取或者过滤的方式。它的查询语言就像SQL的WHERE和正则表达式的交叉一样，提供了具有表现力的，自然语言界面来定义一个集合被搜寻的逻辑条件

# 集合中使用NSPredicate

Foundation提供使用谓词（predicate）来过滤NSArray／NSMutableArray&NSSet／NSMutableSet的方法。

不可变的集合，NSArray&NSSet，有可以通过评估接收到的predicate来返回一个不可变集合的方法filteredArrayUsingPredicate:和filteredSetUsingPredicate:。

可变集合，NSMutableArray&NSMutableSet，可以使用方法filterUsingPredicate:，它可以通过运行接收到的谓词来移除评估结果为FALSE的对象。

NSDictionary可以用谓词来过滤它的键和值（两者都为NSArray对象）。NSOrderedSet可以由过滤的NSArray或NSSet生成一个新的有序的集，或者NSMutableSet可以简单的removeObjectsInArray:，来传递通过_否定_predicate过滤的对象。

# Core Data中使用NSPredicate
NSFetchRequest有一个predicate属性，它可以指定管理对象应该被获取的逻辑条件。谓词的使用规则在这里同样适用，唯一的区别在于，在管理对象环境中，谓词由持久化存储助理（persistent store coordinator）评估，而不像集合那样在内存中被过滤

{% codeblock data  lang:objc   %}
@interface Person : NSObject
@property NSString *firstName;
@property NSString *lastName;
@property NSNumber *age;
@end

@implementation Person

- (NSString *)description {
    return [NSString stringWithFormat:@"%@ %@", self.firstName, self.lastName];
}

@end

#pragma mark -

NSArray *firstNames = @[ @"Alice", @"Bob", @"Charlie", @"Quentin" ];
NSArray *lastNames = @[ @"Smith", @"Jones", @"Smith", @"Alberts" ];
NSArray *ages = @[ @24, @27, @33, @31 ];

NSMutableArray *people = [NSMutableArray array];
[firstNames enumerateObjectsUsingBlock:^(id obj, NSUInteger idx, BOOL *stop) {
    Person *person = [[Person alloc] init];
    person.firstName = firstNames[idx];
    person.lastName = lastNames[idx];
    person.age = ages[idx];
    [people addObject:person];
}];

NSPredicate *bobPredicate = [NSPredicate predicateWithFormat:@"firstName = 'Bob'"];
NSPredicate *smithPredicate = [NSPredicate predicateWithFormat:@"lastName = %@", @"Smith"];
NSPredicate *thirtiesPredicate = [NSPredicate predicateWithFormat:@"age >= 30"];

// ["Bob Jones"]
NSLog(@"Bobs: %@", [people filteredArrayUsingPredicate:bobPredicate]);

// ["Alice Smith", "Charlie Smith"]
NSLog(@"Smiths: %@", [people filteredArrayUsingPredicate:smithPredicate]);

// ["Charlie Smith", "Quentin Alberts"]
NSLog(@"30's: %@", [people filteredArrayUsingPredicate:thirtiesPredicate]);


{% endcodeblock %}


# 谓词语法
## 替换（占位符）
* %@是对值为字符串，数字或者日期的对象的替换值。(变量值)
* %K是key path的替换值。（变量名）
{% codeblock   lang:objc  %}
NSPredicate *ageIs33Predicate = [NSPredicate predicateWithFormat:@"%K = %@", @"age", @33];

// ["Charlie Smith"]
NSLog(@"Age 33: %@", [people filteredArrayUsingPredicate:ageIs33Predicate]);
{% endcodeblock %}

* $VARIABLE_NAME是可以被NSPredicate -predicateWithSubstitutionVariables:替换的值。
{% codeblock 变量  lang:objc   %}
NSPredicate *namesBeginningWithLetterPredicate = [NSPredicate predicateWithFormat:@"(firstName BEGINSWITH[cd] $letter) OR (lastName BEGINSWITH[cd] $letter)"];

// ["Alice Smith", "Quentin Alberts"]
NSLog(@"'A' Names: %@", [people filteredArrayUsingPredicate:[namesBeginningWithLetterPredicate predicateWithSubstitutionVariables:@{@"letter": @"A"}]]);
{% endcodeblock %}

## 基本比较
* =, ==：左边的表达式和右边的表达式相等。
* '>=', =>：左边的表达式大于或者等于右边的表达式。
* <=, =<：左边的表达式小于等于右边的表达式。
* '>'：左边的表达式大于右边的表达式。
* <：左边的表达式小于右边的表达式。
* !=, <>：左边的表达式不等于右边的表达式。
* BETWEEN：左边的表达式等于右边的表达式的值或者介于它们之间。右边是一个有两个指定上限和下限的数值的数列（指定顺序的数列）。比如，1 BETWEEN { 0 , 33 }，或者$INPUT BETWEEN { $LOWER, $UPPER }。

## 基本复合谓词
* AND, &&：逻辑与.
* OR, ||：逻辑或.
* NOT, !：逻辑非.

## 字符串比较
字符串比较在默认的情况下是**区分大小写和音调**的。你可以在方括号中用关键字符c和d来修改操作符以相应的指定不区分大小写和变音符号，比如firstname BEGINSWITH[cd] $FIRST_NAME。
* BEGINSWITH：左边的表达式以右边的表达式作为开始。
* CONTAINS：左边的表达式包含右边的表达式。
* ENDSWITH：左边的表达式以右边的表达式作为结束。
* LIKE：左边的表达式等于右边的表达式：?和*可作为通配符，其中?匹配1个字符，*匹配0个或者多个字符。
* MATCHES：左边的表达式根据ICU v3（更多内容请查看ICU User Guide for Regular Expressions）的regex风格比较，等于右边的表达式

## 关系操作
* ANY，SOME：指定下列表达式中的任意元素。比如，ANY children.age < 18。
* ALL：指定下列表达式中的所有元素。比如，ALL children.age < 18。
* NONE：指定下列表达式中没有的元素。比如，NONE children.age < 18。它在逻辑上等于NOT (ANY ...)。
* IN：等于SQL的IN操作，左边的表达必须出现在右边指定的集合中。比如，name IN { 'Ben', 'Melissa', 'Nick' }。

## 数组操作
* array[index]：指定数组中特定索引处的元素。
* array[FIRST]：指定数组中的第一个元素。
* array[LAST]：指定数组中的最后一个元素。
* array[SIZE]：指定数组的大小。

## 布尔值谓词
* TRUEPREDICATE：结果始终为真的谓词。
* FALSEPREDICATE：结果始终为假的谓词。

## 直接量
在谓词表达式中可以使用如下直接量

* FALSE、NO：代表逻辑假
* TRUE、YES：代表逻辑真
* NULL、NIL：代表空值
* SELF：代表正在被判断的对象自身
* "string"或'string'：代表字符串
* 数组：和c中的写法相同，如：{'one', 'two', 'three'}。
* 数值：包括证书、小数和科学计数法表示的形式
* 十六进制数：0x开头的数字
* 八进制：0o开头的数字
* 二进制：0b开头的数字

## 保留字
> 下列单词都是保留字（不论大小写）

>AND、OR、IN、NOT、ALL、ANY、SOME、NONE、LIKE、CASEINSENSITIVE、CI、MATCHES、CONTAINS、BEGINSWITH、ENDSWITH、BETWEEN、NULL、NIL、SELF、TRUE、YES、FALSE、NO、FIRST、LAST、SIZE、ANYKEY、SUBQUERY、CAST、TRUEPREDICATE、FALSEPREDICATE

>注：虽然大小写都可以，但是更推荐使用大写来表示这些保留字


# NSCompoundPredicate
我们见过与&或被用在谓词格式字符串中以创建复合谓词。然而，我们也可以用NSCompoundPredicate来完成同样的工作

{% codeblock 下列谓词是相等的  lang:objc   %}
[NSCompoundPredicate andPredicateWithSubpredicates:@[[NSPredicate predicateWithFormat:@"age > 25"], [NSPredicate predicateWithFormat:@"firstName = %@", @"Quentin"]]];

[NSPredicate predicateWithFormat:@"(age > 25) AND (firstName = %@)", @"Quentin"];

{% endcodeblock %}
