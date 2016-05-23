#Chapter-01 UIKit框架下的各类控件和知识点
---
```objc
Update更新：2016年5月23日 By {MISSAJJ琴瑟静听}
 
```

###UIKit框架知识点
![image](UIKIT.png)

###所有控件的父类:UIView
UIView可以称之为控件/视图，屏幕上所有能看到的东西都是UIView，按钮（UIButton）、文本（UILabel）都是控件

控件都有一些共同的属性：尺寸，位置，背景色
......

所有控件的父类都是UIView，在开发中我们会将有共同属性的类抽取出一个父类（猫/狗/蟑螂抽出一个动物类）。苹果将拥有共同属性的控件类抽取出了UIView类，所有的控件都是继承自UIView类，UIButton/UILabel都继承自UIView(查看头文件)

###父控件、子控件
每一个控件其实都是一个容器，可以将其他控件放到该控件的内部，比较常见的还是将UIView作为容器。

- 可以将A控件放入B控件
- A控件是B控件的子控件
- B控件是A控件的父控件

每一个控制器都有一个UIView，控制器本身是不可见，能够看到的是控制器的View，每一个控制器中都一个UIVIew的属性，控制器中管理的所有子控件都是该控件的子控件。


###UIView的常见属性
```objc
@property(nonatomic,readonly) UIView *superview;
 获得自己的父控件对象

@property(nonatomic,readonly,copy) NSArray *subviews;
 获得自己的所有子控件对象
 
@property(nonatomic) CGAffineTransform transform;
 控件的形变属性(可以设置旋转角度、比例缩放、平移等属性)

@property(nonatomic) NSInteger tag;
 控件的ID(标识)，父控件可以通过tag来找到对应的子控件
```

###UIView的常见方法
```objc
- (void)addSubview:(UIView *)view;
 添加一个子控件view

- (void)removeFromSuperview;
 将自己从父控件中移除

- (UIView *)viewWithTag:(NSInteger)tag;
 根据一个tag标识找出对应的控件（一般都是子控件）
```

###UIView的常见属性
```objc
@property(nonatomic) CGRect frame;
 控件矩形框在父控件中的位置和尺寸(以父控件的左上角为坐标原点)

@property(nonatomic) CGRect bounds;
 控件矩形框的位置和尺寸(以自己左上角为坐标原点，所以bounds的x、y一般为0)

@property(nonatomic) CGPoint center;
 控件中点的位置(以父控件的左上角为坐标原点)
 
```
###UIKit坐标系


```objc
在UIKit中，坐标系的原点(0，0)在左上角，x值向右正向延伸，y值向下正向延伸

```
![image](UIkit坐标系.png)