#  新浪微博如何进行OAuth授权



###1.成为新浪的开发者
去open.weibo.com上注册一个账号

###2.在开发者平台创建一个属于自己的应用程序

  - App Key 如：412XX59360
  - App Secret 如：98392a5714c6XXXf5aee796d971fe0ef
 

###3.对自己创建的应用程序, 开始进行OAuth授权
  - 获取未授权的RequestToken(获取登录界面)
https://api.weibo.com/oauth2/authorize?client_id=你的应用ID&redirect_uri=http://www.MISSAJJ.com

  - 获取已经授权的RequestToken(让用户登录)
只要用户输入账号密码, 并且点击授权
http://www.MISSAJJ.com/?code=a3581b5124e7c2s5dd01a664c7691a7d
    - 注意: 其中code= 后面的字符串就是已经授权的RequestToken

###4.利用已经授权的RequestToken换取AccessToken





