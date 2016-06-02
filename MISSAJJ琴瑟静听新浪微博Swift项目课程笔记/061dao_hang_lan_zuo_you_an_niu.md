# 导航栏左右按钮

---
```objc
Update更新：2016年6月2日 By {MISSAJJ琴瑟静听} 
```


##无 Highlighted 图片的导航栏按钮

```Swift
navigationItem.leftBarButtonItem = UIBarButtonItem(image: UIImage(named: "navigationbar_friendattention"), style: UIBarButtonItemStyle.Plain, target: self, action: #selector(HomeTableViewController.leftBtnClick))

```

##有 Highlighted 图片的导航栏按钮(customView)

```Swift
 let leftBtn = UIButton()
 leftBtn.setImage(UIImage(named: "navigationbar_friendattention"), forState: UIControlState.Normal)
 leftBtn.setImage(UIImage(named: "navigationbar_friendattention_highlighted"), forState: UIControlState.Highlighted)
 leftBtn.sizeToFit()
 navigationItem.leftBarButtonItem = UIBarButtonItem(customView: leftBtn)
        
 let rigthBtn = UIButton()
 rigthBtn.setImage(UIImage(named: "navigationbar_pop"), forState: UIControlState.Normal)
 rigthBtn.setImage(UIImage(named: "navigationbar_pop_highlighted"), forState: UIControlState.Highlighted)
 rigthBtn.sizeToFit()
 navigationItem.rightBarButtonItem = UIBarButtonItem(customView: rigthBtn)
```

##导航栏按钮的封装

```Swift
 navigationItem.leftBarButtonItem = createBarButtonItem("navigationbar_friendattention")
 navigationItem.rightBarButtonItem = createBarButtonItem("navigationbar_pop")


    /**
     封装UIBarButtonItem: 在HomeTableViewController内增加一个封装方法
     - parameter imageName: 图片名字符串
     - returns: 导航栏按钮
     */
    private func createBarButtonItem(imageName: String) -> UIBarButtonItem
    {
        let btn = UIButton()
        btn.setImage(UIImage(named: imageName), forState: UIControlState.Normal)
        btn.setImage(UIImage(named: imageName + "_highlighted"), forState: UIControlState.Highlighted)
        btn.sizeToFit()
        return UIBarButtonItem(customView: btn)
    }
```

##进阶:导航栏按钮的分类封装
- 创建一个Tools分类, 继承UIBarButtonItem

```Swift
//  UIBarButtonItem+Extension.swift
//  MISSAJJ
//
//  Created by MISSAJJ on 16/6/2.
//  Copyright © 2015年 MISSAJJ. All rights reserved.
//

import UIKit

extension UIBarButtonItem
{
    // 1.用于快速创建一个对象: 传入图片名字符串,目标,点击事件
    // 2.依赖于指定构造方法self.init
    convenience init(imageName: String, target: AnyObject?, action: Selector)
    {
        let btn = UIButton()
        btn.setImage(UIImage(named: imageName), forState: UIControlState.Normal)
        btn.setImage(UIImage(named: imageName + "_highlighted"), forState: UIControlState.Highlighted)
        btn.sizeToFit()
        btn.addTarget(target, action: action, forControlEvents: UIControlEvents.TouchUpInside)
        
        self.init(customView: btn)
    }
}


```

- HomeTableViewController调用

```Swift
 navigationItem.leftBarButtonItem = UIBarButtonItem(imageName: "navigationbar_friendattention", target: self, action: Selector("leftBtnClick"))
 navigationItem.rightBarButtonItem = UIBarButtonItem(imageName: "navigationbar_pop", target: self, action: Selector("rightBtnClick"))
```

```Swift

    // MARK: - 内部控制方法
    @objc private func leftBtnClick()
    {
        MALog("")
    }
    @objc private func rightBtnClick()
    {
        MALog("")
    }



```


