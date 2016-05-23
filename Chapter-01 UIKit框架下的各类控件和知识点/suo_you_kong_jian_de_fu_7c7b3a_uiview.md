#所有控件的父类:UIView
 
---
```objc
Update更新：2016年5月23日 By {MISSAJJ琴瑟静听}
 
```
所有控件的父类都是UIView，在开发中我们会将有共同属性的类抽取出一个父类（猫/狗/蟑螂抽出一个动物类）。苹果将拥有共同属性的控件类抽取出了UIView类，所有的控件都是继承自UIView类，UIButton/UILabel都继承自UIView(查看头文件)

UIView可以称之为控件/视图，屏幕上所有能看到的东西都是UIView，按钮（UIButton）、文本（UILabel）都是控件。

控件都有一些共同的属性：
- 尺寸，
- 位置，
- 背景色
......

 
###UIView的常见属性
```objc
@property(nonatomic,readonly) UIView *superview;
 获得自己的父控件对象
  // 1.通过UIView的superview属性,拿到自己的父控件 
     NSLog(@"%@", self.button.superview);   
```
```objc
@property(nonatomic,readonly,copy) NSArray *subviews;
 获得自己的所有子控件对象
 
 // 2.通过UIView的subviews的属性可以获取到该控件中的所有子控件
    // (数组中只会存放UIView的直接子控件)
    // 数组中子控件的顺序是和控件加入顺序有关的
    // 2.1.获取子控件的个数
     NSLog(@"%ld", self.redView.subviews.count);
    // 2.2.打印数据中具体的子控件
     NSLog(@"%@", self.redView.subviews);
    // 2.3.打印控制器View的所有子控件
    NSLog(@"%ld", self.view.subviews.count);
    _UILayoutGuide : 和autolayout有关(目前还没有学习)
    NSLog(@"%@", self.view.subviews);
```
```objc
@property(nonatomic) CGAffineTransform transform;
 控件的形变属性(可以设置旋转角度、比例缩放、平移等属性)
```
```objc
@property(nonatomic) NSInteger tag;
 控件的ID(标识)，父控件可以通过tag来找到对应的子控件
 


```

###UIView的常见方法

- 添加一个子控件view

```objc
- (void)addSubview:(UIView *)view; 
 
    // 1.addSubView演示 
    // 1.创建Label
    UILabel *label = [[UILabel alloc] init]; 
    // 2.一个控件要显示必须有属于自己的位置和尺寸
    label.frame = CGRectMake(100, 100, 200, 50); 
    // 3.设置背景颜色
    label.backgroundColor = [UIColor redColor]; 
    // 4.设置Label文字
    label.text = @"我是一个Label"; 
    // 添加子控件
    [self.view addSubview:label];
     
```
- 将自己从父控件中移除


```objc
- (void)removeFromSuperview;
  
 // 将控件本身从父控件中移除掉
 [label removeFromSuperview];
 [self.btn removeFromSuperview];
```

- 根据一个tag标识找出对应的控件


```objc
- (UIView *)viewWithTag:(NSInteger)tag;
 根据一个tag标识找出对应的控件（一般都是子控件）
 
  // 2.viewWithTag: 可以为控件绑定一个tag,之后通过tag来查找到对应的控件
    // 不常使用tag来取出控件
    // 1.tag取出控件的效率较低
    // 2.tag较多时容易搞混乱
    UIView *view = [self.view viewWithTag:100]; 
    
    UIButton *btn = (UIButton *)[self.view viewWithTag:1000];  
```

- 通过控件的tag值做动作

```objc
    - (IBAction)btnClick:(UIButton *)sender {
    /*
    if (sender == self.btn1) {
        NSLog(@"按钮1发生了点击");
    } else if (sender == self.btn2) {
        NSLog(@"按钮2发生了点击");
    } else if (sender == self.btn3) {
        NSLog(@"按钮3发生了点击");
    }
     */
    switch (sender.tag) {
        case 1:
            NSLog(@"按钮1发生了点击");
            break;
        case 2:
            NSLog(@"按钮2发生了点击");
            break;
        case 3:
            NSLog(@"按钮3发生了点击");
            break;
            
        default:
            break;
    }
}

```

###UIView的常见属性

- 控件矩形框在父控件中的位置和尺寸


```objc
@property(nonatomic) CGRect frame;
 控件矩形框在父控件中的位置和尺寸(以父控件的左上角为坐标原点)
 
     // 2.设置frame(位置和尺寸)
    btn.frame = CGRectMake(100, 100, 175, 50);
```

- 控件矩形框的位置和尺寸
 
```objc
@property(nonatomic) CGRect bounds;
 控件矩形框的位置和尺寸(以自己左上角为坐标原点，所以bounds的x、y一般为0)
 
    // 如果只修改尺寸,可以通过另外一个属性来修改:bounds
    // bounds的x/y传入0/0即可
    // 注意:中心点不变,改变尺寸
     btn.bounds = CGRectMake(0, 0, 300, 175);
```

- 控件中点的位置(以父控件的左上角为坐标原点)
 
```objc
@property(nonatomic) CGPoint center; 
   // 如果只修改位置,可以通过另外一个属性来修改:center
   btn.center = CGPointMake(100, 100);
```

- 代码案例

```objc
#import "ViewController.h" 
@interface ViewController () 
@property (nonatomic, weak) UIButton *btn; 
@end 
@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    
    // 1.创建UIButton对象
    UIButton *btn = [[UIButton alloc] init];
    
    // 2.设置frame(位置和尺寸)
    btn.frame = CGRectMake(100, 100, 175, 50);
    
    // 3.设置背景颜色
    btn.backgroundColor = [UIColor yellowColor]; 
    
    // 如果只修改尺寸,可以通过另外一个属性来修改:bounds
    // bounds的x/y传入0/0即可
    // 注意:中心点不变,改变尺寸
    btn.bounds = CGRectMake(0, 0, 300, 175);
    
    // 如果只修改位置,可以通过另外一个属性来修改:center
    btn.center = CGPointMake(100, 100);
    
    // 添加控制器的View
    [self.view addSubview:btn];
    
    self.btn = btn;
}

- (IBAction)btnClick {
    // OC语法:对象的结构体属性是不允许直接修改内部的变量
    // self.btn.frame.origin.x = 20;
    /*
    CGRect frame = self.btn.frame;
    frame.origin.x = 20;
    self.btn.frame = frame;
     */
    // self.btn.frame.size.height = 175;
    CGRect frame = self.btn.frame;
    frame.size.height = 175;
    self.btn.frame = frame;
}
```
###UIKit坐标系


```objc
在UIKit中，坐标系的原点(0，0)在左上角，x值向右正向延伸，y值向下正向延伸

```
![image](UIkit坐标系.png)
