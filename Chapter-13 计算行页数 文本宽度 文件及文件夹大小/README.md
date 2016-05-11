#Chapter-13 计算行页数 文本宽度 文件及文件夹大小
---
```objc
Update更新：2016年5月9日 By {MISSAJJ琴瑟静听}
```

## 计算总行数\总页数
```objc
总数 : 2476
每页显示的最大数量 : 35
总页数 :  (2476 + 35 - 1) / 35
pagesCount = (总数  +  每页显示的最大数量 - 1) / 每页显示的最大数量

总数 : 1660
每一行显示的最大数量 : 30
总行数 : (1660 + 30 - 1) / 30
rowsCount = (总数  +  每行显示的最大数量 - 1) / 每行显示的最大数量
```

## 计算某个文件\文件夹的大小
```objc
@implementation NSString (XMGExtension)
//- (unsigned long long)fileSize
//{
//    // 总大小
//    unsigned long long size = 0;
//
//    // 文件管理者
//    NSFileManager *mgr = [NSFileManager defaultManager];
//
//    // 文件属性
//    NSDictionary *attrs = [mgr attributesOfItemAtPath:self error:nil];
//
//    if ([attrs.fileType isEqualToString:NSFileTypeDirectory]) { // 文件夹
//        // 获得文件夹的大小  == 获得文件夹中所有文件的总大小
//        NSDirectoryEnumerator *enumerator = [mgr enumeratorAtPath:self];
//        for (NSString *subpath in enumerator) {
//            // 全路径
//            NSString *fullSubpath = [self stringByAppendingPathComponent:subpath];
//            // 累加文件大小
//            size += [mgr attributesOfItemAtPath:fullSubpath error:nil].fileSize;
//        }
//    } else { // 文件
//        size = attrs.fileSize;
//    }
//
//    return size;
//}

- (unsigned long long)fileSize
{
    // 总大小
    unsigned long long size = 0;

    // 文件管理者
    NSFileManager *mgr = [NSFileManager defaultManager];

    // 是否为文件夹
    BOOL isDirectory = NO;

    // 路径是否存在
    BOOL exists = [mgr fileExistsAtPath:self isDirectory:&isDirectory];
    if (!exists) return size;

    if (isDirectory) { // 文件夹
        // 获得文件夹的大小  == 获得文件夹中所有文件的总大小
        NSDirectoryEnumerator *enumerator = [mgr enumeratorAtPath:self];
        for (NSString *subpath in enumerator) {
            // 全路径
            NSString *fullSubpath = [self stringByAppendingPathComponent:subpath];
            // 累加文件大小
            size += [mgr attributesOfItemAtPath:fullSubpath error:nil].fileSize;
        }
    } else { // 文件
        size = [mgr attributesOfItemAtPath:self error:nil].fileSize;
    }

    return size;
}
@end

XMGLog(@"%zd", @"/Users/xiaomage/Desktop".fileSize);
```

## 计算文字的宽度
```objc
CGFloat titleW = [字符串 sizeWithFont:字体大小].width;
CGFloat titleW = [字符串 sizeWithAttributes:@{NSFontAttributeName : 字体大小}].width;
```
