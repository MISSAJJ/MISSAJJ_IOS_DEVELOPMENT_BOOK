#  优化OAuth授权用户信息

###抽取一个全局类
```Swift
//  Common.swift
//  XMGWB
//
//  Created by xiaomage on 15/12/5.
//  Copyright © 2015年 xiaomage. All rights reserved.
//

import Foundation

/// OAuth授权需要的key
let WB_App_Key = "4129759360"
/// OAuth授权需要的Secret
let WB_App_Secret = "98392a5714c6194f5aee796d971fe0ef"
/// OAuth授权需要的重定向地址
let WB_Redirect_uri = "http://www.520it.com"


// MARK: - 本地通知
/// 自定转场展现
let XMGPresentationManagerDidPresented = "XMGPresentationManagerDidPresented"
/// 自定义转场消失
let XMGPresentationManagerDidDismissed = "XMGPresentationManagerDismissed"
```