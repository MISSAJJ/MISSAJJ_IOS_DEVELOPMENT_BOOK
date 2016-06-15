# 12.加载微博数据

发送网络请求获得数据的相关的代码, 最好是封装在一个类里, 之前我们已经在进行OAuth授权的时候创建了`NetworkTools.swift`这个类,用于请求获取AccessToken,现在我们就将请求微博数据的网络请求也写在这个类里, 今后只要通过那个类来统一管理维护就可.

###封装网络请求
发送网络请求三部曲
- 1.准备路径 
- 2.准备参数 
- 3.发送GET请求

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
        
        assert(UserAccount.loadUserAccount() != nil, "必须授权之后才能获取微博数据")
        
        // 1.准备路径
        let path = "2/statuses/home_timeline.json"
        // 2.准备参数
        let parameters = ["access_token": UserAccount.loadUserAccount()!.access_token!]
        // 3.发送GET请求
        GET(path, parameters: parameters, success: { (task, objc) -> Void in
            
            // 返回数据给调用者
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


```swift 

```


```swift 

```


```swift 

```