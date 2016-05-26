# 创建项目文件
---
```objc
Update更新：2016年5月26日 By {MISSAJJ琴瑟静听} 
```
## 准备工作

### 删除模板文件
此新浪微博 Swift 项目前期以全代码形式书写，后期会陆续改为StoryBoard形式的写法

* ViewController.swift
* Main.storyboard
* LaunchScreen.xib



### 创建项目结构

#### 主目录 `Classes`

#### 二级目录
 

 目录名 | 说明 
 ----- | -----
 Main | 主要 
 Home | 首页 
 Message | 消息 
 Discover | 发现 
 Profile | 我 
 Tools | 工具类 

## 创建项目文件

### Main

| 目录 | Controller |
| ------ | ------ |
| Main | MainViewController.swift(`:UITabBarController`) |


![image](images/CreateProject/目录结构1.png)


### 功能模块

| 目录 | Controller |
| ------ | ------ |
| Home | HomeTableViewController.swift |
| Message | MessageTableViewController.swift |
| Discover | DiscoverTableViewController.swift |
| Profile | ProfileTableViewController.swift |

#### 细节

* 每个 ViewController 继承自 `UITableViewController`
* 搭建完成的文件结构图如下：

![image](images/CreateProject/目录结构2.png)

 

* 修改 `AppDelegate` 中的 `didFinishLaunchingWithOptions` 函数，设置启动控制器

```swift     

     window = UIWindow(frame: UIScreen.mainScreen().bounds)
        window?.backgroundColor = UIColor.whiteColor()
        window?.rootViewController = MainViewController() //改为继承与UITabBarController的MainViewController
        
        window?.makeKeyAndVisible()
 
```
* 模拟器启动项目截图

![image](images/CreateProject/创建工程-模拟器.png)

