# 优化用户数据缓存


###输出用户数据信息详情

```Swift 
    override var description: String {
        // 将模型转换为字典
        let property = ["access_token", "expires_in", "uid"]
        let dict = dictionaryWithValuesForKeys(property)
        // 将字典转换为字符串
        return "\(dict)"
    }
```

###优化缓存路径

- 创建String+Extension.swift,用于统一管理路径

```Swift 
//  String+Extension.swift 

import UIKit

extension String
{
    /// 快速生成缓存路径
    func cachesDir() -> String
    {
        // 1.获取缓存目录的路径
        let path = NSSearchPathForDirectoriesInDomains(NSSearchPathDirectory.CachesDirectory, NSSearchPathDomainMask.UserDomainMask, true).last!
        // 2.生成缓存路径
        let name = (self as NSString).lastPathComponent
        let filePath = (path as NSString).stringByAppendingPathComponent(name)
        
        return filePath
    }
    /// 快速生成文档路径
    func docDir() -> String
    {
        // 1.获取缓存目录的路径
        let path = NSSearchPathForDirectoriesInDomains(NSSearchPathDirectory.DocumentDirectory, NSSearchPathDomainMask.UserDomainMask, true).last!
        // 2.生成缓存路径
        let name = (self as NSString).lastPathComponent
        let filePath = (path as NSString).stringByAppendingPathComponent(name)
        
        return filePath
    }
    /// 快速生成临时路径
    func tmpDir() -> String
    {
        // 1.获取缓存目录的路径
        let path = NSTemporaryDirectory()
        
        // 2.生成缓存路径
        let name = (self as NSString).lastPathComponent
        let filePath = (path as NSString).stringByAppendingPathComponent(name)
        
        return filePath
    }
}

```
###优化调用方法

```Swift  
//缓存路径 ("缓存文件名".cachesDir())  
//如: "useraccount.plist".cachesDir()
static let filePath: String = "useraccount.plist".cachesDir()
//归档数据
NSKeyedArchiver.archiveRootObject(self, toFile: UserAccount.filePath)
```

###优化后的UserAccount.swift代码

```Swift  
//  UserAccount.swift 

import UIKit

class UserAccount: NSObject, NSCoding {
    
    var access_token: String?
    var expires_in: Int = 0
    var uid: String?
    
    // MARK: - 生命周期方法
    init(dict: [String: AnyObject])
    {
        super.init()
        // 如果要想初始化方法中使用KVC必须先调用super.init初始化对象
        // 如果属性是基本数据类型, 那么建议不要使用可选类型, 因为基本数据类型的可选类型在super.init()方法中不会分配存储空间
        self.setValuesForKeysWithDictionary(dict)
    }
    
    // 当KVC发现没有对应的key时就会调用
    override func setValue(value: AnyObject?, forUndefinedKey key: String) {
        
    }
    override var description: String {
        // 将模型转换为字典
        let property = ["access_token", "expires_in", "uid"]
        let dict = dictionaryWithValuesForKeys(property)
        // 将字典转换为字符串
        return "\(dict)"
    }
    
    // MARK: - 外部控制方法
    // 归档模型
    func saveAccount() -> Bool
    {
        /*
        // 1.获取缓存目录的路径
        let path = NSSearchPathForDirectoriesInDomains(NSSearchPathDirectory.CachesDirectory, NSSearchPathDomainMask.UserDomainMask, true).last!
        // 2.生成缓存路径
        let filePath = (path as NSString).stringByAppendingPathComponent("useraccount.plist")
        NJLog(filePath)
        */
        // 3.归档对象  (优化后的代码)
        return NSKeyedArchiver.archiveRootObject(self, toFile: UserAccount.filePath)
    }
    
    /// 定义属性保存授权模型
    static var account: UserAccount?
//    static let filePath: String = (NSSearchPathForDirectoriesInDomains(NSSearchPathDirectory.CachesDirectory, NSSearchPathDomainMask.UserDomainMask, true).last! as NSString).stringByAppendingPathComponent("useraccount.plist")
    // 文件保存路径(优化后的代码)
    static let filePath: String = "useraccount.plist".cachesDir()
    
    // 解归档模型
    class func loadUserAccount() -> UserAccount?
    {
        // 1.判断是否已经加载过了
        if UserAccount.account != nil{
            NJLog("已经有加载过")
            // 直接返回
            return UserAccount.account
        }
        
        // 2.尝试从文件中加载
        NJLog("还没有加载过")
        /*
        // 1.获取缓存目录的路径
        let path = NSSearchPathForDirectoriesInDomains(NSSearchPathDirectory.CachesDirectory, NSSearchPathDomainMask.UserDomainMask, true).last!
        // 2.生成缓存路径
        let filePath = (path as NSString).stringByAppendingPathComponent("useraccount.plist")
        */
        
        // 3.解归档对象 (优化后的代码)
        guard let account = NSKeyedUnarchiver.unarchiveObjectWithFile(UserAccount.filePath) as? UserAccount else
        {
            return UserAccount.account
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
    }
    
    required init?(coder aDecoder: NSCoder)
    {
        self.access_token = aDecoder.decodeObjectForKey("access_token") as? String
        self.expires_in = aDecoder.decodeIntegerForKey("expires_in") as Int
        self.uid = aDecoder.decodeObjectForKey("uid") as? String
    }
}

```

