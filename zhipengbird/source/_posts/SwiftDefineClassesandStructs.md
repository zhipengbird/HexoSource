---
title: Comparing Structures and Classes
date: 2019-08-19 20:57:53
tags:
- Swift 
---
## Comparing Structures and Classes
Structures and classes in Swift have many things in common. Both can:
* Define properties to store values
* Define methods to provide functionality
* Define subscripts to provide access to their values using subscript syntax
* Define initializers to set up their initial state
* Be extended to expand their functionality beyond a default implementation
* Conform to protocols to provide standard functionality of a certain kind
For more information, see Properties, Methods, Subscripts, Initialization, Extensions, and Protocols.

Classes have additional capabilities that structures don’t have:
* Inheritance enables one class to inherit the characteristics of another.
* Type casting enables you to check and interpret the type of a class instance at runtime.
* Deinitializers enable an instance of a class to free up any resources it has assigned.
* Reference counting allows more than one reference to a class instance.
For more information, see Inheritance, Type Casting, Deinitialization, and Automatic Reference Counting.

## Definition Syntax
Structures and classes have a similar definition syntax. You introduce structures with the struct keyword and classes with the class keyword. Both place their entire definition within a pair of braces:
```swift
struct SomeStructure {
    // structure definition goes here
}
class SomeClass {
    // class definition goes here
}
```
> Whenever you define a new structure or class, you define a new Swift type.
> * Give types *UpperCamelCase* names (such as SomeStructure and SomeClass here) to match the capitalization of standard Swift types (such as String, Int, and Bool).
> * Give properties and methods *lowerCamelCase* names (such as frameRate and incrementCount) to differentiate them from type names.

## Accessing Properties
You can access the properties of an instance using dot syntax. In dot syntax, you write the property name immediately after the instance name, separated by a period (.), without any spaces:
```swift
print("The width of someResolution is \(someResolution.width)")
```

## Memberwise Initializers for Structure Types
All structures have an automatically generated memberwise initializer, which you can use to initialize the member properties of new structure instances. Initial values for the properties of the new instance can be passed to the memberwise initializer by name:
```swift
let vga = Resolution(width: 640, height: 480)
```
> Unlike structures, class instances don’t receive a default memberwise initializer.

## Structures and Enumerations Are Value Types
A value type is a type whose value is copied when it’s assigned to a variable or constant, or when it’s passed to a function.
***In fact, all of the basic types in Swift—integers, floating-point numbers, Booleans, strings, arrays and dictionaries—are value types, and are implemented as structures behind the scenes.***

All structures and enumerations are value types in Swift. *This means that any structure and enumeration instances you create—and any value types they have as properties—are always copied when they are passed around in your code.*

> Collections defined by the standard library like arrays, dictionaries, and strings use an optimization to reduce the performance cost of copying. Instead of making a copy immediately, these collections share the memory where the elements are stored between the original instance and any copies. If one of the copies of the collection is modified, the elements are copied just before the modification. The behavior you see in your code is always as if a copy took place immediately.

## Classes Are Reference Types
Unlike value types, reference types are not copied when they are assigned to a variable or constant, or when they are passed to a function. Rather than a copy, a reference to the same existing instance is used.


## Identity Operators
Because classes are reference types, it’s possible for multiple constants and variables to refer to the same single instance of a class behind the scenes. (The same isn’t true for structures and enumerations, because they are always copied when they are assigned to a constant or variable, or passed to a function.)

It can sometimes be useful to find out whether two constants or variables refer to exactly the same instance of a class. To enable this, Swift provides two identity operators:
* Identical to (===)
* Not identical to (!==)

> Note that identical to (represented by three equals signs, or ===) doesn’t mean the same thing as equal to (represented by two equals signs, or ==). 
> * Identical to means that two constants or variables of class type refer to exactly the same class instance.
> * Equal to means that two instances are considered equal or equivalent in value, for some appropriate meaning of equal, as defined by the type’s designer.