# 获取保存用户的详细信息

```Swift

    /// 获取用户信息
    func loadUserInfo(finished: (account: UserAccount?, error: NSError?)->())
    {
        // 断言
        // 断定access_token一定是不等于nil的, 如果运行的时access_token等于nil, 那么程序就会崩溃, 并且报错
        assert(access_token != nil, "使用该方法必须先授权")
        
        // 1.准备请求路径
        let path = "2/users/show.json"
        // 2.准备请求参数
        let parameters = ["access_token": access_token!, "uid": uid!]
        // 3.发送GET请求
        NetworkTools.shareInstance.GET(path, parameters: parameters, success: { (task, objc) -> Void in
            
            let dict = objc as! [String: AnyObject]
            
            // 1.取出用户信息
            self.avatar_large = dict["avatar_large"] as? String
            self.screen_name = dict["screen_name"] as? String
            
            // 2.保存授权信息
//            self.saveAccount()

            //通过闭包来保存用户信息
            finished(account: self, error: nil)
            
            }) { (task, error) -> Void in

                finished(account: nil, error: error)
        }
    }
    
    /// 判断用户是否登录
    class func isLogin() -> Bool {
        return UserAccount.loadUserAccount() != nil
    }
```



###继续优化后的代码

