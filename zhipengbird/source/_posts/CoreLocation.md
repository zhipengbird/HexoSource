---
title: CoreLocation
date: 2017-03-22 14:06:22
categories: iOS 
tags: 
- Location 
---

# 获取定位信息
iOS开发者使用`CoreLocation.framework`框架进行定位非常简单`CoreLocation`框架的常用API主要有如下几个。

* `CLLocationManager`定位管理器类。
* `CLLocationManagerdelegate`该协议代表定位管理器的`delegate`协议。实现该协议的对象可负责处理`CLLocationManager`的定位事件。
* `CLLocation`该对象代表位置。该对象包含了当前设备的 *经度*、*纬度*、*高度*、*速度*、*路线*等信息还包含了该定位信息的水平精确度、垂直精确度以及时间戳信息。
* `CLHeading`该对象代表设备的移动方向。
* `CLRegion`该对象代表一个区域。一般程序不会直接使用该类而是使用它的两个子类即`CLCircularRegion`圆形区域和`CLBeaconRegion`蓝牙信号区。
除此之外`CoreLocation`框架还涉及一个`CLLocationCoordinate2D`结构体变量该结构体变量包含 *经度*、*纬度* 两个值。其中`CLLocation`对象的`coordinate`属性就是一个`CLLocationCoordinate2D`结构体变量。

# 获取位置信息
使用`CoreLocation.framework`进行定位只要如下3步即可。
* 创建`CLLocationManager`对象该对象负责获取定位相关信息。并为该对象设置一些必要的属性。
* 为`CLLocationManager`指定`delegate`属性该属性值必须是一个实现`CLLocationManagerDelegate`协议的对象。实现              `CLLocationManagerDelegate`协议时可根据需要实现协议中特定的方法。
* 调用`CLLocationManager`的`startUpdatingLocation`方法获取定位信息。定位结束时可调用`stopUpdatingLocation`方法结束获取定位信息

#解析：
*  `LLocationManager`*负责获取定位信息* 而`delegate`则 *负责处理定位事件* ——通过这些事件即可获取设备所在位置。

## CLLocationManager还提供了如下类方法来判断当前设备的定位相关服务状态。
   * `+ locationServicesEnabled`返回当前定位服务是否可用。

   * `+ deferredLocationUpdatesAvailable`返回延迟定位更新是否可用。

   * `+ significantLocationChangeMonitoringAvailable`返回重大位置改变监听是否可用。

   * `+ headingAvailable`返回该设备是否支持磁力计计算方向。

   * `+ isRangingAvailable`返回蓝牙信号范围服务是否可用。这是iOS 7新增的方法。

除此之外在使用CLLocationManager开始定位之前还可为该对象设置如下属性。
 * `pausesLocationUpdatesAutomatically`设置iOS设备是否可暂停定位来节省电池的电量。如果该属性设为“YES”则当iOS设备不再需要定位数据时iOS设备可以自动暂停定位。

* `distanceFilter`设置`CLLocationManager`的自动过滤距离。也就是说只有当设备在水平方向的位置改变超过该数值以米为单位指定的距离时才会生成一次位置改变的信号。

* `desiredAccuracy`设置定位服务的精度。该属性值支持
    * `kCLLocationAccuracyBestForNavigation`导航级的最佳精确度、
    * `kCLLocationAccuracyBest`最佳精确度、
    * `kCLLocationAccuracy NearestTenMeters`10米误差、
    * `kCLLocationAccuracyHundredMeters`百米误差、
    * `kCLLocationAccuracyKilometer`千米误差、
    * `kCLLocationAccuracyThreeKilometers`三千米误差等常量值。当然也可直接指定一个浮点数作为定位服务允许的误差。

* `activityType`设置定位数据的用途。该属性支持
    * `CLActivityTypeOther`定位数据作为普通用途、
    * `CLActivityTypeAutomotiveNavigation`定位数据作为车辆导航使用、
    * `CLActivityTypeFitness`定位数据作为步行导航使用
    * `CLActivityTypeOtherNavigation`定位数据作为其他导航使用这几个枚举值之一。
    
## CLLocation对象中包含如下属性这些属性包含了定位相关信息。
* `altitude`该属性表示当前设备的海拔高度单位是米。
* `coordinate`该属性返回一个`CLLocationCoordinate2D`结构体变量该结构体变量中包含*经度*、*纬度*信息。
* `course`该属性表示当前设备前进的方向。该值为0°表示向北90°表示向东180°表示向南270°表示向西。
* `horizontalAccuracy`该属性表明定位信息的水平精确度。将返回的坐标作为圆心并将水平精确度视为半径。真正的设备位置落在此圆内的某处。此圆越小位置就越精确此圆越大则位置越不精确。如果精确度为负值则表明测量精确度失败。

* `verticalAccuracy`该属性表明定位信息的垂直精确度。也就是说iOS设备的实际高度在该定位信息的高度加或减该属性值的范围内。
timestamp该属性返回定位信息的返回时间。
* `speed`该属性表示返回设备的移动速度单位是米/秒。实际上该属性适用于行车速度而不太适用于步行速度。


