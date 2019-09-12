---
title: String and Characters
date: 2019-09-09 15:18:20
tags:
---

A string is a series of characters, such as "hello, world" or "albatross". Swift strings are represented by the String type. The contents of a String can be accessed in various ways, including as a collection of Character values.

## String Literals

You can include predefined String values within your code as string literals. A string literal is a sequence of characters surrounded by double quotation marks (").
Use a string literal as an initial value for a constant or variable:

```swift
let someString = "Some string literal value"
```

## Multiline String Literals

If you need a string that spans several lines, use a multiline string literal—a sequence of characters surrounded by three double quotation marks:

```swift
let quotation = """
The White Rabbit put on his spectacles.  "Where shall I begin,
please your Majesty?" he asked.

"Begin at the beginning," the King said gravely, "and go on
till you come to the end; then stop."
"""
```

A multiline string literal includes all of the lines between its opening and closing quotation marks. The string begins on the first line after the opening quotation marks (""") and ends on the line before the closing quotation marks, which means that neither of the strings below start or end with a line break:

```swift
let singleLineString = "These are the same."
let multilineString = """
These are the same.
"""
```

## Special Characters in String Literals

String literals can include the following special characters:

* The escaped special characters \0 (null character), \\ (backslash), \t (horizontal tab), \n (line feed), \r (carriage return), \" (double quotation mark) and \' (single quotation mark)
* An arbitrary Unicode scalar value, written as \u{n}, where n is a 1–8 digit hexadecimal number (Unicode is discussed in Unicode below)

