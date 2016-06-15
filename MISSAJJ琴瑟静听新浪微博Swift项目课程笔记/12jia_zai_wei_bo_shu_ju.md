# 12.加载微博数据
---
```objc
Update更新：2016年6月15日 By {MISSAJJ琴瑟静听} 
```

发送网络请求获得数据的相关的代码, 最好是封装在同一个类里或者自己封装的框架里, 之前我们已经在进行OAuth授权的时候创建了`NetworkTools.swift`用于请求获取AccessToken,现在我们就将请求微博数据的网络请求也写在这个类里, 今后只要通过那个类来统一管理维护就可.

###封装网络请求
- 发送网络请求三部曲
 - 1.准备路径 
 - 2.准备参数 
 - 3.发送GET请求


```swift 
// MARK: - 外部控制方法
    func loadStatuses()
    {
        
        //因为一定要有UserAccount.loadUserAccount的值才能发送请求,
        //所以要通过断言提醒其他开发者,接着就可以放心去进行强制解包了.
        assert(UserAccount.loadUserAccount() != nil, "必须授权之后才能获取微博数据")
        
        // 1.准备路径
        let path = "2/statuses/home_timeline.json"
        // 2.准备参数
        let parameters = ["access_token": UserAccount.loadUserAccount()!.access_token!]
        // 3.发送GET请求
        GET(path, parameters: parameters, success: { (task, objc) -> Void in 
            //获取后处理
            }) { (task, error) -> Void in
            //错误处理    
        }
    } 
```
- 调用方法

```swift
 // MARK: - 内部控制方法
    private func loadData()
    {
     NetworkTools.shareInstance.loadStatuses { (array, error) in
     //获取数据后处理   
        }
    }

```
###创建微博数据模型

```swift 
//  Status.swift 
import UIKit

class Status: NSObject {
    /// 微博创建时间
    var created_at: String?
    
    /// 字符串型的微博ID
    var idstr: String?
    
    /// 微博信息内容
    var text: String?
    
    /// 微博来源
    var source: String?
    
    ///字典转模型
    init(dict: [String: AnyObject])
    {
        super.init()
        setValuesForKeysWithDictionary(dict)
    }
    //对应数据
    override func setValue(value: AnyObject?, forUndefinedKey key: String) {
        
    }
    
    //为了方便调试, 重写description
    override var description: String {
        let property = ["created_at", "idstr", "text", "source"]
        let dict = dictionaryWithValuesForKeys(property)
        return "\(dict)"
    }
    
}
```

###完善和优化网络请求代码
```swift 
//  NetworkTools.swift 

import UIKit
import AFNetworking

class NetworkTools: AFHTTPSessionManager {

    // Swift推荐我们这样编写单例
    static let shareInstance: NetworkTools = {

        // 注意: baseURL后面一定更要写上./
      let baseURL = NSURL(string: "https://api.weibo.com/")!
        
       let instance = NetworkTools(baseURL: baseURL, sessionConfiguration: NSURLSessionConfiguration.defaultSessionConfiguration())
        
        instance.responseSerializer.acceptableContentTypes = NSSet(objects:"application/json", "text/json", "text/javascript", "text/plain") as Set
        
        
        
        return instance
    }()

    // MARK: - 外部控制方法
    func loadStatuses(finished: (array: [[String: AnyObject]]?, error: NSError?)->())
    {
        //因为一定要有UserAccount.loadUserAccount的值才能发送请求,
        //所以要通过断言提醒其他开发者,接着就可以放心去进行强制解包了.
        assert(UserAccount.loadUserAccount() != nil, "必须授权之后才能获取微博数据")
        
        // 1.准备路径
        let path = "2/statuses/home_timeline.json"
        // 2.准备参数
        let parameters = ["access_token": UserAccount.loadUserAccount()!.access_token!]
        // 3.发送GET请求
        GET(path, parameters: parameters, success: { (task, objc) -> Void in
            
            // 返回数据给调用者[服务器返回objc是字典,在objc内通过key获取到的statuses也是字典数组]
            guard let arr = (objc as! [String: AnyObject])["statuses"] as? [[String: AnyObject]] else
            {
                finished(array: nil, error: NSError(domain: "com.missajj.www", code: 1000, userInfo: ["message": "没有获取到数据"]))
                return
            }
        
            finished(array: arr, error: nil)
            }) { (task, error) -> Void in
                finished(array: nil, error: error)
        }
    }
}
 
```

###获取数据后的处理
```swift 
   // MARK: - 内部控制方法
    private func loadData()
    {
           NetworkTools.shareInstance.loadStatuses { (array, error) -> () in
                // 1.安全校验
                if error != nil
                {
                    SVProgressHUD.showErrorWithStatus("获取微博数据失败", maskType: SVProgressHUDMaskType.Black)
                    return
                }
                //守护校验返回数据一定有值
                guard let arr = array else
                {
                    return
                }
                
                // 2.将字典数组转换为模型数组
                var models = [Status]()
                for dict in arr
                {
                    let status = Status(dict: dict)
                    models.append(status)
                }
           
        }
    }
```


```swift 

```


```swift 

```