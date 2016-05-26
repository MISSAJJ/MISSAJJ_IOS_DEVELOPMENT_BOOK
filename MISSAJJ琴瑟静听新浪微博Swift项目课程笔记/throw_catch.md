# try throw 代码实践

> `throw catch` 是 `Xcode 7.0` 对错误处理的一个非常大的变化

* 范例代码

```swift
// 2. 反序列化
// 1.获取json文件路径
        let jsonPath = NSBundle.mainBundle().pathForResource("MainVCSettings.json", ofType: nil)
        // 2.加载json数据
        let jsonData = NSData(contentsOfFile: jsonPath!)
        // 3.序列化json
        do{
             // throw是Xcode7最明显的一个变化, Xcode7之前都是通过传入error指针捕获异常, Xocode7开始通过try/catch捕获异常
            let dictArray = try NSJSONSerialization.JSONObjectWithData(jsonData!, options: NSJSONReadingOptions.MutableContainers)

            // 遍历字典时候需要明确指明数组中的数据类型
            for dict in dictArray  as! [[String:String]]
            {
                 // 由于addChildVC方法参数不能为nil, 但是字典中取出来的值可能是nil, 所以需要加上!
                addChildViewController(dict["vcName"]!, title: dict["title"]!, imageName: dict["imageName"]!)
            }
        }catch{
            print(error)
            addChildViewController("HomeTableViewController", title: "首页", imageName: "tabbar_home")
            addChildViewController("MessageTableViewController", title: "消息", imageName: "tabbar_message_center")
            addChildViewController("DiscoverTableViewController", title: "发现", imageName: "tabbar_discover")
            addChildViewController("ProfileTableViewController", title: "我", imageName: "tabbar_profile")
        }
```

* 如果能确保代码执行正确，可以强行 try!

```swift
let array = try! NSJSONSerialization.JSONObjectWithData(jsonData, options: NSJSONReadingOptions.MutableContainers)
```

> 不过需要注意的是，一旦解析错误，程序会直接崩溃！
