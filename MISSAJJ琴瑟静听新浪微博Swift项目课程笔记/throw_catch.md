# try throw 代码实践
---
```objc
Update更新：2016年5月30日 By {MISSAJJ琴瑟静听} 
```

> `throw catch` 是 `Xcode 7.0` 对错误处理的一个非常大的变化


### 遇到throw,进行 try 处理,写上do{}catch{}
```
Swift和OC不太一样, OC中一般情况如果发生错误会给传入的指针赋值, 而在Swift中使用的是异常处理机制
 1.以后但凡看大 throws的方法, 那么就必须进行 try处理, 而只要看到try, 就需要写上do catch
 2.do{}catch{}, 只有do中的代码发生了错误, 才会执行catch{}中的代码
 3. try  正常处理异常, 也就是通过do catch来处理
    try! 告诉系统一定不会有异常, 也就是说可以不通过 do catch来处理
         但是需要注意, 开发中不推荐这样写, 一旦发生异常程序就会崩溃
         如果没有异常那么会返回一个确定的值给我们
        
    try? 告诉系统可能有错也可能没错, 如果没有系统会自动将结果包装成一个可选类型给我们, 
    如果有错系统会返回nil, 如果使用try? 那么可以不通过do catch来处理

```

####注意: 一般建议用 try
* 如果能确保代码执行正确，可以强行 try!
* 不过需要注意的是，一旦解析错误，程序会直接崩溃！

```swift
let array = try! NSJSONSerialization.JSONObjectWithData(jsonData, options: NSJSONReadingOptions.MutableContainers)
```
 

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

 


