#  优化OAuth授权用户信息

###抽取一个全局类管理固定内容

 OC 总我们经常使用宏定义来定义常量, Swift 项目中无法使用宏定义, 但是我们可以通过创建一个全局类,如:`Common.swift`来定义常量和本地通知key等,以便高效率统一管理

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
- 调用写法格式: 
  - 先写一个斜杠`\`再将常量名写在括号`()`内,如:` \(WB_App_Key)`

```Swift
// 1.定义字符串保存登录界面URL
let urlStr = "https://api.weibo.com/oauth2/authorize?client_id=\(WB_App_Key)&redirect_uri=\(WB_Redirect_uri)"
```