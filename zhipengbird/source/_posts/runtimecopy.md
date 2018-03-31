---
title: runtime - 实现对象的自动拷贝
date: 2018-01-06 23:21:28
tags:
---
在我们开发过程中，有时会遇到对象进行拷贝的情况，在对象属性少的情况下，我们可以直接在 `copyWithZone:/mutableCopyWithZone:` 方法里手动对复制后的对象的相应属性赋值，但在属性成员很多的情况及通过runtime在运行时关联属性时，我们这种手动赋值的方式就有点应赋不过来了。runtime是OC的一把利器，用得好可以帮我们节省很多事情。废话不多说，用代码说话。
1. 为对象增加一个扩展并遵守 `<NSCopying,NSMutableCopying>`协议 
    ```
    #import <objc/runtime.h>
    @interface Person()<NSCopying,NSMutableCopying>
    @end
    ```
2. 实现协议  
    
    ```
    -(id)copyWithZone:(NSZone * )zone{
        id objCopy = [[[self class] allocWithZone:zone] init];
        // 1.获取属性列表
        unsigned int propertyCount = 0;
        objc_property_t * propertyArray = class_copyPropertyList([self class], &propertyCount);
        
        for (int i=0; i<propertyCount; i++) {
            
            objc_property_t  property = propertyArray[i];
            
            // 2.属性名字
            const char * propertyName = property_getName(property);
            NSString * key = [NSString stringWithUTF8String:propertyName];
            
            // 3.通过属性名拿到属性值
            id value=[self valueForKey:key];
            NSLog(@"name:%s,value:%@",propertyName,value);
            
            // 4.判断 值对象是否响应copyWithZone
            if ([value respondsToSelector:@selector(copyWithZone:)]) {
                //5. 设置属性值
                [objCopy setValue:[value copy] forKey:key];
            }else{
                [objCopy setValue:value forKey:key];
            }
        }
        //*****切记需要手动释放
        free(propertyArray);
        return objCopy;
    }
    ``` 
    ```
    -(id)mutableCopyWithZone:(NSZone *)zone{
        id objcopy = [[[self class]allocWithZone:zone] init];
        //    1.获取属性列表
        unsigned int count = 0;
        objc_property_t* propertylist = class_copyPropertyList([self class], &count);
        
        for (int i = 0; i < count ; i++) {
            objc_property_t property = propertylist[i];
            //    2.获取属性名
            const char * propertyName = property_getName(property);
            NSString * key = [NSString stringWithUTF8String:propertyName];
            //    3.获取属性值
            id value = [self valueForKey:key];
            //    4.判断属性值对象是否遵守NSMutableCopying协议
            if ([value respondsToSelector:@selector(mutableCopyWithZone:)]) {
                //    5.设置对象属性值
                [objcopy setValue:[value mutableCopy] forKey:key];
            }else{
                [objcopy setValue:value forKey:key];
            }
        }
        //*****切记需要手动释放
        free(propertylist);
        return objcopy;
    
    }
    ```

通过以上两个方法的实现，可以应付对象的拷贝场景。
> 小结下:
    使用runtime机制实现对象的拷贝有以下几个步骤:
    * 导入<objc/runtime.h>头文件
    * 遵守`<NSCopying,NSMutableCopying>`协议
    * 实现`mutableCopyWithZone:/copyWithZone:`方法
        * 获取类的属性列表
        * 对属性列表进行遍历
            * 拿到属性
            * 拿到属性名
            * 通过属性名拿到属性值, 使用`valueForKey:`方法
            * 判断值对象是否响应协议方法，将值赋值给新对象的相应属性
        * 释放属性指针

