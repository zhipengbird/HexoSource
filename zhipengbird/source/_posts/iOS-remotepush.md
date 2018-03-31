---
title: iOS 远程推送
date: 2016-09-20 15:31:30
categories:
- iOS
tags:
- push 
---

# iOS 通知分类：
  1. remote-notification
        * silent remote notifications

  iOS 7在推送方面最大的变化就是允许，应用收到通知后在后台（background）状态下运行一段代码，可用于从服务器获取内容更新。功能使用场景：（多媒体）聊天，Email更新，基于通知的订阅内容同步等功能，提升了终端用户的体验。
  Remote Notifications 与之前版本的对比可以参考下面两张 Apple 官方的图片便可一目了然。
  ![iOS6](http://docs.jpush.cn/download/attachments/7865069/iOS6+push.jpg?version=1&modificationDate=1384927835000)
  ![iOS7](http://docs.jpush.cn/download/attachments/7865069/iOS7.png?version=1&modificationDate=1384927835000)
  如果只携带content-available: 1 不携带任何badge，sound 和消息内容等参数，则可以不打扰用户的情况下进行内容更新等操作即为“Silent Remote Notifications”。
  ![Slient Remote Notifications](http://docs.jpush.cn/download/attachments/7865069/silent.png?version=1&modificationDate=1384932801000)

    > 若有`content-available: 1` 字段并带有任何badge，sound 和消息内容等参数一个或多个，都会在手机端有一个的表现行式（如声单，提示框，badge 并会调用：`[UIApplicationDelegate application:didReceiveRemoteNotification:fetchCompletionHandler:]`方法
  若只带`content-available: 1`字段只会调用`[UIApplicationDelegate application:didReceiveRemoteNotification:fetchCompletionHandler:]`,在手机端没有其他的表现。


  2. 不同版本的通知注册及回调用情况
      1. iOS8 以前
      ```objc
        [[UIApplication sharedApplication] registerForRemoteNotificationTypes:(UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeAlert)];
      ```
      2. iOS8~iOS9
        ```objc
        UIUserNotificationSettings * settings = [UIUserNotificationSettings settingsForTypes:( UIUserNotificationTypeBadge | UIUserNotificationTypeSound | UIUserNotificationTypeAlert)      categories:nil];
       [[UIApplication sharedApplication] registerUserNotificationSettings:settings];

       [[UIApplication sharedApplication] registerForRemoteNotifications];
        ```
        3. iOS10
          ```objc
          // iOS10.0 register remote notification
          [[UIApplication sharedApplication]registerForRemoteNotifications];
       [[UNUserNotificationCenter currentNotificationCenter]requestAuthorizationWithOptions:UNAuthorizationOptionAlert|UNAuthorizationOptionBadge|UNAuthorizationOptionSound completionHandler:^(BOOL granted, NSError * _ Nullable error) {
           if (granted) {
               [UNUserNotificationCenter currentNotificationCenter].delegate= self;
           }

          }];
        }
          ```
        > 在iOS10，苹果将用户通知相关操作集合在了`UserNotifications.framework`中，通知的回调用采用代理方式。


        注册通知成功后，Token的获取回调用：
        ```objc
        - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken NS_AVAILABLE_IOS(3_0){
          NSString * strDeviceToken =[[deviceToken description] stringByTrimmingCharactersInSet:[NSCharacterSet characterSetWithCharactersInString:@"< >"]];
          NSLog(@"APNS token:%@", strDeviceToken);
        }
        ```
        注册通知失败的回调用：
        ```objc
        - (void)application:(UIApplication *)application didFailToRegisterForRemoteNotificationsWithError:(NSError *)error NS_AVAILABLE_IOS(3_0);
        ```
        接收到通知的回调用处理方法：
        iOS10以前：
        ```objc
        // 接收到远程通知
        - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo;
        // 接收到本地通知
        - (void)application:(UIApplication *)application didReceiveLocalNotification:(UILocalNotification *)notification ；
        ```
        iOS10的回调：
        实现UNUserNotificationCenterDelegate的代理方法：这些代理方法是可选的。
        ```objc
        // The method will be called on the delegate only if the application is in the foreground. If the method is not implemented or the handler is not called in a timely manner then the notification will not be presented. The application can choose to have the notification presented as a sound, badge, alert and/or in the notification list. This decision should be based on whether the information in the notification is otherwise visible to the user.
        - (void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler __IOS_AVAILABLE(10.0) __TVOS_AVAILABLE(10.0) __WATCHOS_AVAILABLE(3.0);


        // The method will be called on the delegate when the user responded to the notification by opening the application, dismissing the notification or choosing a UNNotificationAction. The delegate must be set before the application returns from applicationDidFinishLaunching:.
        - (void)userNotificationCenter:(UNUserNotificationCenter *)center didReceiveNotificationResponse:(UNNotificationResponse *)response withCompletionHandler:(void(^)())completionHandler __IOS_AVAILABLE(10.0) __WATCHOS_AVAILABLE(3.0) __TVOS_PROHIBITED{

          NSDictionary * userinfo= response.notification.request.content.userInfo;
            [self analysisNotification:userinfo];
            // TODO:: 如果有用户操作，可获取到对应的动作类型，并进行相关处理、
            // 例如
                if ([response.notification.request.content.categoryIdentifier isEqualToString:@"replaymessage"]) {
           UNTextInputNotificationResponse * tresponse =(UNTextInputNotificationResponse*)response;
           NSLog(@"%@",tresponse.userText);
            //        get the user input text to dosomething

       }

        }

        ```
