---
title: MD5
date: 2016-07-21 11:05:41
tags:
- MD5
- NSString
- NSData
---
由NSString、NSData 生成的MD5字符串
```objc
@interface NSString (MD5)
-(NSString*)MD5;

@end
@interface NSData (MD5)
-(NSString*)MD5;
@end
```

<!-- more -->
```objc
#import <CommonCrypto/CommonDigest.h>

@implementation NSString (MD5)

-(NSString * )MD5{
    if (!self.length) {
        NSLog(@"string not be nil");
        return nil;
    }
    // Create pointer to the string as UTF8
    const char * ptr = [self UTF8String];
    // Create byte array of unsigned chars
    unsigned char md5Buffer[CC_MD5_DIGEST_LENGTH];
    // Create 16 byte MD5 hash value, store in buffer
    CC_MD5(ptr,(CC_LONG)strlen(ptr),md5Buffer);
    // Convert MD5 value in the buffer to NSString of hex values
    NSMutableString * output = [NSMutableString stringWithCapacity:CC_MD5_DIGEST_LENGTH*2];
    for (int i = 0; i<CC_MD5_DIGEST_LENGTH; i++) {
        [output appendFormat:@"%02x",md5Buffer[i]];
    }

    return output;
}

@end

@implementation NSData (MD5)

-(NSString * )MD5{
    if (!self.length) {
         NSLog(@"data not be nil");
        return nil;
    }
    // Create byte array of unsigned chars
    unsigned char md5Buffer[CC_MD5_DIGEST_LENGTH];
     // Create 16 byte MD5 hash value, store in buffer
    CC_MD5(self.bytes, (CC_LONG)self.length, md5Buffer);
     // Convert unsigned char buffer to NSString of hex values
    NSMutableString * output =[NSMutableString stringWithCapacity:CC_MD5_DIGEST_LENGTH*2];
    for (int i = 0 ; i<CC_MD5_DIGEST_LENGTH; i++) {
        [output appendFormat:@"%02x",md5Buffer[i] ];
    }
    return output;

}
@end

```
