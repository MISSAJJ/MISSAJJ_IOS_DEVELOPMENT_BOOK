#  优化OAuth授权用户信息

###抽取一个全局类管理固定内容

- OC 总我们经常使用宏定义来定义常量, Swift 项目中无法使用宏定义, 但是我们可以通过创建一个全局类,如:`Common.swift`来定义常量和本地通知key等,以便高效率统一管理

```Swift
//  Common.swift 

import Foundation

/// OAuth授权需要的key
let WB_App_Key = "41xxxx59360"
/// OAuth授权需要的Secret
let WB_App_Secret = "98392a5714c6194f5aee796d971fe0ef"
/// OAuth授权需要的重定向地址
let WB_Redirect_uri = "http://www.missajj.com"


// MARK: - 本地通知
/// 自定转场展现
let MAPresentationManagerDidPresented = "MAPresentationManagerDidPresented"
/// 自定义转场消失
let MAPresentationManagerDidDismissed = "MAPresentationManagerDismissed"
```
###全局调用的写法格式: 

- 直接调用: 

```Swift
if !urlStr.hasPrefix(WB_Redirect_uri)
        {
            NJLog("不是授权回调页面")
            return true
        }
 
 
// 2.准备请求参数
        let parameters = ["client_id": WB_App_Key, "client_secret": WB_App_Secret, "grant_type": "authorization_code", "code": code, "redirect_uri": WB_Redirect_uri]
```

- 字符串调用:先写一个斜杠`\`再将常量名写在括号`()`内,如:` \(WB_App_Key)`

```Swift
// 1.定义字符串保存登录界面URL
let urlStr = "https://api.weibo.com/oauth2/authorize?client_id=\(WB_App_Key)&redirect_uri=\(WB_Redirect_uri)"
```


###优化后的 OAuth 代码

```Swift 
//  OAuthViewController.swift 

import UIKit


class OAuthViewController: UIViewController {

    /// 网页容器
    @IBOutlet weak var customWebView: UIWebView!
    
    override func viewDidLoad() {
        super.viewDidLoad()
        // 1.定义字符串保存登录界面URL
        let urlStr = "https://api.weibo.com/oauth2/authorize?client_id=\(WB_App_Key)&redirect_uri=\(WB_Redirect_uri)"
        // 2.创建URL
        guard let url = NSURL(string: urlStr) else
        {
            return
        }
        // 3.创建Request
        let request = NSURLRequest(URL: url)
        
        // 4.加载登录界面
        customWebView.loadRequest(request)
    }

}

extension OAuthViewController: UIWebViewDelegate
{
    // 该方法每次请求都会调用
    // 如果返回false代表不允许请求, 如果返回true代表允许请求
    func webView(webView: UIWebView, shouldStartLoadWithRequest request: NSURLRequest, navigationType: UIWebViewNavigationType) -> Bool {
   
        // 1.判断当前是否是授权回调页面
        guard let urlStr = request.URL?.absoluteString else
        {
            return false
        }
        if !urlStr.hasPrefix(WB_Redirect_uri)
        {
            NJLog("不是授权回调页面")
            return true
        }
        
        NJLog("是授权回调页面")
        // 2.判断授权回调地址中是否包含code=
        // URL的query属性是专门用于获取URL中的参数的, 可以获取URL中?后面的所有内容
        let key = "code="
        guard let str = request.URL!.query else
        {
            return false
        }
        
        if str.hasPrefix(key)
        {
            let code = str.substringFromIndex(key.endIndex)
            loadAccessToken(code)
            return false
        }
        NJLog("授权失败")
        return false
    }
    
    /// 利用RequestToken换取AccessToken
    private func loadAccessToken(codeStr: String?)
    {
        guard let code = codeStr else
        {
            return
        }
        // 注意:redirect_uri必须和开发中平台中填写的一模一样
        // 1.准备请求路径
        let path = "oauth2/access_token"
        // 2.准备请求参数
        let parameters = ["client_id": WB_App_Key, "client_secret": WB_App_Secret, "grant_type": "authorization_code", "code": code, "redirect_uri": WB_Redirect_uri]
        // 3.发送POST请求
        NetworkTools.shareInstance.POST(path, parameters: parameters, success: { (task: NSURLSessionDataTask, objc: AnyObject) -> Void in
               
            
            let account = UserAccount(dict: objc as! [String : AnyObject])
            NJLog(account.saveAccount())
            
            }) { (task: NSURLSessionDataTask?, error: NSError) -> Void in
                NJLog(error)
        }
    }
}

```