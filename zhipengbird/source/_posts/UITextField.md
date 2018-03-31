---
title: UITextField
date: 2016-11-22 13:29:03
categroy: iOS 
tags:
- UITextField
- UI

---
思路：
使用runtime 获取textfiled的所有属性
```objc
//获取类的所有属性
unsigned int count = 0;
Ivar *ivarList = class_copyIvarList([UITextField class], &count);
for (int i = 0; i<count; i++) {
    Ivar ivar = ivarList[i];
    NSLog(@"%s",ivar_getName(ivar));
}
free(ivarList);
```
修改文本框内的placehold字体颜色
1. 使用runtime // 利用KVC设置它颜色,结果成功 这里有一个问题，可能会出现低版本没有该属性，你需要进行检验下。
```objc
[textField setValue:[UIColor redColor]forKeyPath:@"_placeholderLabel.textColor"];  
[textField setValue: [UIFontboldSystemFontOfSize:16] forKeyPath :@"_placeholderLabel.font"];  
```

2. 设置属性文本
```objc
NSMutableDictionary *dict = [NSMutableDictionary dictionary];
dict[NSForegroundColorAttributeName] = [UIColor redColor];
NSAttributedString *attribute = [[NSAttributedString alloc] initWithString:self.placeholder attributes:dict];
[self setAttributedPlaceholder:attribute];
```
3. 设置光标颜色
使用`@property(null_resettable, nonatomic, strong) UIColor *tintColor`属性
```objc
textField.tintColor = UIColorFromRGBA(rgbValue,A) 
```
4. 捕捉键盘的return事件
遵守UITextFieldDelegate,并设置相关代理。实现如下代理方法
```objc
- (BOOL)textFieldShouldReturn:(UITextField *)textField;  
 // called when 'return' key pressed. return NO to ignore.
```

5. 捕捉文本改变事件通知
要想获取到文本改变事件通知，需要监听一个通知 `UITextFieldTextDidChangeNotification`
```objc
[[NSNotificationCenter defaultCenter]addObserver:self selector:@selector(textFiledDidChange:) name:UITextFieldTextDidChangeNotification object:`文本框对象`];

```
> PS: 文本常用通知
```objc
UIKIT_EXTERN NSNotificationName const UITextFieldTextDidBeginEditingNotification; //输入框已经开始编辑
UIKIT_EXTERN NSNotificationName const UITextFieldTextDidEndEditingNotification;         //输入框已经结束编辑
UIKIT_EXTERN NSNotificationName const UITextFieldTextDidChangeNotification;         //输入框已经改变
```

6. 限制文本字数
监听`UITextFieldTextDidChangeNotification`通知，并在方法里做如下操作。下面是我的一个代码片段。

```objc
#pragma mark - 文本改变通知
-(void)textFiledDidChange:(NSNotification*)textChange{
    UITextField * field = (UITextField*)textChange.object;
    NSString * toBeString = field.text;
    
    UITextRange  * selectedRange =  [field markedTextRange];
    UITextPosition* position = [field positionFromPosition:selectedRange.start offset:0];

    if (!position) {
        //判断文本字符数是否操作最大值
        if (toBeString.length>30) {
            NSRange  rangeIndex = [toBeString rangeOfComposedCharacterSequenceAtIndex:30];
            if (rangeIndex.length==1) {
                field.text = [toBeString substringToIndex:30];
            }else{
                NSRange range = [toBeString rangeOfComposedCharacterSequencesForRange:NSMakeRange(0, 30)];
                field.text = [toBeString substringWithRange:range];
            }
        }
        if (toBeString.length==0) {
            //没有输入文本处理
            _characterNumber.text=[NSString stringWithFormat:@"%d/30",(int)field.text.length];
            [_remark_textfield showUnderLine:UIColorFromRGBA(0xb4b4b4, 1)];
            _remark_OK.enabled=NO;

        }else{
            // 有文本处理
            _characterNumber.text=[NSString stringWithFormat:@"%d/30",(int)field.text.length];
            [_remark_textfield showUnderLine:UIColorFromRGBA(0xe62f17, 1)];
            if ([CheckInputValid CheckForNicknameWithInString:field.text]) {
                _remark_OK.enabled=YES;
            }else{
                _remark_OK.enabled=NO;
            }
        }
    }
}
```

7. 个性化定制文本框右边的视图( `rightView`)
```objc

    // 初始化一个视图（可以是任一视图（UILabel,UIButton,...）），Ps 需要指定一个框高  ,你可以对这个视图添加你想要的事件
    _characterNumber = [[UILabel alloc]initWithFrame:CGRectMake(0, 0, 50, 30)];
    _characterNumber.text = @"0/30";
    _characterNumber.textAlignment = NSTextAlignmentRight;
    _characterNumber.font = [UIFont systemFontOfSize:12];
    _characterNumber.textColor = UIColorFromRGBA(0xb4b4b4, 1);

    // 设置右边视图
    _remark_textfield.rightView = _characterNumber;

    // 设置右边视图的显示模式
    _remark_textfield.rightViewMode = UITextFieldViewModeAlways;

```

8. 当键盘出现时，需要改变输入框的位置，要如何做？  
要想实现这个，很简单，同样你需要监听`UIKeyboardDidChangeFrameNotification`这个通知。
```objc
[[NSNotificationCenter defaultCenter ]addObserver:self selector:@selector(keyBoardFrameChanged:) name:UIKeyboardDidChangeFrameNotification object:nil];

-(void)keyBoardFrameChanged:( NSNotification * )notification{
    NSDictionary * userInfo = [notification userInfo];
    NSValue * value = [userInfo objectForKey:UIKeyboardFrameEndUserInfoKey];

    // 获取到键盘的位置
    CGRect keyboardRect =  [value CGRectValue];

    CGFloat keyboardTop = keyboardRect.origin.y;

    //进行你的想要的布局操作
}
```

>PS：常见的键盘通知
 ```objc
    UIKIT_EXTERN NSNotificationName const UIKeyboardWillChangeFrameNotification  NS_AVAILABLE_IOS(5_0) __TVOS_PROHIBITED; //键盘将要改变通知
    UIKIT_EXTERN NSNotificationName const UIKeyboardDidChangeFrameNotification   NS_AVAILABLE_IOS(5_0) __TVOS_PROHIBITED; //键盘已经改变通知

    UIKIT_EXTERN NSNotificationName const UIKeyboardWillShowNotification __TVOS_PROHIBITED; //键盘将要显示通知
    UIKIT_EXTERN NSNotificationName const UIKeyboardDidShowNotification __TVOS_PROHIBITED;  //键盘已经显示
    UIKIT_EXTERN NSNotificationName const UIKeyboardWillHideNotification __TVOS_PROHIBITED; //键盘将要隐藏
    UIKIT_EXTERN NSNotificationName const UIKeyboardDidHideNotification __TVOS_PROHIBITED;  //键盘已经隐藏
```

9. 给文本框加上一根横线
对于这个，你可以给做一个子类，继承自UITextFiled. 重写drawRect方法如下所示
```objc
@interface TextFieldExtension : UITextField
/**
 显示下划线
 */
-(void)showUnderLine:(UIColor* _Nullable)lineColor;
@end
#import "TextFieldExtension.h"

@interface TextFieldExtension ()
@property(nonatomic,assign) BOOL showunderline;
@property(nonatomic,strong)UIColor * lineColor;
@end

@implementation TextFieldExtension
- (void)drawRect:(CGRect)rect {
    if (_showunderline) {
        // Drawing code
        CGContextRef context = UIGraphicsGetCurrentContext();       
        [self.lineColor set];//设置下划线颜色 这里是红色 可以自定义        
        CGFloat y = CGRectGetHeight(self.frame);
        CGContextMoveToPoint(context, 0, y);        
        CGContextAddLineToPoint(context, CGRectGetWidth(self.frame), y);
        //设置线的宽度        
        CGContextSetLineWidth(context, 1);     
        //渲染 显示到self上      
        CGContextStrokePath(context);
    }
}
-(void)showUnderLine:(UIColor *)lineColor{
    _showunderline=YES;
    _lineColor=lineColor;
    [self setNeedsDisplay];
}
-(UIColor *)lineColor{
    if (!_lineColor) {
        _lineColor =[UIColor grayColor];
    }
    return _lineColor;
}
@end

```