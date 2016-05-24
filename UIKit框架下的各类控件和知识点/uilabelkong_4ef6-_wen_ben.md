# UILabel控件-文本
 
---
```objc
Update更新：2016年5月23日 By {MISSAJJ琴瑟静听}
```
###什么是UILabel

- UILabel极其常用，功能比较专一：显示文字

![image](Images/UILabel图片 1.png)

###UILabel知识点

![image](Images/UILabel.png)

###UILabel的常见属性
```objc
@property(nonatomic,copy)   NSString           *text; 
显示的文字

@property(nonatomic,retain) UIFont             *font; 
字体

@property(nonatomic,retain) UIColor            *textColor; 
文字颜色

@property(nonatomic)        NSTextAlignment    textAlignment; 
对齐模式（比如左对齐、居中对齐、右对齐） 

@property(nonatomic) NSInteger numberOfLines; 
文字行数

@property(nonatomic)        NSLineBreakMode    lineBreakMode;
换行模式
```


###UIFont

```objc
UIFont代表字体，常见创建方法有以下几个：
+ (UIFont *)systemFontOfSize:(CGFloat)fontSize;   系统默认字体
+ (UIFont *)boldSystemFontOfSize:(CGFloat)fontSize;  粗体
+ (UIFont *)italicSystemFontOfSize:(CGFloat)fontSize;  斜体

```


###UILabel代码
```objc
- (void)viewDidLoad {
    [super viewDidLoad];
    
    // 1.创建UILabel的对象
    UILabel *label = [[UILabel alloc] init];
    
    // 2.设置frame(位置和尺寸)
    label.frame = CGRectMake(100, 100, 175, 175);
    
    // 3.设置背景
    label.backgroundColor = [UIColor yellowColor];
    
    // 4.设置显示的文字
    label.text = @"Hello World Hello World Hello World Hello World Hello World Hello World Hello World ";
    
    // 5.设置文字的颜色
    label.textColor = [UIColor blueColor];
    
    // 6.设置label的行数
    label.numberOfLines = 0;
    
    // 7.设置字体的大小
    // label.font = [UIFont systemFontOfSize:14.0]; //系统默认
    // label.font = [UIFont boldSystemFontOfSize:18.0]; //粗体
    label.font = [UIFont italicSystemFontOfSize:18.0]; //斜体
    
    // 8.设置文字居中
    label.textAlignment = NSTextAlignmentCenter;
    
    // 9.省略号的位置或者剪切,字符串或者单词完整性
    label.lineBreakMode = NSLineBreakByCharWrapping;
    
    // 10.设置阴影
    // 10.1.设置阴影颜色
    label.shadowColor = [UIColor greenColor];
    
    // 10.2.设置阴影偏移量
    // CGPoint point = CGPointMake(<#CGFloat x#>, <#CGFloat y#>)
    label.shadowOffset = CGSizeMake(2, -2);
    
    // 将对象添加到控制器的View中
    [self.view addSubview:label];
}

```
###UILabel在Xib中的基本设置

- 要想让UILabel自动换行，设置Lines为0即可

![image](Images/UILabel基本设置1.png)

-  让UILabel的文字居中显示

![image](Images/UILabel基本设置2.png)

- UILabel更多设置
- 

![image](Images/UILabel基本设置3.png)


## 计算文字的宽度
```objc
CGFloat titleW = [字符串 sizeWithFont:字体大小].width;
CGFloat titleW = [字符串 sizeWithAttributes:@{NSFontAttributeName : 字体大小}].width;
```