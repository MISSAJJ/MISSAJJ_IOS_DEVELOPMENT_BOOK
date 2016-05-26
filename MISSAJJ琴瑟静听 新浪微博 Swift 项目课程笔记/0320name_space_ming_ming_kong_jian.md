#Name Space 命名空间
---

 
###Swift中新增了一个叫做命名空间的概念
- 作用: 避免重复
- 不用项目中的命名空间是不一样的, 默认情况下命名空间的名称就是当前项目的名称
- 正是因为Swift可以通过命名空间来解决重名的问题, 所以在做Swift开发时尽量使用cocoapods来集成三方框架, 这样可以有效的避免类名重复
- 正是因为Swift中有命名空间, 所以通过一个字符串来创建一个类和OC中也不太一样了, OC中可以直接通过类名创建一个类, 而Swift中如果想通过类名来创建一个类必须加上命名空间



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

###通过命名空间可以实现什么效果?
- 命名空间可运用于更换底部菜单图片及跳转 VC
- 比如:淘宝APP 搞活动的时候,用户没有更新 APP, 却在打开 APP 发现底部菜单的图片和跳转界面全部被换掉了
- 方案:为了防止应用审核期的等待,在开发的时候早已经将未来要做的活动做好了对应的一套菜单及 VC,通过网络传 json数据 给 APP 端, 如果 json 没有数据就显示默认的一套菜单, 到做活动的时候,服务器传的 json 数据有值了,APP端就会显示另外一套预先准备的菜单


#####json 数据示例:
```
 [
  {
  "vcName": "HomeTableViewController",
  "title": "首页123",
  "imageName": "tabbar_home"
  },
  {
  "vcName": "MessageTableViewController",
  "title": "消息123",
  "imageName": "tabbar_message_center"
  },
  {
  "vcName": "DiscoverTableViewController",
  "title": "广场123",
  "imageName": "tabbar_discover"
  },
  {
  "vcName": "ProfileTableViewController",
  "title": "我123",
  "imageName": "tabbar_profile"
  }
  ]
```
#####方案示意图

![image](images/CreateProject/命名空间运用于更换菜单图片.png)

####通过 json 数据通过命名空间动态创建控制器的代码案例


```Swift
import UIKit

class MainViewController: UITabBarController {

    override func viewDidLoad() {
        super.viewDidLoad()

        // iOS7以后只需要设置tintColor, 那么图片和文字都会按照tintColor渲染
        tabBar.tintColor = UIColor.orangeColor()
        
        // 添加子控制器
        addChildViewControllers()
    }
    
    /// 添加所有子控制器
    func addChildViewControllers()
    {
        
        // 1.根据JSON文件创建控制器
        // 1.1读取JSON数据
        guard let filePath =  NSBundle.mainBundle().pathForResource("MainVCSettings.json", ofType: nil) else
        {
            NJLog("JSON文件不存在")
            return
        }
        
        guard let data = NSData(contentsOfFile: filePath) else
        {
            NJLog("加载二进制数据失败")
            return
        }
        
        // 1.2将JSON数据转换为对象(数组字典)
        do
        {
            /*
             Swift和OC不太一样, OC中一般情况如果发生错误会给传入的指针赋值, 而在Swift中使用的是异常处理机制
             1.以后但凡看大 throws的方法, 那么就必须进行 try处理, 而只要看到try, 就需要写上do catch
             2.do{}catch{}, 只有do中的代码发生了错误, 才会执行catch{}中的代码
             3. try  正常处理异常, 也就是通过do catch来处理
                try! 告诉系统一定不会有异常, 也就是说可以不通过 do catch来处理
                     但是需要注意, 开发中不推荐这样写, 一旦发生异常程序就会崩溃
                     如果没有异常那么会返回一个确定的值给我们
        
                try? 告诉系统可能有错也可能没错, 如果没有系统会自动将结果包装成一个可选类型给我们, 如果有错系统会返回nil, 如果使用try? 那么可以不通过do catch来处理
            */
            
            let objc = try NSJSONSerialization.JSONObjectWithData(data, options: NSJSONReadingOptions.MutableContainers) as! [[String: AnyObject]]
            
            // 1.3遍历数组字典取出每一个字典
            for dict in objc
            {
                // 1.4根据遍历到的字典创建控制器
                let title = dict["title"] as? String
                let vcName = dict["vcName"] as? String
                let imageName = dict["imageName"] as? String
                addChildViewController(vcName, title: title, imageName: imageName)
            }
        }catch
        {
            // 只要try对应的方法发生了异常, 就会执行catch{}中的代码
            addChildViewController("HomeTableViewController", title: "首页", imageName: "tabbar_home")
            addChildViewController("MessageTableViewController", title: "消息", imageName: "tabbar_message_center")
            addChildViewController("DiscoverTableViewController", title: "发现", imageName: "tabbar_discover")
            addChildViewController("ProfileTableViewController", title: "我", imageName: "tabbar_profile")
        }
    }

    /// 添加一个子控制器
    func addChildViewController(childControllerName: String?, title: String?, imageName: String?) {

        
        // 1.动态获取命名空间
        guard let name =  NSBundle.mainBundle().infoDictionary!["CFBundleExecutable"] as? String else
        {
            NJLog("获取命名空间失败")
            return
        }
        
        // 2.根据字符串获取Class
        var cls: AnyClass? = nil
        if let vcName = childControllerName
        {
            cls = NSClassFromString(name + "." + vcName)
        }
        
        // 3.根据Class创建对象
        // Swift中如果想通过一个Class来创建一个对象, 必须告诉系统这个Class的确切类型
        guard let typeCls = cls as? UITableViewController.Type else
        {
            NJLog("cls不能当做UITableViewController")
            return
        }
        // 通过Class创建对象
        let childController = typeCls.init()
        
        
        // 1.2设置自控制的相关属性
        childController.title = title
        if let ivName = imageName
        {
            childController.tabBarItem.image = UIImage(named: ivName)
            childController.tabBarItem.selectedImage = UIImage(named: ivName + "_highlighted")
        }
        
        // 1.3包装一个导航控制器
        let nav = UINavigationController(rootViewController: childController)
        // 1.4将子控制器添加到UITabBarController中
        addChildViewController(nav)
        
    }
}
```