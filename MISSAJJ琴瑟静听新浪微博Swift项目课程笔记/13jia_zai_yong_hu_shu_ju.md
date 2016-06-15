# 13.加载用户数据
---
```objc
Update更新：2016年6月15日 By {MISSAJJ琴瑟静听} 
```

获取到的微博数据`statuses`内还有一个`key`是`user`数据,用于展示该条微博是哪个用户所发,所以我们需要再增加一个`user.swift`模型,在转换微博数据的同时就把`user`转换成`user.swift`模型,接着再继续进行数据处理



###创建用户模型
```swift 
//  User.swift  

import UIKit

class User: NSObject {
    /// 字符串型的用户UID
    var idstr: String?
    
    /// 用户昵称
    var screen_name: String?
    
    /// 用户头像地址（中图），50×50像素
    var profile_image_url: String?
    
    /// 用户认证类型
    var verified_type: Int = -1
    
     ///字典转模型
    init(dict: [String: AnyObject])
    {
        super.init()
        setValuesForKeysWithDictionary(dict)
    }
     
    override func setValue(value: AnyObject?, forUndefinedKey key: String) {
        
    }
    
    //描述
    override var description: String {
        let property = ["idstr", "screen_name", "profile_image_url", "verified_type"]
        let dict = dictionaryWithValuesForKeys(property)
        return "\(dict)"
    }

}
```


###引入user模型

```swift 
  /// 微博作者的用户信息
    var user: User?
```

但是`setValuesForKeysWithDictionary(dict)`这个方法, 通过`KVC`是不会把字典转模型的,所以我们需要通过重写`setValue(value: AnyObject?, forKeyPath keyPath: String) `拦截`user`进行赋值操作

```swift 
 /// KVC的setValuesForKeysWithDictionary方法内部会调用setValue方法
    override func setValue(value: AnyObject?, forKey key: String) {
        
        NJLog("key = \(key), value = \(value)")
        // 1.拦截user赋值操作
        if key == "user"
        {
            user = User(dict: value as! [String : AnyObject])
            return
        }
        //注意:拦截一定要写在super.setValue之前
        super.setValue(value, forKey: key)
    }

```

###微博模型的完整代码

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
    
    /// 微博作者的用户信息
    var user: User?
    
    init(dict: [String: AnyObject])
    {
        super.init()
        setValuesForKeysWithDictionary(dict)
    }
    
    
  
    /// KVC的setValuesForKeysWithDictionary方法内部会调用setValue方法
    override func setValue(value: AnyObject?, forKey key: String) {
        
        NJLog("key = \(key), value = \(value)")
        // 1.拦截user赋值操作
        if key == "user"
        {
            user = User(dict: value as! [String : AnyObject])
            return
        }
        super.setValue(value, forKey: key)
    }
    override func setValue(value: AnyObject?, forUndefinedKey key: String) {
        
    }
    
    override var description: String {
        let property = ["created_at", "idstr", "text", "source"]
        let dict = dictionaryWithValuesForKeys(property)
        return "\(dict)"
    }
    
}


```