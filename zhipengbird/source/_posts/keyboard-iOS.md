---
title: keyboard_iOS
date: 2017-03-16 16:55:27
categories:
- iOS 
tags:
- 键盘
---
# 一. 键盘通知
  当文本`View`(如`UITextField`,`UITextView`, `UIWebView`内的输入框)进入编辑模式成为first responder时，系统会自动显示键盘。成为firstresponder可能由用户点击触发，也可向文本View发送`becomeFirstResponder`消息触发。当文本视图退出first responder时，键盘会消失。文本View退出first responder可能由用户点击键盘上的Done或Return键结束输入触发，也可向文本View发送`resignFirstResponder`消息触发。
  当键盘显示或消失时，系统会发送相关的通知:
    1. UIKeyboardWillShowNotification
    2. UIKeyboardDidShowNotification
    3. UIKeyboardWillHideNotification
    4. UIKeyboardDidHideNotification
  通知消息 `NSNotification`中的 userInfo字典中包含键盘的位置和大小信息，对应的key为
    1. UIKeyboardFrameBeginUserInfoKey
    2. UIKeyboardFrameEndUserInfoKey
    3. UIKeyboardAnimationDurationUserInfoKey
    4. UIKeyboardAnimationCurveUserInfoKey
  `UIKeyboardFrameBeginUserInfoKey`,`UIKeyboardFrameEndUserInfoKey`对应的`Value`是个`NSValue`对象，内部包含`CGRect`结构，分别为 **键盘起始时和终止时的位置信息**。
  `UIKeyboardAnimationCurveUserInfoKey`对应的`Value`是`NSNumber`对象，内部为`UIViewAnimationCurve`类型的数据，表示键盘显示或消失的 **动画类型**。
  `UIKeyboardAnimationDurationUserInfoKey`对应的`Value`也是`NSNumber`对象，内部为`double`类型的数据，表示键盘显示或消失时动画的持续时间。
# 文本对象与WebView键盘设置
 `UITextFiled`和 `UITextView`都遵循 `[UITextInputTraits](https://developer.apple.com/reference/uikit/uitextinputtraits#//apple_ref/occ/intfp/UITextInputTraits/returnKeyType)`协议，在`UITextInputTraits`协议中定义了设置键盘的属性，有
  1. keyboardType:键盘类型，如UIKeyboardTypeDefault,UIKeyboardTypeURL,UIKeyboardTypeEmailAddress,UIKeyboardTypePhonePad等
  2. returnKeyType:键盘Return键显示的文本，默认为”Return”,其他可选择的包括Go,Next,Done,Send,Google等。
  3. keyboardAppearance：键盘外观，默认为 UIKeyboardAppearanceDefault，即上图中的浅兰色不透明背景，另外还有一种为 UIKeyboardAppearanceAlert
  4. autocapitalizationType:文本大小写样式，见 UITextAutocapitalizationType。
  5. autocorrectionType:是否自动更正，见 UITextAutocorrectionType。
  6. spellCheckingType:拼写检查设置，见UITextSpellCheckingType。
  7. enablesReturnKeyAutomatically：是否在无文本时禁用Return键，默认为NO。若为YES,则用户至少输入一个字符后Return键才被激活。
  8. secureTextEntry：若输入的是密码，可设置此类型为YES,输入字符时可显示最后一个字符，其他字符显示为点。

  UIWebView本身不直接遵循 UITextInputTraits协议，但同样可设置其内部输入部件的键盘属性。如Configuring the Keyboard for Web Views中所述。
  设置autocorrect, auto-capitalization属性。

# 使用inputAccessoryView与inputView定制输入视图
  `inputAccessoryView`和 `inputView`属性在 `UIResponder`中定义，为`readonly`的属性，但在`UITextFiled`和 `UITextView`中重新定义为了·的属性，可以由用户赋值。若 inputView的不为nil,则当文本视图成为first responder时，不会显示系统键盘，而是显示自定义的`inputView`;若`inputAccessoryView`不为nil,则`inputAccessoryView`会显示在系统键盘或定制`inputView`的上方。当使用`inputView`时，仍然会有`WillShow`,`DidShow`,`WillHide`,`DidHide`的键盘通知，通知中的`BeginFrame`与`EndFrame`为系统键盘(或`inputView`)与`inputAccessoryView`一起的frame。

  在标准视图中只有`UITextField`和`UITextView`将`inputView`和`inputAccessoryView`重新定义为了`readwrite`类型，若想在自定义视图中使用，需要在自定义视图中重新定义`inputView`和`inputAccessoryView`属性。见 [Input Views and Input Accessory Views](https://developer.apple.com/library/content/documentation/StringsTextFonts/Conceptual/TextAndWebiPhoneOS/InputViews/InputViews.html#//apple_ref/doc/uid/TP40009542-CH12-SW2)。


# ps
我们可以将键盘看成有两部分组成（当然，键盘还会有其他的部分组成），一部分是inputView，一部分是InputAccessoryView，并且，inputView在系统键盘的下面的部分，我们调用的系统默认键盘的时候，我们看到的部分就是这个InputView（输入视图），而这个InputAccessoryView就是键盘顶部的一个部分，当我Nil的时候则不显示，当我们给这个属性赋值的时候，就会显示这个我们添加的视图。



# 键盘中的枚举

## 键盘风格
UIKit框架支持8种风格键盘。
```objc
typedef enum {  
    UIKeyboardTypeDefault,                // 默认键盘：支持所有字符  
    UIKeyboardTypeASCIICapable,           // 支持ASCII的默认键盘  
    UIKeyboardTypeNumbersAndPunctuation,  // 标准电话键盘，支持+ * # 等符号  
    UIKeyboardTypeURL,                    // URL键盘，有.com按钮；只支持URL字符  
    UIKeyboardTypeNumberPad,              //数字键盘  
    UIKeyboardTypePhonePad,               // 电话键盘  
    UIKeyboardTypeNamePhonePad,           // 电话键盘，也支持输入人名字  
    UIKeyboardTypeEmailAddress,           // 用于输入电子邮件地址的键盘  
} UIKeyboardType;  

```
eg:
`textView.keyboardtype = UIKeyboardTypeNumberPad;`

## 键盘外观
```objc
typedef enum {  
    UIKeyboardAppearanceDefault,    // 默认外观：浅灰色  
    UIKeyboardAppearanceAlert,      //深灰/石墨色  
} UIKeyboardAppearance;
```
eg :
`textView.keyboardAppearance=UIKeyboardAppearanceDefault;`

## 回车键
```objc
typedef enum {  
    UIReturnKeyDefault,  //默认：灰色按钮，标有Return
    UIReturnKeyGo,  //标有Go的蓝色按钮
    UIReturnKeyGoogle,  //标有Google的蓝色按钮，用于搜索
    UIReturnKeyJoin,  //标有Join的蓝色按钮
    UIReturnKeyNext,  //标有Next的蓝色按钮
    UIReturnKeyRoute,  //标有Route的蓝色按钮
    UIReturnKeySearch,  //标有Search的蓝色按钮
    UIReturnKeySend,  //标有Send的蓝色按钮
    UIReturnKeyYahoo,  //标有Yahoo!的蓝色按钮，用于搜索
    UIReturnKeyDone,  //标有Done的蓝色按钮
    UIReturnKeyEmergencyCall,  //紧急呼叫按钮
} UIReturnKeyType;
```
eg:
`textView.returnKeyType=UIReturnKeyGo;`

## 自动大写
```objc
typedef enum {  
    UITextAutocapitalizationTypeNone, //不自动大写  
    UITextAutocapitalizationTypeWords, //单词首字母大写  
    UITextAutocapitalizationTypeSentences, //句子首字母大写  
    UITextAutocapitalizationTypeAllCharacters, //所有字母大写  
} UITextAutocapitalizationType;  
```
eg:
`textField.autocapitalizationType = UITextAutocapitalizationTypeWords;`
## 自动更正
```objc
typedef enum {  
    UITextAutocorrectionTypeDefault,//默认  
    UITextAutocorrectionTypeNo,//不自动更正  
    UITextAutocorrectionTypeYes,//自动更正  
} UITextAutocorrectionType;
```
eg:
`textField.autocorrectionType = UITextAutocorrectionTypeYes;`

## 安全文本输入
`textView.secureTextEntry=YES;`
开启安全输入主要是用于密码或一些私人数据的输入，此时会禁用自动更正和自此缓存。
