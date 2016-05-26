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


#Name Space 命名空间
---

 
###Swift中新增了一个叫做命名空间的概念
- 作用: 避免重复
- 不用项目中的命名空间是不一样的, 默认情况下命名空间的名称就是当前项目的名称
- 正是因为Swift可以通过命名空间来解决重名的问题, 所以在做Swift开发时尽量使用cocoapods来集成三方框架, 这样可以有效的避免类名重复
- 正是因为Swift中有命名空间, 所以通过一个字符串来创建一个类和OC中也不太一样了, OC中可以直接通过类名创建一个类, 而Swift中如果想通过类名来创建一个类必须加上命名空间


###通过命名空间可以实现什么效果?
- 命名空间可运用于更换底部菜单图片及跳转 VC
- 比如:淘宝APP 搞活动的时候,用户没有更新 APP, 却在打开 APP 发现底部菜单的图片和跳转界面全部被换掉了
- 方案:为了防止应用审核期的等待,在开发的时候早已经将未来要做的活动做好了对应的一套菜单及 VC,通过网络传 json 给 APP 端, 如果 json 没有数据就显示默认的一套菜单, 到做活动的时候,服务器传的 json 数据有值了,APP端就会显示另外一套预先准备的菜单


![image](images/CreateProject/命名空间运用于更换菜单图片.png)
### 用代码动态创建类

* 跟踪 `类` 名称

```swift
print(vc)
```

* 将info.plist 的 key 值,显示真实 key 值的方法


![image](images/CreateProject/infolist 显示真实 key.png)

* 显示真实 Key 值的截图

![image](images/CreateProject/命名空间 key.png)

* namespace 对应的是 `CFBundleExecutable`
    * 在 swift 中，类名的组成格式是 `namespace.类名`
    * namespace 对应的是 `CFBundleExecutable`

* 从 `mainBundle` 的 `infoDictionary` 获取命名空间名称

```swift
print(NSBundle.mainBundle().infoDictionary)

let namespace = NSBundle.mainBundle().infoDictionary!["CFBundleExecutable"] as! String
```

##Swift命名空间代码案例:
---
###Swift通过guard来避免重复嵌套判定 
```swift
guard 条件表达式 else {
//            需要执行的语句
//            只有条件为假才会执行{}中的内容
            return
        }
        guard可以有效的解决可选绑定容易形成{}嵌套问题
        
```

###代码案例
```Swift
/// 添加一个子控制器
//    func addChildViewController(childController: UIViewController, title: String, imageName: String) {
    
    func addChildViewController(childControllerName: String, title: String, imageName: String) {
    
        /*
        guard 条件表达式 else {
//            需要执行的语句
//            只有条件为假才会执行{}中的内容
            return
        }
        guard可以有效的解决可选绑定容易形成{}嵌套问题
        */
        
        // 1.动态获取命名空间
        // 由于字典/数组中只能存储对象, 所以通过一个key从字典中获取值取出来是一个AnyObject类型, 并且如果key写错或者没有对应的值, 那么就取不到值, 所以返回值可能有值也可能没值, 所以最终的类型是AnyObject?
        guard let name =  NSBundle.mainBundle().infoDictionary!["CFBundleExecutable"] as? String else
        {
            NJLog("获取命名空间失败")
            return
        }
        
        /*
        Swift中新增了一个叫做命名空间的概念
        作用: 避免重复
        不用项目中的命名空间是不一样的, 默认情况下命名空间的名称就是当前项目的名称
        正是因为Swift可以通过命名空间来解决重名的问题, 所以在做Swift开发时尽量使用cocoapods来集成三方框架, 这样可以有效的避免类名重复
        正是因为Swift中有命名空间, 所以通过一个字符串来创建一个类和OC中也不太一样了, OC中可以直接通过类名创建一个类, 而Swift中如果想通过类名来创建一个类必须加上命名空间
        */
        let cls: AnyClass? = NSClassFromString(name + "." + childControllerName)
        
         // Swift中如果想通过一个Class来创建一个对象, 必须告诉系统这个Class的确切类型
        guard let typeCls = cls as? UITableViewController.Type else
        {
            NJLog("cls不能当做UITableViewController")
            return
        }
        
        // 通过Class创建对象
        let childController = typeCls.init()
        NJLog(childController)
        
        /*
        // 1.2设置自控制的相关属性
        childController.title = title
        childController.tabBarItem.image = UIImage(named: imageName)
        childController.tabBarItem.selectedImage = UIImage(named: imageName + "_highlighted")
        
        
        // 1.3包装一个导航控制器
        let nav = UINavigationController(rootViewController: childController)
        // 1.4将子控制器添加到UITabBarController中
        addChildViewController(nav)
        */
    }

```        
* Tips
    + 在 swift 中，类名是包含命名空间的
    + 命名空间默认是项目名称，同一个命名空间全局共享
    + 如果新建项目时，项目名称中包含有中文，可以按照下图修改
    + 
![image](images/CreateProject/命名空间.png)

* 调整创建控制器代码，根据类名动态创建类

```swift
let namespace = NSBundle.mainBundle().infoDictionary!["CFBundleExecutable"] as! String
let clsName = namespace + "." + vcName
let cls: AnyClass = NSClassFromString(clsName)!
let vc = cls.alloc() as! UITableViewController
```

* 扩展方法参数，增加`标题`和`图像名`参数

```swift
///  添加子控制器
///
///  :param: vcName    视图控制器名称
///  :param: title     标题
///  :param: imageName 图像名
private func addChildViewController(vcName: String, title: String, imageName: String) {
    tabBar.tintColor = UIColor.orangeColor()

    let namespace = NSBundle.mainBundle().infoDictionary!["CFBundleExecutable"] as! String
    let clsName = namespace + "." + vcName
    // 告诉编译器暂时就是AnyClass
    let cls: AnyClass!  = NSClassFromString(ns + "." + vcName)
    // 告诉编译器真实类型是UIViewController
    let vcCls = cls as! UIViewController.Type
    // 实例化控制器
    let vc = vcCls.init()


    vc.title = title
    vc.tabBarItem.image = UIImage(named: imageName)
    vc.tabBarItem.selectedImage = UIImage(named: imageName + "_highlighted")
    let nav = UINavigationController(rootViewController: vc)

    addChildViewController(nav)
}
```

* 扩充调用函数，添加其他控制器

```swift
///  添加所有子控制器
private func addChildViewControllers() {
    addChildViewController("HomeTableViewController", title: "首页", imageName: "tabbar_home")
    addChildViewController("MessageTableViewController", title: "消息", imageName: "tabbar_message_center")
    addChildViewController("DiscoverTableViewController", title: "发现", imageName: "tabbar_discover")
    addChildViewController("ProfileTableViewController", title: "我", imageName: "tabbar_profile")
}
```

