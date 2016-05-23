# 判断NSString字符串或NSArray数组是否为空

 
---
```objc
Update更新：2016年5月20日 By {MISSAJJ琴瑟静听}
```
## 条件判断的一些注意点
```objc
1.判断一个数组中是否有具体内容
1> 正确
if (array.count) {

}

2> 错误
if (array) {

}

2.判断一个字符串是否有具体内容
1> 正确
if (string.length) {

}

2> 错误
if (string) {

}

```
 

##判断NSString字符串的最佳方法

```objc
#pragma mark ======ios 判断字符串为空和只为空格解决办法==
/**
 *  @author MISSAJJ, 15-11-25 
 */
+ (BOOL)isBlankString:(NSString *)string{
    
    //字符串的长度为0表示空串
    if (string.length == 0) {
        return YES;
    }
    if (string == nil) {
        return YES;
    }
    
    if (string == NULL) {
        return YES;
    }
    
    if ([string isEqualToString:@""]) {
        return YES;
    }
    if ([string isKindOfClass:[NSNull class]]) {
        return YES;
    }
    
    if ([[string stringByTrimmingCharactersInSet:[NSCharacterSet whitespaceAndNewlineCharacterSet]] length]==0) {
        return YES;
    }
    return NO;
}

```
##判断数组为空的最佳方法
```objc
/**
 *  @author MISSAJJ, 15-11-25 
 */
#pragma mark ======ios 判断数组为空的解决办法==
+ (BOOL)isBlankArray:(NSArray *)array{

    if (array == nil) {
        return YES;
    }

    if ([array isKindOfClass:[NSNull class]]) {
        return YES;
    }

    if (array.count == 0) {
        return YES;
    }
    
    return NO;
}
```