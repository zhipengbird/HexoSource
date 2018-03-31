---
title: TableView表格学习笔记  
date: 2016-11-04 11:26:00  
tags:  
- Tableview  
---


### UITableViewCell学习

#### 选中状态颜色设置
  1. 系统自带的效果：
```objc
  typedef NS_ENUM(NSInteger, UITableViewCellSelectionStyle) {
    UITableViewCellSelectionStyleNone,//没有效果
    UITableViewCellSelectionStyleBlue,//蓝色
    UITableViewCellSelectionStyleGray,//灰色
    UITableViewCellSelectionStyleDefault NS_ENUM_AVAILABLE_IOS(7_0)//灰色
};

```
2. 改变选中时的背景色
```objc
self.selectionStyle= UITableViewCellSelectionStyleBlue;
self.selectedBackgroundView=[[UIView alloc]initWithFrame:self.bounds];
self.selectedBackgroundView.backgroundColor=[UIColor purpleColor];
self.multipleSelectionBackgroundView =[[UIView alloc]initWithFrame:self.bounds];
self.multipleSelectionBackgroundView.backgroundColor=[UIColor cyanColor];
```
3. 选中状态背景设置的常用属性     
*   `@property (nonatomic, strong, nullable) UIView *backgroundView;`
    当表格的样式处于`UITableViewStylePlain`时，该属性为空时，处于`UITableViewStyleGrouped`样式时，为非空。该背景视图将添加到其他视图的底部。

*   `@property (nonatomic, strong, nullable) UIView *selectedBackgroundView;`
       当表格的样式处于`UITableViewStylePlain`时，该属性为空时，处于`UITableViewStyleGrouped`样式时，为非空。当`backgroundView`不为空时，该视图将作为一个子视图直接添加到`backgroundView`上面，或者添加到其他视图的底部。只有当cell处于选中状态下才会添加。调用`-setSelected:animated:`函数，将会使`selectedBackgroundView`有淡入淡出的动画效果。

*   `@property (nonatomic, strong, nullable) UIView *multipleSelectionBackgroundView NS_AVAILABLE_IOS(5_0);`

      如果不为空，该视图将会替代`selectedBackgroundView`,在多选状态下。

    4. 分隔线的样式

    ```objective-c
     typedef NS_ENUM(NSInteger, UITableViewCellSeparatorStyle) {
         UITableViewCellSeparatorStyleNone,
         UITableViewCellSeparatorStyleSingleLine,
         UITableViewCellSeparatorStyleSingleLineEtched   // This separator style is only supported for grouped style table views currently
     } __TVOS_PROHIBITED;
    ```

    ```objective-c
     //设置分隔线的缩进
     @property (nonatomic) UIEdgeInsets                    separatorInset NS_AVAILABLE_IOS(7_0) UI_APPEARANCE_SELECTOR __TVOS_PROHIBITED; // allows customization of the separator frame
    ```

### 表格的相操作

##### 1. 表格的删除操作

实现表格的如下代理方法：

```objc
-(BOOL)tableView:(UITableView *)tableView canEditRowAtIndexPath:(NSIndexPath *)indexPath{
    return YES;
}
-(NSString *)tableView:(UITableView *)tableView titleForDeleteConfirmationButtonForRowAtIndexPath:(NSIndexPath *)indexPath{
  //设置左滑后，显示的操作说明，这里可以换成其操作
    return @"delte";
}
```
```objc
-(void)tableView:(UITableView *)tableView commitEditingStyle:(UITableViewCellEditingStyle)editingStyle forRowAtIndexPath:(NSIndexPath *)indexPath{  
  //点击该操作后的处理，我这里所做的是删除操作，记住若要删除一个cell，同时也要对数据源进行更改
  [ _ datasource removeObjectAtIndex:indexPath.row ];
  [tableView deleteRowsAtIndexPaths:@[indexPath] withRowAnimation:UITableViewRowAnimationLeft];
}
-(UITableViewCellEditingStyle)tableView:(UITableView *)tableView editingStyleForRowAtIndexPath:(NSIndexPath * )indexPath{
  //设置编辑的样式
    return UITableViewCellEditingStyleDelete;
}

```

如果左滑后要出现多个操作按钮该怎么处理呢，iOS8以后苹果官司方新增加了个类`UITableViewRowAction` ,这个类该怎么用，用在哪里？

##### 2 . `UITableViewRowAction` 使用

```objective-c
NS_CLASS_AVAILABLE_IOS(8_0) __TVOS_PROHIBITED @interface UITableViewRowAction : NSObject <NSCopying>

+ (instancetype)rowActionWithStyle:(UITableViewRowActionStyle)style title:(nullable NSString * )title handler:(void (^)(UITableViewRowAction * action, NSIndexPath * indexPath))handler;

@property (nonatomic, readonly) UITableViewRowActionStyle style;
@property (nonatomic, copy, nullable) NSString *title;
@property (nonatomic, copy, nullable) UIColor *backgroundColor; // default background color is dependent on style
@property (nonatomic, copy, nullable) UIVisualEffect* backgroundEffect;
@end
```

​    该类非常简单，事件的响应使用block回调用，事件的名称以及背景色都可以自定义，那么在表格中哪里使用呢？

   我们只需要简单实现表格的`- (nullable NSArray<UITableViewRowAction *> *)tableView:(UITableView *)tableView editActionsForRowAtIndexPath:(NSIndexPath *)indexPath NS_AVAILABLE_IOS(8_0)`这个代理方法。

```objective-c
// 设置显示多个按钮
- (NSArray<UITableViewRowAction *> *)tableView:(UITableView *)tableView editActionsForRowAtIndexPath:(NSIndexPath *)indexPath {
    //    UITableViewRowAction 通过此类创建按钮
    /*
     * 1. 我们在使用一些应用的时候，在滑动一些联系人的某一行的时候，会出现删除、置顶、更多等等的按钮，在iOS8之前，我们都需要自己去实现。到了iOS8，系统已经写好了，只需要一个代理方法和一个类就搞定了

     * 2. iOS8的协议多了一个方法，返回值是数组的tableView:editActionsForRowAtIndexPath:方法，我们可以在方法内部写好几个按钮，然后放到数组中返回，那些按钮的类就是UITableViewRowAction

     * 3. 在UITableViewRowAction类，我们可以设置按钮的样式、显示的文字、背景色、和按钮的事件（事件在Block中实现）

     * 4. 在代理方法中，我们可以创建多个按钮放到数组中返回，最先放入数组的按钮显示在最右侧，最后放入的显示在最左侧

     * 5. 注意：如果我们自己设定了一个或多个按钮，系统自带的删除按钮就消失了...
     * /
    UITableViewRowAction * deleteRowAction = [UITableViewRowAction rowActionWithStyle:UITableViewRowActionStyleDefault title:@"删除" handler:^(UITableViewRowAction *  action, NSIndexPath *  indexPath) {
            //事件回调用处理
    }];
    deleteRowAction.backgroundColor = [UIColor redColor];

    UITableViewRowAction * topRowAction = [UITableViewRowAction rowActionWithStyle:UITableViewRowActionStyleDefault title:@"置顶" handler:^(UITableViewRowAction *  action, NSIndexPath * indexPath) {
       //事件回调用处理
    }];
    topRowAction.backgroundColor = [UIColor blueColor];

    UITableViewRowAction * moreRowAction = [UITableViewRowAction rowActionWithStyle:UITableViewRowActionStyleNormal title:@"更多" handler:^(UITableViewRowAction *  action, NSIndexPath * indexPath) {
            //事件回调用处理
    }];

    return @[deleteRowAction,topRowAction,moreRowAction];
}
```

##### 3. 表格的单选与多选的设置

```objective-c
_groupNumberTableview.allowsSelection=YES; // 是否允许选中
_groupNumberTableview.editing=YES;//是否处于编辑状态
_groupNumberTableview.allowsMultipleSelection=YES;//是否允许多选
_groupNumberTableview.allowsMultipleSelectionDuringEditing=YES;//是否允许在编辑状态下多选
```

### 动态添加headerview

```objc
UIView *headerView = _tableView.tableHeaderView;  
headerView.height = 0;  
[_tableView beginUpdates];  
[_tableView setTableHeaderView:headerView];// 关键是这句话  
[_tableView endUpdates];
```
这两句话可以让其过渡有动画效果

