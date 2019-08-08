---
title: Tips
date: 2018-12-05 17:44:44
tags:
---

给大家分享一个修改bug时遇到的情景和建议

UIImagePickerController的代理方法，
```objc
- (void)imagePickerController:(UIImagePickerController *)picker didFinishPickingMediaWithInfo:(NSDictionary<NSString *,id> *)info
```
不要通过picker push或者present一个新的controller

原因：
1：这个方法调用时picker 要执行dismiss操作（苹果官方文档建议）
2：如果，picker不dismiss操作，直接通过picker push或者present一个新的controller，会出现难以控制的异常情况