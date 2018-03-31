---
title: constellation
date: 2017-03-08 10:44:58
categories:
tags:
---

```objc
- (NSString *) astrology {
    NSString *astroString = @"魔羯水瓶双鱼白羊金牛双子巨蟹狮子处女天秤天蝎射手魔羯";
    NSString *astroFormat = @"102123444543";
    NSString *result;

    NSDateComponents *components = [[NSCalendar currentCalendar] components:NSCalendarUnitDay | NSCalendarUnitMonth | NSCalendarUnitYear fromDate:self];

    long m = [components month];
    long d = [components day];

    result = [NSString stringWithFormat:@"%@座",[astroString substringWithRange:NSMakeRange(m * 2-(d < [[astroFormat substringWithRange:NSMakeRange((m-1), 1)] intValue] - (-19)) * 2,2)]];

    return result;
}
```

```objc
/**
 *  根据月和日的下标获取星座名
 *
 *  @param monthIndex 月的下标
 *  @param dayIndex   日的下标
 *
 *  @return 星座名
 */
- (NSString *)getConstellationNameByMonthIndex:(NSInteger)monthIndex dayIndex:(NSInteger)dayIndex {
    NSArray *constellations = @[@"水瓶座", @"双鱼座", @"白羊座", @"金牛座", @"双子座", @"巨蟹座", @"狮子座", @"处女座", @"天秤座", @"天蝎座", @"射手座", @"摩羯座"];
    NSInteger index;
    NSArray *conIndexs = @[@(20),@(19),@(21),@(20),@(21),@(22),@(23),@(23),@(23),@(24),@(23),@(22)];
    if ([[conIndexs objectAtIndex:monthIndex] integerValue] <= dayIndex + 1) {
        index = monthIndex;
    } else index = (monthIndex - 1 + 12) % 12;
    return [constellations objectAtIndex:index];
}

```
