#Chapter-07 NSDate NSCalendar时间日期换算
---
```objc
Update更新：2016年5月9日 By {MISSAJJ琴瑟静听}
```
## NSDateFormatter的作用
- NSString \* -> NSDate *

```obj
- (nullable NSDate *)dateFromString:(NSString *)string;
```

- NSDate \* -> NSString *

```objc
- (NSString *)stringFromDate:(NSDate *)date;
```

## 常见的日期格式
- http://www.cnblogs.com/mailingfeng/archive/2011/07/28/2120422.html

## NSString \* -> NSDate *
- 2015-11-20 09:10:05

```objc
// 时间字符串
NSString *string = @"2015-11-20 09:10:05";

// 日期格式化类
NSDateFormatter *fmt = [[NSDateFormatter alloc] init];
// 设置日期格式(为了转换成功)
fmt.dateFormat = @"yyyy-MM-dd HH:mm:ss";

// NSString * -> NSDate *
NSDate *date = [fmt dateFromString:string];

NSLog(@"%@", date);
```

- 11月-20号/2015年 09-10:05秒

```objc
// 时间字符串
NSString *string = @"11月-20号/2015年 09-10:05秒";

// 日期格式化类
NSDateFormatter *fmt = [[NSDateFormatter alloc] init];
fmt.dateFormat = @"MM月-dd号/yyyy年 HH-mm:ss秒";

NSLog(@"%@", [fmt dateFromString:string]);
```

- Tue May 31 17:46:55 +0800 2011

```objc
// 时间字符串
NSString *string = @"Tue May 31 17:46:55 +0800 2011";

// 日期格式化类
NSDateFormatter *fmt = [[NSDateFormatter alloc] init];
fmt.dateFormat = @"EEE MMM dd HH:mm:ss Z yyyy";
// fmt.dateFormat = @"EEE MMM dd HH:mm:ss ZZZZ yyyy";
// 设置语言区域(因为这种时间是欧美常用时间)
fmt.locale = [[NSLocale alloc] initWithLocaleIdentifier:@"en_US"];

NSLog(@"%@", [fmt dateFromString:string]);
```

- 1745645645645

```objc
// 时间戳 : 从1970年1月1号 00:00:00开始走过的毫秒数

// 时间字符串 - 时间戳
NSString *string = @"1745645645645";
NSTimeInterval second = string.longLongValue / 1000.0;

// 时间戳 -> NSDate *
NSDate *date = [NSDate dateWithTimeIntervalSince1970:second];
NSLog(@"%@", date);
```

## NSCalendar的注意点
```objc
#define iOS(version) ([UIDevice currentDevice].systemVersion.doubleValue >= (version))

NSCalendar *calendar = nil;
if ([UIDevice currentDevice].systemVersion.doubleValue >= 8.0) {
    calendar = [NSCalendar calendarWithIdentifier:NSCalendarIdentifierGregorian];
} else {
    calendar = [NSCalendar currentCalendar];
}

NSCalendar *calendar = nil;
if ([NSCalendar respondsToSelector:@selector(calendarWithIdentifier:)]) {
    calendar = [NSCalendar calendarWithIdentifier:NSCalendarIdentifierGregorian];
} else {
    calendar = [NSCalendar currentCalendar];
}
```

## NSDate \* -> NSString *
```objc
NSDate *date = [NSDate date];

NSDateFormatter *fmt = [[NSDateFormatter alloc] init];
fmt.dateFormat = @"yyyy年MM月dd号 HH:mm:ss";

NSString *string = [fmt stringFromDate:date];
```

## 获得日期元素
```objc
NSString *string = @"2015-11-20 09:10:05";

NSString *month = [string substringWithRange:NSMakeRange(5, 2)];

NSLog(@"%@", month);
```
```objc
// 时间字符串
NSString *string = @"2015-11-20 09:10:05";

// 日期格式化类
NSDateFormatter *fmt = [[NSDateFormatter alloc] init];
// 设置日期格式(为了转换成功)
fmt.dateFormat = @"yyyy-MM-dd HH:mm:ss";

// NSString * -> NSDate *
NSDate *date = [fmt dateFromString:string];

// 利用NSCalendar处理日期
NSCalendar *calendar = [NSCalendar currentCalendar];
NSInteger month = [calendar component:NSCalendarUnitMonth fromDate:date];
NSInteger hour = [calendar component:NSCalendarUnitHour fromDate:date];
NSInteger minute = [calendar component:NSCalendarUnitMinute fromDate:date];

NSLog(@"%zd %zd %zd", month, hour, minute);
```
```objc
// 时间字符串
NSString *string = @"2015-11-20 09:10:05";

// 日期格式化类
NSDateFormatter *fmt = [[NSDateFormatter alloc] init];
// 设置日期格式(为了转换成功)
fmt.dateFormat = @"yyyy-MM-dd HH:mm:ss";

// NSString * -> NSDate *
NSDate *date = [fmt dateFromString:string];

// 利用NSCalendar处理日期
NSCalendar *calendar = [NSCalendar currentCalendar];

NSCalendarUnit unit = NSCalendarUnitYear | NSCalendarUnitMonth | NSCalendarUnitDay | NSCalendarUnitHour | NSCalendarUnitMinute | NSCalendarUnitSecond;
NSDateComponents *cmps = [calendar components:unit  fromDate:date];

//    NSLog(@"%zd %zd %zd", cmps.year, cmps.month, cmps.day);
NSLog(@"%@", cmps);
```

## 日期比较
```objc
// 时间字符串
NSString *createdAtString = @"2015-11-20 11:10:05";
NSDateFormatter *fmt = [[NSDateFormatter alloc] init];
fmt.dateFormat = @"yyyy-MM-dd HH:mm:ss";
NSDate *createdAtDate = [fmt dateFromString:createdAtString];

// 手机当前时间
NSDate *nowDate = [NSDate date];

/**
 NSComparisonResult的取值
 NSOrderedAscending = -1L, // 升序, 越往右边越大
 NSOrderedSame,  // 相等
 NSOrderedDescending // 降序, 越往右边越小
 */
// 获得比较结果(谁大谁小)
NSComparisonResult result = [nowDate compare:createdAtDate];
if (result == NSOrderedAscending) { // 升序, 越往右边越大
    NSLog(@"createdAtDate > nowDate");
} else if (result == NSOrderedDescending) { // 降序, 越往右边越小
    NSLog(@"createdAtDate < nowDate");
} else {
    NSLog(@"createdAtDate == nowDate");
}
```
```objc
// 时间字符串
NSString *createdAtString = @"2015-11-20 09:10:05";
NSDateFormatter *fmt = [[NSDateFormatter alloc] init];
fmt.dateFormat = @"yyyy-MM-dd HH:mm:ss";
NSDate *createdAtDate = [fmt dateFromString:createdAtString];

// 手机当前时间
//    NSDate *nowDate = [NSDate date];

// 获得createdAtDate和nowDate的时间间隔(间隔多少秒)
//    NSTimeInterval interval = [nowDate timeIntervalSinceDate:createdAtDate];
NSTimeInterval interval = [createdAtDate timeIntervalSinceNow];
NSLog(@"%f", interval);
```
```objc
NSDateFormatter *fmt = [[NSDateFormatter alloc] init];
fmt.dateFormat = @"yyyy-MM-dd HH:mm:ss";

// 时间字符串
NSString *createdAtString = @"2015-11-01 09:10:05";
NSDate *createdAtDate = [fmt dateFromString:createdAtString];

// 其他时间
NSString *otherString = @"2015-10-31 08:56:45";
NSDate *otherDate = [fmt dateFromString:otherString];

// 获得NSCalendar
NSCalendar *calendar = nil;
if ([NSCalendar respondsToSelector:@selector(calendarWithIdentifier:)]) {
    calendar = [NSCalendar calendarWithIdentifier:NSCalendarIdentifierGregorian];
} else {
    calendar = [NSCalendar currentCalendar];
}

// 获得日期之间的间隔
NSCalendarUnit unit = NSCalendarUnitYear | NSCalendarUnitMonth | NSCalendarUnitDay | NSCalendarUnitHour | NSCalendarUnitMinute | NSCalendarUnitSecond;
NSDateComponents *cmps = [calendar components:unit fromDate:createdAtDate toDate:otherDate options:0];

NSLog(@"%@", cmps);
```
