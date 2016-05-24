# 使用Xib引入模型的自定义控件案例

 

---
```objc
Update更新：2016年5月20日 By {MISSAJJ琴瑟静听}
```

###Xib课程笔记思维导图：

![image](Images/xib笔记流程图.png)

###Xib的创建和设置：

- 1,创建Xib文件

![image](Images/Xib文件创建.png)

- 2,创建与Xib对应的类文件

(注意:必须要和Xib文件同名)

![image](Images/Xib类文件创建.png)

- 3,创建与Xib对应的类文件

![image](Images/Xib设置.png)  

###Xib的使用注意点：
 
Xib只能描述软件界面,必须创建一个和Xib文件同名的类文件来管理这个xib

- 1.不能通过alloc]init创建
- 2.通常提供类方法 
- 3.不会init和initWithFrame 
- 4.会执行initWithCoder和awakeFromNib


###Xib的加载方式：
- 1.如果一个View是从xib中加载出来的,没有设置尺寸,那么在xib中描述的尺寸,就是该View的尺寸
- 2.如果mainBundle作为参数,可以传入nil.(了解一下就可以)

1、加载方式一:(常用)
```objc
    UIView *newsView = [[[NSBundle mainBundle] loadNibNamed:@"News" owner:nil options:nil] firstObject];
    [self.view addSubview:newsView];
```

2、加载方式二:(不常用,了解一下就可以)
```objc
    UINib *nib = [UINib nibWithNibName:@"News" bundle:[NSBundle mainBundle]];
    UIView *newsView1 = [[nib instantiateWithOwner:nil options:nil] firstObject];
    newsView1.frame = CGRectMake(0, 110, 375, 100);
    [self.view addSubview:newsView1];
```
3、加载方式三: 快速宏加载

- 宏定义代码

```objc
//加载nib宏
#define LoadNib(nibName) \
[[NSBundle mainBundle]loadNibNamed:(nibName) owner:self options:nil][lastObject]
```

- 使用方法

```objc
//只需要传一个NibNamed的字符串
LoadNib(@"MASignAndLoginViewController");

```

###快速宏加载Xib的案例

```objc
"程序猿的偷懒,最终为的是更效率!" 同意这句话的童鞋请举手!^_^
以下这个快速宏加载Xib的案例就是MISSAJJ为了偷懒而写的,分享给大家拓展思路

```

- MISSAJJ做屏幕适配的方案:

```objc
 本屏幕适配方法用在iPhone上的话只需在xib文件内准备4，5尺寸两套布局,
 因为5，6，6Plus三种屏幕的尺寸宽高比是差不多的，
 所以可以在5的基础上按比例放大来兼容6和6Plus的屏幕,4为单独的一套布局
 比例按照iphone5的比例，iphone4单独做xib适配比例为1.0
 本方法效率高，也方便后期维护,但只适用于4，5，6，6Plus,不适用iPad.
 参考文献: http://blog.it985.com/5121.html
 
 ```
 
 - xib文件如图

![image](Images/MISSAJJ快速宏加载xib案例.png)


- 快速宏加载nib的宏定义

```objc
//加载iphone4的xib界面:[1]单独为iphone4创建的界面, [0]为iphone5界面,用其缩放适配其他大屏幕
#define IPHONE4BUNDLE(nibName) \
[[NSBundle mainBundle]loadNibNamed:(nibName) owner:self options:nil][1]
```

- 在与Xib对应的viewController以loadView方法判定加载读取哪一个xib内的view


```objc
-(void)loadView{
    
    [super loadView];
  
    //单独对iPhone4适配
    if (kDeviceHeight == 480) {

        self.view = IPHONE4BUNDLE(@"MASignAndLoginViewController"); 
        
    } 
}
```


###使用Xib引入模型的自定义控件案例

###1,Xib文件

![image](Images/案例-Xib文件.png)

- Xib对应的类.h文件

```objc 
#import <UIKit/UIKit.h>
@class MAProduct;
@interface MAProductView : UIView

// 快速创建类方法
+ (instancetype)productView;

// 提供模型接口
@property (nonatomic, strong) MAProduct *product;

@end

```
- Xib对应的类.m文件

```objc 
#import "MAProductView.h"
#import "MAProduct.h"

@interface MAProductView ()

@property (weak, nonatomic) IBOutlet UIImageView *iconView;
@property (weak, nonatomic) IBOutlet UILabel *titleLabel;

@end

@implementation MAProductView

#pragma mark - 快速通过xib创建对象的类方法
+ (instancetype)productView
{
    return [[[NSBundle mainBundle] loadNibNamed:NSStringFromClass(self) owner:nil options:nil] firstObject];
}

//设置模型传值
- (void)setProduct:(MAProduct *)product
{
    //这一步很重要, 否则传值为空就无数据
    _product = product;
    
    // 给子控件设置数据
    self.iconView.image = [UIImage imageNamed:product.icon];
    self.titleLabel.text = product.title;
}
@end
```
- 相关注意点:


```objc
// 如果控件是通过xib或者storyboard加载出来的,那么就会执行该方法
// 作用:在initWithCoder方法中添加子控件
- (instancetype)initWithCoder:(NSCoder *)aDecoder
{
    if (self = [super initWithCoder:aDecoder]) {
        NSLog(@"%s", __func__);
        self.backgroundColor = [UIColor yellowColor];
    }
    return self;
}

// 所有xib中的控件都加载好之后会执行该方法
// 作用:初始化操作,比如设置背景,初始化一些数据
- (void)awakeFromNib
{
    [super awakeFromNib];
    
    self.backgroundColor = [UIColor yellowColor];
    
    NSLog(@"%s", __func__);
} 
```
- 其他注意点:


```objc
/*
#pragma mark - 设置数据的方法 (淘汰)
- (void)setIcon:(NSString *)iconName
{
    self.iconView.image = [UIImage imageNamed:iconName];
}

- (void)setTitle:(NSString *)title
{
    self.titleLabel.text = title;
}
*/

#pragma mark - 初始化方法 
(注意 : 使用Xib不需要初始化方法,代码创建才需要)
/*
- (instancetype)init
{
    if (self = [super init]) {
        NSLog(@"%s", __func__);
    }
    return self;
}

- (instancetype)initWithFrame:(CGRect)frame
{
    if (self = [super initWithFrame:frame]) {
        NSLog(@"%s", __func__);
    }
    return self;
}
*/
```
###2,模型文件

- 模型.h文件

```objc
#import <Foundation/Foundation.h>

@interface MAProduct : NSObject

// 商品的图片
@property (nonatomic, copy) NSString *icon;

// 商品的名字
@property (nonatomic, copy) NSString *title;

- (instancetype)initWithDict:(NSDictionary *)dict;
+ (instancetype)productWithDict:(NSDictionary *)dict;

@end
```
- 模型.m文件

```objc
#import "MAProduct.h"

@implementation MAProduct

/* 设置模型数据的一种方案:(淘汰)
- (instancetype)initWithIcon:(NSString *)icon title:(NSString *)title
{
    if (self = [super init]) {
        self.icon = icon;
        self.title = title;
    }
    return self;
}

+ (instancetype)productWithIcon:(NSString *)icon title:(NSString *)title
{
    return [[self alloc] initWithIcon:icon title:title];
}
 */
//设置模型数据的第二种方案 : (常用)
- (instancetype)initWithDict:(NSDictionary *)dict
{
    if (self = [super init]) {
        self.icon = dict[@"icon"];
        self.title = dict[@"title"];
    }
    return self;
}
+ (instancetype)productWithDict:(NSDictionary *)dict
{
    return [[self alloc] initWithDict:dict];
}

@end
```

###3, ViewController调用

- 调用代码

```objc
    /********************** 2.添加商品的View *****************************/
    // 1.创建商品的View
    MAProductView *productView = [MAProductView productView];
    productView.frame = CGRectMake(x, y, width, height);
    [self.shopCartView addSubview:productView];
    
    // 2.设置数据
    productView.product = self.products[index];

```
- 完整的ViewController代码

```objc
#import "ViewController.h"
#import "MAProduct.h"
#import "MAProductView.h"

@interface ViewController ()

// 购物车的View
@property (weak, nonatomic) IBOutlet UIView *shopCartView;

// 添加商品和移除商品的按钮
@property (weak, nonatomic) IBOutlet UIButton *removeProductBtn;
@property (weak, nonatomic) IBOutlet UIButton *addProductBtn;

// 商品的数据
@property (nonatomic, strong) NSArray *products;

@end

@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];
}

#pragma mark - 添加和移除商品
#pragma mark 添加商品
- (IBAction)addProduct:(UIButton *)sender {
    /********************** 1.常量 *****************************/
    // 1.1.定义一些常量
    CGFloat width = 70;
    CGFloat height = 100;
    
    // 1.2.计算常量
    CGFloat hMargin = (self.shopCartView.frame.size.width - 3 * width) / 2;
    CGFloat vMargin = (self.shopCartView.frame.size.height - 2 * height) / 1;
    NSInteger index = self.shopCartView.subviews.count;
    CGFloat x = index % 3 * (hMargin + width);
    CGFloat y = index / 3 * (vMargin + height);
    
    
    /********************** 2.添加商品的View *****************************/
    // 1.创建商品的View
    MAProductView *productView = [MAProductView productView];
    productView.frame = CGRectMake(x, y, width, height);
    [self.shopCartView addSubview:productView];
    
    // 2.设置数据
    productView.product = self.products[index];
    
    /********************** 3.判断按钮的状态 *****************************/
    // 3.1.判断添加商品的按钮状态
    sender.enabled = self.shopCartView.subviews.count != 6;
    // 3.2.让移除按钮可以点击
    self.removeProductBtn.enabled = YES;
}

#pragma mark 移除商品
- (IBAction)removeProduct:(UIButton *)sender {
    // 1.取出最后一个商品,并且移除掉
    UIView *lastProduct = [self.shopCartView.subviews lastObject];
    [lastProduct removeFromSuperview];
    
    // 2.判断按钮的状态
    // 2.1.判断移除按钮的状态
    sender.enabled = self.shopCartView.subviews.count;
    // 2.2.判断添加按钮的状态
    self.addProductBtn.enabled = YES;
}

#pragma mark - 懒加载数据
- (NSArray *)products
{
    if (_products == nil) {
        // 1.获取plist文件的路径
        NSString *plistPath = [[NSBundle mainBundle] pathForResource:@"products.plist" ofType:nil];
        
        // 2.读取plist文件
        _products = [NSArray arrayWithContentsOfFile:plistPath];
        
        // 3.将字典转成模型对象
        NSMutableArray *tempArray = [NSMutableArray array];
        for (NSDictionary *dict in _products) {
            // 3.1.创建模型对象,并且给属性赋值
            // MAProduct *product = [MAProduct productWithIcon:dict[@"icon"] title:dict[@"title"]];(淘汰)
            
            MAProduct *product = [MAProduct productWithDict:dict];
            
            // 3.2.将模型对象放入数组中
            [tempArray addObject:product];
        }
        
        _products = tempArray;
    }
    
    return _products;
}

@end
```