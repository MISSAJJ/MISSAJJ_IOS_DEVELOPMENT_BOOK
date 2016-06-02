# 导航栏下拉菜单
---
```objc
Update更新：2016年6月2日 By {MISSAJJ琴瑟静听} 
```

##需求截图

![image](images/下拉菜单标题按钮.png)

##自定义button
- 创建继承与UIButton的分类TitleButton.swift

```Swift
 // MARK: - 内部控制方法
    private func setupNav()
    {  
        // 添加标题按钮
        let titleButton = TitleButton()
        titleButton.setTitle("MISSAJJ", forState: UIControlState.Normal)
        titleButton.addTarget(self, action: Selector("titleBtnClick:"), forControlEvents: UIControlEvents.TouchUpInside)
        navigationItem.titleView = titleButton
    }
     
    @objc private func titleBtnClick(btn: TitleButton)
    {
        btn.selected = !btn.selected
    }
```
- 重写UIButton的集中不同方式

  - 1.重写RectForContentRect,将按钮的文字标题和图片交换过来

```swift
    override func titleRectForContentRect(contentRect: CGRect) -> CGRect {
        return CGRectZero
    }
    override func imageRectForContentRect(contentRect: CGRect) -> CGRect {
        return CGRectZero
    }
```
  - 2.通过layoutSubviews重新布局

```swift
   override func layoutSubviews() {
        super.layoutSubviews()
        
        /*  
        // offsetInPlace 方法用于设置控件的偏移位
        //注意:layoutSubviews会被调用两次,所以用了非主流方法width * 0.5(一半)
        titleLabel?.frame.offsetInPlace(dx: -imageView!.frame.width * 0.5, dy: 0)
        imageView?.frame.offsetInPlace(dx: titleLabel!.frame.width * 0.5, dy: 0)
        */
        
        // 和OC不太一样, Swift语法允许我们直接修改一个对象的结构体属性的成员
        titleLabel?.frame.origin.x = 0 //标题X设置为0
        imageView?.frame.origin.x = titleLabel!.frame.width //图片的X为标题的宽
    }

```

##完整代码

```Swift
//  TitleButton.swift
//  MISSAJJ
//
//  Created by MISSAJJ on 16/6/2.
//  Copyright © 2016年 MISSAJJ. All rights reserved.
//

import UIKit

class TitleButton: UIButton {
    
    // 通过纯代码创建时调用
    // 在Swift中如果重写父类的方法, 必须在方法前面加上override
    override init(frame: CGRect) {
        super.init(frame: frame)
        
       setupUI()
    }

    // 通过XIB/SB创建时调用
    required init?(coder aDecoder: NSCoder) {
        // 系统对initWithCoder的默认实现是报一个致命错误
//        fatalError("init(coder:) has not been implemented")
        super.init(coder: aDecoder)
        
        setupUI()
    }
    
    private func setupUI()
    {
        setImage(UIImage(named: "navigationbar_arrow_down"), forState: UIControlState.Normal)
        setImage(UIImage(named: "navigationbar_arrow_up"), forState: UIControlState.Selected)
        
        setTitleColor(UIColor.darkGrayColor(), forState: UIControlState.Normal)
        sizeToFit()
    }
    
    override func setTitle(title: String?, forState state: UIControlState) {
        //此处title为?可选类型, 而加的"  "空格是不可选类型,不同的类型不能结合,所以用(title ?? "")的方法解决为可选类型
        //?? 用于判断前面的参数是否是nil, 如果是nil就返回??后面的数据, 如果不是nil那么??后面的语句不执行
        super.setTitle((title ?? "") + "  ", forState: state) //这里多加了空格字符串, 用于标题和箭头按钮之间的距离
    }
    
    override func layoutSubviews() {
        super.layoutSubviews()
        
        /*
        // offsetInPlace 方法用于设置控件的偏移位
        titleLabel?.frame.offsetInPlace(dx: -imageView!.frame.width * 0.5, dy: 0)
        imageView?.frame.offsetInPlace(dx: titleLabel!.frame.width * 0.5, dy: 0)
        */
        
        // 和OC不太一样, Swift语法允许我们直接修改一个对象的结构体属性的成员
        titleLabel?.frame.origin.x = 0
        imageView?.frame.origin.x = titleLabel!.frame.width
    }
}


```

