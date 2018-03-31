---
title: xcode solutions
date: 2016-09-18 18:51:23
categories: Xcode8
tags:
---

1. 屏蔽系统日志
  `Xcode8`里边 `Edit Scheme`-> `Run` -> `Arguments`, 在`Environment Variables`里边添加
  `OS_ACTIVITY_MODE = Disable`

2. Xcode8注释快捷键失效注释快捷键 `⌘ ``+`` / `失效，在终端输入下面的命令，重启系统即可
```sh
sudo /usr/libexec/xpccachectl
```

`__aaa`
3. ios10相机等权限获取时的崩溃
  出现的提示信息有：
  ```
  This app has crashed because it attempted to access privacy-sensitive data without a usage description. The app's Info.plist must contain an NSCameraUsageDescription key with a string value explaining to the user how the app uses this data.
  ```
  意思是没有获取相机的权限，需要添加在plist文件中添加这项权限
  ```json
  // 相机权限
  <key>NSCameraUsageDescription</key>
  <string>CameraUsageDescription</string>
  ```
  以下是所有权限的相关键值：
  ```js
  // 蓝牙权限
  <key>NSBluetoothPeripheralUsageDescription</key>
	<string>BluetoothPeripheralUsageDescription</string>

  // 日历权限
	<key>NSCalendarsUsageDescription</key>
	<string>CalendarsUsageDescription</string>
  // 相机权限
	<key>NSCameraUsageDescription</key>
	<string>CameraUsageDescription</string>

  // 联系人
	<key>NSContactsUsageDescription</key>
	<string>ContactsUsageDescription</string>

  // 健康分享权限
	<key>NSHealthShareUsageDescription</key>
	<string>HealthShareUsageDescription</string>
  // 健康更新权限
	<key>NSHealthUpdateUsageDescription</key>
	<string>HealthUpdateUsageDescription</string>

  // HomeKit使用权限
	<key>NSHomeKitUsageDescription</key>
	<string>HomeKitUsageDescription</string>
  //  位置一直使用权限
	<key>NSLocationAlwaysUsageDescription</key>
	<string>LocationAlwaysUsageDescription</string>

  // 使用位置权限
	<key>NSLocationUsageDescription</key>
	<string>LocationUsageDescription</string>
  // 程序运行时使用位置权限
	<key>NSLocationWhenInUseUsageDescription</key>
	<string>LocationWhenInUseUsageDescription</string>

  // 获取appleMusic权限
	<key>NSAppleMusicUsageDescription</key>
	<string>AppleMusicUsageDescription</string>

  // 麦克锋使用权限
	<key>NSMicrophoneUsageDescription</key>
	<string>MicrophoneUsageDescription</string>

  // 陀螺仪使用权限
	<key>NSMotionUsageDescription</key>
	<string>MotionUsageDescription</string>

  // 相册使用权限
	<key>NSPhotoLibraryUsageDescription</key>
	<string>PhotoLibraryUsageDescription</string>

  // 提醒权限
	<key>NSRemindersUsageDescription</key>
	<string>RemindersUsageDescription</string>

  // Siri 使用权限
	<key>NSSiriUsageDescription</key>
	<string>SiriUsageDescription</string>

  // 语音识别权限
	<key>NSSpeechRecognitionUsageDescription</key>
	<string>SpeechRecognitionUsageDescription</string>

  // 视频权限
	<key>NSVideoSubscriberAccountUsageDescription</key>
	<string>VideoSubscriberAccountUsageDescription</string>

  // 音乐库权限
	<key>kTCCServiceMediaLibrary</key>
	<string>CCServiceMediaLibrary</string>
  ```
