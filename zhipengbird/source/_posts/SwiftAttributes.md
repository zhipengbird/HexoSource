---
title: Attributes
date: 2019-08-23 13:51:56
tags:
- Swift
---
# Attributes

There are two kinds of attributes in Swift—those that apply to declarations and those that apply to types. An attribute provides additional information about the declaration or type.

You specify an attribute by writing the @ symbol followed by the attribute’s name and any arguments that the attribute accepts:
```swift
@attribute name
@attribute name(attribute arguments)
```
Some declaration attributes accept arguments that specify more information about the attribute and how it applies to a particular declaration. These attribute arguments are enclosed in parentheses, and their format is defined by the attribute they belong to.

## Declaration Attributes
You can apply a declaration attribute to declarations only.

### available
Apply this attribute to indicate a declaration’s life cycle relative to certain Swift language versions or certain platforms and operating system versions.
The available attribute always appears with a list of two or more comma-separated attribute arguments. These arguments begin with one of the following platform or language names:
  * iOS
  * iOSApplicationExtension
  * macOS
  * macOSApplicationExtension
  * watchOS
  * watchOSApplicationExtension
  * tvOS
  * tvOSApplicationExtension
  * swift

The remaining arguments can appear in any order and specify additional information about the declaration’s life cycle, including important milestones
* The `unavailable` argument indicates that the declaration isn’t available on the specified platform. This argument can’t be used when specifying Swift version availability.
* The `introduced` argument indicates the first version of the specified platform or language in which the declaration was introduced. It has the following form:

``` swift
introduced: `version number`
```
The version number consists of one to three positive integers, separated by periods.

* The `deprecated` argument indicates the first version of the specified platform or language in which the declaration was deprecated. It has the following form:
  
  ```swift
  deprecated: version number
  ```

* The `obsoleted` argument indicates the first version of the specified platform or language in which the declaration was obsoleted. When a declaration is obsoleted, it’s removed from the specified platform or language and can no longer be used. It has the following form:
```swift
obsoleted: version number
```
* The `message` argument provides a textual message that the compiler displays when emitting a warning or error about the use of a deprecated or obsoleted declaration. It has the following form:
```message: message```
The message consists of a string literal.

* The renamed argument provides a textual message that indicates the new name for a declaration that’s been renamed. The compiler displays the new name when emitting an error about the use of a renamed declaration. It has the following form:
```renamed: new name```
The new name consists of a string literal.

### discardableResult

Apply this attribute to a function or method declaration to suppress the compiler warning when the function or method that returns a value is called without using its result.

### dynamicCallable
Apply this attribute to a class, structure, enumeration, or protocol to treat instances of the type as callable functions. The type must implement either a dynamicallyCall(withArguments:) method, a dynamicallyCall(withKeywordArguments:) method, or both.

### dynamicMemberLookup
Apply this attribute to a class, structure, enumeration, or protocol to enable members to be looked up by name at runtime. The type must implement a `subscript(dynamicMemberLookup:) `subscript.

### GKInspectable
Apply this attribute to expose a custom GameplayKit component property to the SpriteKit editor UI. Applying this attribute also implies the objc attribute.

### inlinable

Apply this attribute to a function, method, computed property, subscript, convenience initializer, or deinitializer declaration to expose that declaration’s implementation as part of the module’s public interface. The compiler is allowed to replace calls to an inlinable symbol with a copy of the symbol’s implementation at the call site.

Inlinable code can interact with public symbols declared in any module, and it can interact with internal symbols declared in the same module that are marked with the usableFromInline attribute. Inlinable code can’t interact with private or fileprivate symbols.

### nonobjc
Apply this attribute to a method, property, subscript, or initializer declaration to suppress an implicit `objc` attribute. The `nonobjc` attribute tells the compiler to make the declaration unavailable in Objective-C code, even though it’s possible to represent it in Objective-C.

Applying this attribute to an extension has the same effect as applying it to every member of that extension that isn’t explicitly marked with the `objc` attribute.