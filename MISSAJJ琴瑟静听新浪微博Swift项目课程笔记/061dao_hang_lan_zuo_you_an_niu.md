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
     封装UIBarButtonItem
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