# 创建自定义Log
----
```objc
Update更新：2016年5月26日 By {MISSAJJ琴瑟静听}
  
```
在 OC 语言中有宏定义可以简单定义 Log, 但是在 Swift 语言内是没有宏定义的, 所以我们需要通过其他办法来自定义 Log, 下面介绍几种相关自定义 Log 的工具

##自定义 Log

```swift
import UIKit 
@UIApplicationMain
class AppDelegate: UIResponder, UIApplicationDelegate {

    var window: UIWindow?


    func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool {
        // 1.初始化LOG
        //自定义 Log
        MALog("test")
        
        // 2.初始化window
        window = UIWindow(frame: UIScreen.mainScreen().bounds)
        window?.backgroundColor = UIColor.whiteColor()
        
        window?.rootViewController = MainViewController()
        window?.makeKeyAndVisible()
        
        return true
    }
}

/*
自定义LOG的目的:
在开发阶段自动显示LOG
在发布阶段自动屏蔽LOG

print(__FUNCTION__)  // 打印所在的方法
print(__LINE__)     // 打印所在的行
print(__FILE__)     // 打印所在文件的路径

方法名称[行数]: 输出内容
*/
    func MALog<T>(message: T, method: String = #function, line: Int = #line)
    {
        #if DEBUG
            print("\(method)[\(line)]: \(message)")
        #endif
    }

```
##CocoaPods 安装日志工具 QorumLogs

![image](cocoaPods 安装.png)

- 1.编辑 Podfile: 如图内1所示,打开编辑 Podfile 文件,输入以下代码并保存关闭


```
platform :ios, '8.0'
use_frameworks!

pod 'QorumLogs'
```

- 2.点 Install Pods 安装,耐心等待直到安装完成提示重启项目,之后是在xcworkspace文件打开

```
安装完成后日志输出


/usr/local/bin/pod install

Updating local specs repositories

CocoaPods 1.0.0 is available.
To update use: `gem install cocoapods`
Until we reach version 1.0 the features of CocoaPods can and will change.
We strongly recommend that you use the latest version at all times.

For more information see http://blog.cocoapods.org
and the CHANGELOG for this version http://git.io/BaH8pQ.

Analyzing dependencies
Downloading dependencies
Using QorumLogs (0.7.2)
Generating Pods project
Integrating client project
Pod installation complete! There is 1 dependency from the Podfile and 1 total pod installed.



```

- 3.AppDelegate.swift导入头文件,并开启日志输出


```Swift
import UIKit
import QorumLogs //导入头文件

@UIApplicationMain
class AppDelegate: UIResponder, UIApplicationDelegate {

    var window: UIWindow?


    func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool {
        
             // 1.初始化LOG
             
         //开关日志输出
         QorumLogs.enabled = true // 启用QorumLogs
         QorumLogs.enabled = false //项目发布之前,关闭QorumLogs
         QorumLogs.test() // 测试QorumLogs
        
        // 设置需要显示的等级
         QorumLogs.minimumLogLevelShown = 4
        
        // 测试的时候，可以限定输出文件
        QorumLogs.onlyShowThisFile(MainViewController)
        
        QL1("mylog") // debug
        QL2("mylog")  // info
        QL3("mylog") // warning
        QL4("mylog") // error
        QLPlusLine() // 打印加号作为分割线 +++++++++++++ 

        QLShortLine() // 打印等号作为分割线 ==========
        
        return true
    }
    
```
- 4.输出的日志如图显示为英文,但还不是彩色的


![image](images/CreateProject/日志显示.png)

- 5.见证奇迹的时刻: 让日志变彩色,安装XcodeColors:

![image](images/CreateProject/彩色日志.png)


----

##日志插件工具下载链接
---
###日志工具QorumLogs

- 为 Xcode 和 Google Docs 设计的 Swift 日志工具。
  - 作者源代码：https://github.com/goktugyil/QorumLogs
![image](https://camo.githubusercontent.com/c3ee4351e7a0b12e3fa4f918571bdb5c317a22d6/687474703a2f2f692e696d6775722e636f6d2f3367504a4861592e676966)

![image](https://camo.githubusercontent.com/97070f770e55da7d42bae435cfc027b9ec84901a/687474703a2f2f692e696d6775722e636f6d2f537a78545879762e706e67)

![image](https://camo.githubusercontent.com/2fc12517298757e8e188e9265d7166d2bfac324e/687474703a2f2f692e696d6775722e636f6d2f4b374f577142772e676966)

####让你的日志变成彩色的 XcodeColors
- 很有趣的插件，可以试一下哦~
  - 作者源代码： https://github.com/robbiehanson/XcodeColors


![image](images/CreateProject/彩色日志.png)

