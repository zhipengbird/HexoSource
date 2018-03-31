---
title: UIImagePickerController
date: 2016-11-17 19:12:27
categories: iOS
tags:
---

创建对象

```objc
//去相册选择图片
 UIImagePickerController * pickerController =[[UIImagePickerController alloc]init];
 pickerController.mediaTypes = @[(NSString *)kUTTypeImage,(NSString *)kUTTypeMovie,(NSString *)kUTTypeVideo];//设置选择的资源类型
 if ([UIImagePickerController isSourceTypeAvailable:UIImagePickerControllerSourceTypePhotoLibrary]) {
     pickerController.sourceType=UIImagePickerControllerSourceTypePhotoLibrary;
     pickerController.delegate=self;

     [self.navigationController presentViewController:pickerController animated:YES completion:^{

     }];
 }
```
代理方法实现：
```objc
-(void)imagePickerController:(UIImagePickerController *)picker didFinishPickingMediaWithInfo:(NSDictionary<NSString *,id> *)info{
    [picker dismissViewControllerAnimated:NO completion:nil];
    NSString * file= nil;
    NSString *mdeiaTye = [info objectForKey:UIImagePickerControllerMediaType]; //获取媒体类型
    if ([mdeiaTye isEqualToString:(NSString*)kUTTypeImage]) {
      //获取的是图片资源
        UIImage *image = [info objectForKey:UIImagePickerControllerOriginalImage];
        NSArray *paths = NSSearchPathForDirectoriesInDomains(NSCachesDirectory, NSUserDomainMask, YES);
        NSString *docPath = [paths lastObject];
        file = [docPath stringByAppendingPathComponent:@"output.png"];
        [[NSFileManager defaultManager]removeItemAtPath:file error:nil];
        NSData *imageData = UIImagePNGRepresentation(image);
        [imageData writeToFile:file atomically:YES];
        NSString* uuid = [[NSString stringWithFormat:@"tmp://%@", [[NSUUID UUID] UUIDString]] stringByAppendingPathExtension:[file pathExtension]];
        [[NSFileManager defaultManager] moveItemAtPath:file toPath:[FileDownloader cacheFileWithUrl:uuid] error:nil];

    }else {
      // 获取的是视频资源
        NSArray *paths = NSSearchPathForDirectoriesInDomains(NSCachesDirectory, NSUserDomainMask, YES);
        NSString *docPath = [paths lastObject];
        file = [docPath stringByAppendingPathComponent:@"output.mp4"];
        [[NSFileManager defaultManager]removeItemAtPath:file error:nil];
        NSURL * url =[info objectForKey:UIImagePickerControllerMediaURL];
        NSData * data =[NSData dataWithContentsOfURL:url];
        [data writeToFile:file atomically:YES];//将视频移到本应用的目录下
        NSString* uuid = [[NSString stringWithFormat:@"tmp://%@", [[NSUUID UUID] UUIDString]] stringByAppendingPathExtension:[file pathExtension]];
        [[NSFileManager defaultManager] moveItemAtPath:file toPath:[FileDownloader cacheFileWithUrl:uuid] error:nil];
        // 获取视频时长
        CGFloat duration = [self getVideoDuration:url];

    }
}

```
```objc
//获取视频时长
-(CGFloat) getVideoDuration:(NSURL*) URL {
    NSDictionary* opts = [NSDictionary dictionaryWithObject:[NSNumber numberWithBool:NO] forKey:AVURLAssetPreferPreciseDurationAndTimingKey];
    AVURLAsset *urlAsset = [AVURLAsset URLAssetWithURL:URL options:opts];
    float second = 0;
    second = urlAsset.duration.value/urlAsset.duration.timescale;
    return second;
}

```
```objc
// 获取文件大小
-(NSInteger) getFileSize:(NSString*) path {
    NSFileManager *filemanager = [[NSFileManager alloc]init];
    if([filemanager fileExistsAtPath:path]){
        NSDictionary* attributes = [filemanager attributesOfItemAtPath:path error:nil];
        NSNumber *theFileSize;
        if ( (theFileSize = [attributes objectForKey:NSFileSize]) )
            return [theFileSize intValue]/1024; else return -1;
    }
    else {
        return -1;
    }
}

```
