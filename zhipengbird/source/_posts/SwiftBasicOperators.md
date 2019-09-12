---
title: SwiftBasicOperators
date: 2019-08-30 11:36:03
tags:
- swift 
---
 # Basic Operators
An operator is a special symbol or phrase that you use to check, change, or combine values. 
Swift supports most standard C operators and improves several capabilities to eliminate common coding errors.
* The assignment operator (=) doesn’t return a value, to prevent it from being mistakenly used when the equal to operator (==) is intended.
* Arithmetic operators (+, -, *, /, % and so forth) detect and disallow value overflow, to avoid unexpected results when working with numbers that become larger or smaller than the allowed value range of the type that stores them. 

Swift also provides range operators that aren’t found in C, such as `a..<b` and `a...b`, as a shortcut for expressing a range of values.

## Terminology
Operators are unary, binary, or ternary:
* Unary operators operate on a single target (such as -a). Unary prefix operators appear immediately before their target (such as !b), and unary postfix operators appear immediately after their target (such as c!
* Binary operators operate on two targets (such as 2 + 3) and are infix because they appear in between their two targets
* Ternary operators operate on three targets. Like C, Swift has only one ternary operator, the ternary conditional operator `(a ? b : c)`.

## Assignment Operator
The assignment operator (a = b) initializes or updates the value of a with the value of b:
```swift
let b = 10
var a = 5
a = b// a is now equal to 10
```

If the right side of the assignment is a tuple with multiple values, its elements can be decomposed into multiple constants or variables at once:
```swift
let (x, y) = (1, 2)
// x is equal to 1, and y is equal to 2
```
Unlike the assignment operator in C and Objective-C, the assignment operator in Swift does not itself return a value. The following statement is not valid:
```swift
if x = y {
    // This is not valid, because x = y does not return a value.
}
```

## Arithmetic Operators
Swift supports the four standard arithmetic operators for all number types:

* Addition (+)
* Subtraction (-)
* Multiplication (*)
* Division (/)

> Unlike the arithmetic operators in C and Objective-C, the Swift arithmetic operators don’t allow values to overflow by default.
The addition operator is also supported for String concatenation:
```swift
"hello, " + "world"  // equals "hello, world"
```
### Remainder Operator

The remainder operator (a % b) works out how many multiples of b will fit inside a and returns the value that is left over (known as the remainder)
>The remainder operator (%) is also known as a modulo operator in other languages. However, its behavior in Swift for negative numbers means that, strictly speaking, it’s a remainder rather than a modulo operation.

```swift
a = (b x some multiplier) + remainder
9 = (4 x 2) + 1
```
> The sign of b is ignored for negative values of b. This means that a % b and a % -b always give the same answer.

### Unary Minus Operator
The sign of a numeric value can be toggled using a prefixed -, known as the unary minus operator:
```swift
let three = 3
let minusThree = -three       // minusThree equals -3
let plusThree = -minusThree   // plusThree equals 3, or "minus minus three"
```
### Unary Plus Operator
The unary plus operator (+) simply returns the value it operates on, without any change:
```swift
let minusSix = -6
let alsoMinusSix = +minusSix  // alsoMinusSix equals -6
```

## Compound Assignment Operators
Like C, Swift provides compound assignment operators that combine assignment (=) with another operation.
> The compound assignment operators don’t return a value. For example, you can’t write let b = a += 2.

## Comparison Operators
Swift supports all standard C comparison operators:
* Equal to (`a == b`)
* Not Equal to (`a != b`)
* Greater than (`a > b`)
* Less than (`a < b`)
* Greater than or equal to (`a >= b`)
* Less than or equal to (`a <= b`)
*  two identity operators (`===` and `!==`)
> Swift also provides two identity operators (=== and !==), which you use to test whether two object references both refer to the same object instance

Each of the comparison operators returns a Bool value to indicate whether or not the statement is true.
Comparison operators are often used in conditional statements, such as the if statement.
You can compare two tuples if they have the same type and the same number of values. Tuples are compared from left to right, one value at a time, until the comparison finds two values that aren’t equal.

> Tuples can be compared with a given operator only if the operator can be applied to each value in the respective tuples. For example, as demonstrated in the code below, you can compare two tuples of type (String, Int) because both String and Int values can be compared using the < operator. In contrast, two tuples of type (String, Bool) can’t be compared with the < operator because the < operator can’t be applied to Bool values.

> The Swift standard library includes tuple comparison operators for tuples with fewer than seven elements. To compare tuples with seven or more elements, you must implement the comparison operators yourself.

## Ternary Conditional Operator
The ternary conditional operator is a special operator with three parts, which takes the form `question ? answer1 : answer2.` It’s a shortcut for evaluating one of two expressions based on whether `question`is true or false. If `question` is true, it evaluates `answer1` and returns its value; otherwise, it evaluates `answer2` and returns its value.

The ternary conditional operator provides an efficient shorthand for deciding which of two expressions to consider. Use the ternary conditional operator with care, however. Its conciseness can lead to hard-to-read code if overused. Avoid combining multiple instances of the ternary conditional operator into one compound statement

## Nil-Coalescing Operator
The nil-coalescing operator (`a ?? b`) unwraps an optional `a` if it contains `a` value, or returns `a` default value `b` if `a` is nil. The expression `a` is always of an optional type. The expression `b` must match the type that is stored inside`a`.

The nil-coalescing operator is shorthand for the code below:
```swift
a != nil ? a! : b
```

## Range Operators

Swift includes several range operators, which are shortcuts for expressing a range of values.
### Closed Range Operator
The closed range operator (a...b) defines a range that runs from a to b, and includes the values a and b. The value of a must not be greater than b.
The closed range operator is useful when iterating over a range in which you want all of the values to be used, such as with a for-in loop:
```swift
for index in 1...5 {
    print("\(index) times 5 is \(index * 5)")
}
```

### Half-Open Range Operator

The half-open range operator (`a..<b`) defines a range that runs from a to b, but doesn’t include b. It’s said to be half-open because it contains its first value, but not its final value. As with the closed range operator, the value of a must not be greater than b. If the value of a is equal to b, then the resulting range will be empty。
```swift
let names = ["Anna", "Alex", "Brian", "Jack"]
let count = names.count
for i in 0..<count {
    print("Person \(i + 1) is called \(names[i])")
}
```


### One-Sided Ranges
The closed range operator has an alternative form for ranges that continue as far as possible in one direction-—for example, a range that includes all the elements of an array from index 2 to the end of the array. In these cases, you can omit the value from one side of the range operator. This kind of range is called a one-sided range because the operator has a value on only one side.
```swift
for name in names[2...] {
    print(name)
}
// Brian
// Jack

for name in names[...2] {
    print(name)
}
```
he half-open range operator also has a one-sided form that’s written with only its final value. 
```swift
for name in names[..<2] {
    print(name)
}
```
One-sided ranges can be used in other contexts, not just in subscripts. You can’t iterate over a one-sided range that omits a first value, because it isn’t clear where iteration should begin. You can iterate over a one-sided range that omits its final value; however, because the range continues indefinitely, make sure you add an explicit end condition for the loop. You can also check whether a one-sided range contains a particular value

```swift
let range = ...5
range.contains(7)   // false
range.contains(4)   // true
range.contains(-1)  // true
```

## Logical Operators
Logical operators modify or combine the Boolean logic values true and false. Swift supports the three standard logical operators found in C-based languages:
* Logical Not (`!a`)
* Logical AND (`a || b`)
* Logical OR (`a ||b`)

### Logical NOT Operator
The logical NOT operator (!a) inverts a Boolean value so that true becomes false, and false becomes true.
The logical NOT operator is a prefix operator, and appears immediately before the value it operates on, without any white space. It can be read as “not a”, as seen in the following example:
```swift
let allowedEntry = false
if !allowedEntry {
    print("ACCESS DENIED")
}
```

### Logical AND Operator
The logical AND operator (a && b) creates logical expressions where both values must be true for the overall expression to also be true.
If either value is false, the overall expression will also be false. In fact, if the first value is false, the second value won’t even be evaluated, because it can’t possibly make the overall expression equate to true. This is known as short-circuit evaluation.


### Logical OR Operator
The logical OR operator (a || b) is an infix operator made from two adjacent pipe characters. You use it to create logical expressions in which only one of the two values has to be true for the overall expression to be true.
