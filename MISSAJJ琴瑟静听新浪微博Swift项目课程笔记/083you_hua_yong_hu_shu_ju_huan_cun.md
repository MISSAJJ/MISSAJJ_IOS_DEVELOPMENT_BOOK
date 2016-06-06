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

