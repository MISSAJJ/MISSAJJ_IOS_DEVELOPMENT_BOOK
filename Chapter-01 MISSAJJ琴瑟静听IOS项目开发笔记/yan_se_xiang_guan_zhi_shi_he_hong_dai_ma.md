# 颜色相关知识和宏代码

 
---
```objc
Update更新：2016年5月9日 By {MISSAJJ琴瑟静听}
```
## 颜色相关的一些知识
- 颜色的基本组成
    - 一种颜色由N个颜色通道组成
- 颜色通道
    - 1个颜色通道占据8bit
    - 1个颜色通道的取值范围
        - 10进制 : [0, 255]
        - 16进制 : [00, ff];
    - 常见的颜色通道
        - 红色 red R
        - 绿色 green G
        - 蓝色 blue B
        - 透明度 alpha A
    -  R\G\B一样的是灰色
- 颜色的种类
    - 24bit颜色
        - 由R\G\B组成的颜色
        - 常见的表示形式
            - 10进制(`仅仅是用在CSS`)
                - 红色 : rgb(255,0,0)
                - 绿色 : rgb(0,255,0)
                - 蓝色 : rgb(0,0,255)
                - 黄色 : rgb(255,255,0)
                - 黑色 : rgb(0,0,0)
                - 白色 : rgb(255,255,255)
                - 灰色 : rgb(80,80,80)
            - 16进制(`可以用在CSS\android`)
                - 红色 : #ff0000  #f00
                - 绿色 : #00ff00  #0f0
                - 蓝色 : #0000ff  #00f
                - 黄色 : #ffff00  #ff0
                - 黑色 : #000000  #000
                - 白色 : #ffffff  #fff
                - 灰色 : #979797
    - 32bit颜色
        - 由R\G\B\A组成的颜色
        - 常见的表示形式
            - 10进制(`仅仅是用在CSS`)
                - 红色 : rgba(255,0,0,255)
                - 绿色 : rgba(0,255,0,255)
                - 蓝色 : rgba(0,0,255,255)
                - 黄色 : rgba(255,255,0,255)
                - 黑色 : rgba(0,0,0,255)
                - 白色 : rgba(255,255,255,255)
            - 16进制(#AARRGGBB,  `仅仅是用在android`)
                - 红色 : #ffff0000
                - 绿色 : #ff00ff00
                - 蓝色 : #ff0000ff
                - 黄色 : #ffffff00
                - 黑色 : #ff000000
                - 白色 : #ffffffff


## 有透明度的颜色
```objc
[UIColor colorWithRed:1.0 green:1.0 blue:1.0 alpha:0.2];
[UIColor colorWithWhite:1.0 alpha:0.2];
[[UIColor whiteColor] colorWithAlphaComponent:0.2];
```

##和颜色相关的宏代码

```objc
/*** 颜色 ***/
//有alpha的颜色
#define MAColorA(r, g, b, a) [UIColor colorWithRed:(r)/255.0 green:(g)/255.0 blue:(b)/255.0 alpha:(a)/255.0]
//无alpha的颜色
#define MAColor(r, g, b) MAColorA((r), (g), (b), 255)
//随机色
#define MARandomColor MAColor(arc4random_uniform(255), arc4random_uniform(255), arc4random_uniform(255))



//RGB Color macro
#define UIColorFromRGB(rgbValue) [UIColor \
colorWithRed:((float)((rgbValue & 0xFF0000) >> 16))/255.0 \
green:((float)((rgbValue & 0xFF00) >> 8))/255.0 \
blue:((float)(rgbValue & 0xFF))/255.0 alpha:1.0]

//RGB color macro with alpha
#define UIColorFromRGBWithAlpha(rgbValue,a) [UIColor \
colorWithRed:((float)((rgbValue & 0xFF0000) >> 16))/255.0 \
green:((float)((rgbValue & 0xFF00) >> 8))/255.0 \
blue:((float)(rgbValue & 0xFF))/255.0 alpha:a]

//不换行，release下会报错，所以用上边的红定义
//#define UIColorFromRGB(rgbValue) [UIColor colorWithRed:((float)((rgbValue & 0xFF0000) >> 16))/255.0 green:((float)((rgbValue & 0xFF00) >> 8))/255.0 blue:((float)(rgbValue & 0xFF))/255.0 alpha:1.0]
```