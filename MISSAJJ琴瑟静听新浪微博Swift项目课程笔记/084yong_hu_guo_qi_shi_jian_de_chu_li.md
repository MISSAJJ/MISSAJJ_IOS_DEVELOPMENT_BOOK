#  用户过期时间的处理
---
```objc
Update更新：2016年6月6日 By {MISSAJJ琴瑟静听} 
```

###didSet重写用户过期时间

```Swift 
  /// 从授权那一刻开始, 多少秒之后过期时间
    var expires_in: Int = 0
        {
        didSet{
            // 生成正在过期时间
            expires_Date = NSDate(timeIntervalSinceNow: NSTimeInterval(expires_in))
        }
    }
    /// 真正过期时间
    var expires_Date: NSDate?
```

###校验是否过期

- NSComparisonResult.OrderedAscending升序比较

```Swift 
 // 3.校验是否过期了
        /*
        guard let date = account.expires_Date else
        {
            return nil
        }
        // 2015  2016  升序比较
        if date.compare(NSDate()) == NSComparisonResult.OrderedAscending
        {
            return nil
        }
        */

        //两句代码并一句  2015 2016  
        guard let date = account.expires_Date where date.compare(NSDate()) != NSComparisonResult.OrderedAscending  else
        {
            NJLog("过期了")
            return nil
        }


```

###继续优化后的代码

```Swift  

//  UserAccount.swift 

import UIKit

class UserAccount: NSObject, NSCoding {
    /// 令牌
    var access_token: String?
    
    /// 从授权那一刻开始, 多少秒之后过期时间
    var expires_in: Int = 0
        {
        didSet{
            // 生成正在过期时间
            expires_Date = NSDate(timeIntervalSinceNow: NSTimeInterval(expires_in))
        }
    }
    /// 真正过期时间
    var expires_Date: NSDate?
    
    /// 用户ID
    var uid: String?
    
    // MARK: - 生命周期方法
    init(dict: [String: AnyObject])
    {
        super.init()
        self.setValuesForKeysWithDictionary(dict)
    }
    
    // 当KVC发现没有对应的key时就会调用
    override func setValue(value: AnyObject?, forUndefinedKey key: String) {
        
    }
    override var description: String {
        // 将模型转换为字典
        let property = ["access_token", "expires_in", "uid", "expires_Date"]
        let dict = dictionaryWithValuesForKeys(property)
        // 将字典转换为字符串
        return "\(dict)"
    }
    
    // MARK: - 外部控制方法
    // 归档模型
    func saveAccount() -> Bool
    {
        // 3.归档对象
        return NSKeyedArchiver.archiveRootObject(self, toFile: UserAccount.filePath)
    }
    
    /// 定义属性保存授权模型
    static var account: UserAccount?
    /// 归档文件路径
    static let filePath: String = "useraccount.plist".cachesDir()
    
    // 解归档模型
    class func loadUserAccount() -> UserAccount?
    {
        
        // 1.判断是否已经加载过了
        if UserAccount.account != nil{
            // 直接返回
            return nil
        }
        NJLog(UserAccount.filePath)
        // 2.尝试从文件中加载
        guard let account = NSKeyedUnarchiver.unarchiveObjectWithFile(UserAccount.filePath) as? UserAccount else
        {
            return UserAccount.account
        }
        
        // 3.校验是否过期了
        /*
        guard let date = account.expires_Date else
        {
            return nil
        }
        // 2015  2016
        if date.compare(NSDate()) == NSComparisonResult.OrderedAscending
        {
            return nil
        }
        */

        // 2015 2016
        guard let date = account.expires_Date where date.compare(NSDate()) != NSComparisonResult.OrderedAscending  else
        {
            NJLog("过期了")
            return nil
        }
        
        UserAccount.account = account
        
        return UserAccount.account
    }
    
    /// 判断用户是否登录
    class func isLogin() -> Bool {
        return UserAccount.loadUserAccount() != nil
    }
    
    // MARK: - NSCoding
    func encodeWithCoder(aCoder: NSCoder)
    {
        aCoder.encodeObject(access_token, forKey: "access_token")
        aCoder.encodeInteger(expires_in, forKey: "expires_in")
        aCoder.encodeObject(uid, forKey: "uid")
        aCoder.encodeObject(expires_Date, forKey: "expires_Date")
    }
    
    required init?(coder aDecoder: NSCoder)
    {
        self.access_token = aDecoder.decodeObjectForKey("access_token") as? String
        self.expires_in = aDecoder.decodeIntegerForKey("expires_in") as Int
        self.uid = aDecoder.decodeObjectForKey("uid") as? String
        self.expires_Date = aDecoder.decodeObjectForKey("expires_Date") as? NSDate
    }
}

```