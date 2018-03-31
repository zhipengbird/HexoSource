---
layout: "post"
title: "AVAudioPlayer学习"
date: "2016-04-17 00:08"
tags:
- AVAudioPlayer
- AVAudioSession
---
## AVAudioPlayer 使用方式

>音频播放是很多应用程序的常见需求，AVFoundation让这一功能变得非常简单，这一点得归功于AVAudioPlayer这个类。这个类的实例提供了一种简单地从广西或内存中播放音频的方法。虽接口简单，但功能强大 并且在mac和iOS系统中经常被作为实现音频播放的最佳选择  
 AVAudioPlayer 构建于CoreAudio中的C-based Audio Queue Services的最顶层，所以它可以提供你在AVAudio Queue Services 中所能找到的核心功能，比如播放、循环甚至音频计量，但其也有不足之处如：需要从网络流中播入音频、需要访问原始音频样本或者更低的延时时它不能胜任些重任。

<!-- more -->
__创建实例对象的方式方法__  
1. 使用包含要播放音频的内存版本的NSData `initWithData:error:`  
2. 使用本地音频文件的NSURL`initWithContentsOfURL:error:`  
***在用上述方法返回一个有效实例时，建议调用`prepareToPlay`方法。如此一来会取得需要的音频硬件并预加载`Audio Queue`的缓冲区***

__对播放实例的控制操作__  
1. 基础功能：`play` 、`pause`、`stop`  
2. 修改实例的音量：`volume` 范围值0.0~1.0之间的浮点值  
3. 修改实例的`pan`值，允许使用立体声：`pan`值 范围：-1.0~1.0之间的浮点值  
4. 调整播放速率：`rate` 范围值：0.5(半速)~2.0(2倍速)  
5. 设置循环次数属性实现无缝循环：`numberOfLoops`设置一个大于0的数n，可以循环n次，相反若设成-1，会无限次循环  
6. 进行音频计量

__配置音频会话__  
1. 播放音频时切换设备侧面的“铃音/静音”按钮，可以音频在这两种状态下输出  
2. 播放音频时按下`LOCK`锁，进入后台，音频可以正常输出    

__要实现上面两个需要，我们需要做如下工作:__  
*  在`didFinishLaunchingWithOptions`方法里配置AVAudioSession,  
    正确设置音频会话分类为`AVAudioSessionCategoryPlayback` 并启动会话

```objc
AVAudioSession * session  = [AVAudioSession sharedInstance];
    NSError * error;
    if (![session setCategory:AVAudioSessionCategoryPlayback error:& error]) {
        NSLog(@"%@",error.localizedDescription);
    }
    if (![session setActive:YES error:&error]) {
        NSLog(@"%@",error.localizedDescription);
    }
```

* 在`info.plist`文件中添加键值，该值表示应用程序允许在后台播放音频内容

```objc  
<key>UIBackgroundModes</key>
	<array>
		<string>audio</string>
	</array>
```

__音频会话中断通知处理__

> 测试你的应用程序是否做了会话中断处理：  
> 1. 在真机上运行你的应用并播放音频  
> 2. 当音频处于播放状态，从另一台设备上向当前设备发起电话或FaceTime以造成音频会话中断  
> 3. 中断电话发起或FaceTime  
当中断时，播放的音频会慢慢消失和暂停，这一效果系统自动实现，当中断恢复后，查看我的的应用音频是否还会自动继续播放呢
如果没有，我们需要注册`AVAudioSessionInterruptionNotification`通知，并处理该通知  

注册监听

```objc
//        监听中断通知
        [[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(handleInterruption: ) name:AVAudioSessionInterruptionNotification object:[AVAudioSession sharedInstance]];
```

处理中断通知：

```objc
-(void)handleInterruption:(NSNotification*)notification{

//    首先通过检索AVAudioSessionInterruptionTypeKey来确定中类型（AVAudioSessionInterruptionType）,该类型用于表中断的开始或是结束
    NSDictionary * userinfo  = notification.userInfo;
    AVAudioSessionInterruptionType type = [userinfo[AVAudioSessionInterruptionTypeKey] unsignedIntegerValue];
    if (type== AVAudioSessionInterruptionTypeBegan) {
    //中断音频播放
    }else {
        AVAudioSessionInterruptionOptions options =[userinfo[AVAudioSessionInterruptionOptionKey] unsignedIntegerValue];
        if (options == AVAudioSessionInterruptionOptionShouldResume) {
      //在这里继续播放音频   
        }
    }
}

```

_线路改变通知处理_

> 如何确保应用程序对线路变换做出正确的响应。在iOS设备上添加或移除音频输入或输出线路时，会发现线路改变。有多重因会导致线路变化，如用户插入耳机或断开ＵＳＢ麦克风，这些事件的发生时，音频会根据情况改变输入输出线路，同时会广播一个描述该变化的通知给所有相关的监听器。  
> 我们做这么一个小测试：运行你的应用，并播放音频，在播放期间插入耳机，音频输出线路变为耳机插孔并继续正常播放，这是我们希望的效果。断开耳机连接。音频线路再次回到设备的内置扬声器，我们再次听到了声音。虽然这和我们预期的效果一样，不过按苹果公司的相关文档，该音频应处理静音状态。当用插入耳机时隐含的意思是用户不希望外界听到具体的音频内容，这就意味着用户断开耳机，播放的内容需要继续保密，所有我们需要停止音频播放。    


监听线路改变通知

```objc
    [[NSNotificationCenter defaultCenter]addObserver:self selector:@selector(handleRouteChange:) name:AVAudioSessionRouteChangeNotification object:[AVAudioSession sharedInstance]];
```

处理通知：

```objc
-(void)handleRouteChange:(NSNotification*)notification{
//    通过AVAudioSessionRouteChangeReasonKey来确认发现改变的原因
//    我们在这里需要注意的是耳机中断事件
    NSDictionary * userinfo =notification.userInfo;
    AVAudioSessionRouteChangeReason reason = [userinfo[AVAudioSessionRouteChangeReasonKey] unsignedIntegerValue];
    if (reason ==AVAudioSessionRouteChangeReasonOldDeviceUnavailable) {
//        知道有设备断开连接后，需要向userinfo字典提出请求，获取其中用于描述前一线路的AVAudioSessionRouteDescription。线路的描述信息整合在一个输入NSArray和一个输出NSArray中，数组中的实例都是AVAudioSessionPortDescription,用于描术不同的I/O接口属性。
        AVAudioSessionRouteDescription * previousRoute  = userinfo[AVAudioSessionRouteChangePreviousRouteKey];
        AVAudioSessionPortDescription * previousPort  = previousRoute.outputs[0];
        NSString * type =  previousPort.portType;
        if ([type isEqualToString:AVAudioSessionPortHeadphones]) {
      //   如果需要日更新界面，需要使用主线去更新，当前线程可以是后台线程
                dispatch_async(dispatch_get_main_queue(), ^{

                });

            }
        }

    }
}
```
