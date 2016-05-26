# 添加子控制器
---
```objc
Update更新：2016年5月26日 By {MISSAJJ琴瑟静听} 
```
## 功能需求

* 由于采用了多视图控制器的设计方式，因此需要通过代码的方式向主控制器中添加子控制器

## 文件准备

* 将素材文件夹中的 `TabBar` 拖拽到 `Images.xcassets` 目录下

## 代码实现

### 添加第一个视图控制器

```swift
override func viewDidLoad() {
    super.viewDidLoad()

    addChildViewController()
}

///  添加子控制器
private func addChildViewController() {

  // iOS7以后只需要设置tintColor, 那么图片和文字都会按照tintColor渲染
    tabBar.tintColor = UIColor.orangeColor()
    // 注意: Swift开发中定义变量时候先用let(常量), 只有真正需要修改时才用var
    let vc = HomeTableViewController()
    vc.title = "首页"
    vc.tabBarItem.image = UIImage(named: "tabbar_home")
    vc.tabBarItem.selectedImage = UIImage(named: "tabbar_home_highlighted")
    let nav = UINavigationController(rootViewController: vc)

    addChildViewController(nav)
}
```

### 修改图片的渲染模式

  - 代码方式

```Swift 
//取消图片被系统自动渲染,设置原始状态代码UIImage.init(named: imageName)?.imageWithRenderingMode(.AlwaysOriginal)
 
childController.tabBarItem.image = UIImage.init(named: imageName)?.imageWithRenderingMode(.AlwaysOriginal)
 
childController.tabBarItem.selectedImage = UIImage.init(named: imageName + "_highlighted")?.imageWithRenderingMode(.AlwaysOriginal)


```
  - Swift缩写方式


```Swift

//完整写法
var image : UIImage? = UIImage(named:"myImage.png").imageWithRenderingMode(UIImageRenderingMode.AlwaysOriginal)

//缩写方式
var image : UIImage? = UIImage(named:"myImage.png").imageWithRenderingMode(.AlwaysOriginal)
```
  - Assets.xcassets 设置方式

![](./images/CreateProject/修改图片渲染模式.png)


###创建多个子控制器的完整代码
```Swift
//
//  MainViewController.swift
//  MASINAWEIBO
//
//  Created by MISSAJJ on 16/5/26.
//  Copyright © 2016年 MISSAJJ. All rights reserved.
//

import UIKit

class MainViewController: UITabBarController {

    override func viewDidLoad() {
        super.viewDidLoad()

        // iOS7以后只需要设置tintColor, 那么图片和文字都会按照tintColor渲染 : 如果使用了使用原始图片,那就不需要设置 tintcolor
        //tabBar.tintColor = UIColor.orangeColor()
        
        //添加所有子控制器
        addChildViewControllers()
    }
    
    //MARK: - 添加所有子控制器
    func addChildViewControllers()
    {
     
         addChildViewController(HomeTableViewController(), title: "首页", imageName: "tabbar_home")
         addChildViewController(MessageTableViewController(), title: "消息", imageName: "tabbar_message_center")
         addChildViewController(DiscoverTableViewController(), title: "发现", imageName: "tabbar_discover")
         addChildViewController(ProfileTableViewController(), title: "我", imageName: "tabbar_profile")
       
    }
    
    //MARK: - 添加一个子控制器
   func addChildViewController(childController: UIViewController, title: String, imageName: String) {
    
    
        //添加子控制器
 //取消图片被系统自动渲染,设置原始状态代码UIImage.init(named: imageName)?.imageWithRenderingMode(.AlwaysOriginal)
 
//childController.tabBarItem.image = UIImage.init(named: imageName)
 childController.tabBarItem.image = UIImage.init(named: imageName)?.imageWithRenderingMode(.AlwaysOriginal)
 childController.tabBarItem.selectedImage = UIImage.init(named: imageName + "_highlighted")?.imageWithRenderingMode(.AlwaysOriginal)
    
        //设置自控制器的导航栏标题
        childController.title = title
    
        //包装导航控制器
        let nav = UINavigationController.init(rootViewController: childController)
    
        //将子控制器添加到UITabBarController中
        addChildViewController(nav)

        
        
    }
}

```

###项目模拟器截图

![image](images/CreateProject/Simulator Screen Shot 2016年5月26日 21.40.38.png)


