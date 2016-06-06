#  新浪微博如何进行OAuth授权

---
```objc
Update更新：2016年6月6日 By {MISSAJJ琴瑟静听} 
```


   * [0.8.新浪微博如何进行OAuth授权](08oauthshou_quan_deng_lu.md)
       * [0.8.1.获取OAuth授权换取AccessToken全过程](081huo_quoauth_shou_quan_huan_qu_accesstoken_quan_.md)
       * [0.8.2.优化OAuth授权用户信息](082you_hua_oauth_shou_quan_yong_hu_xin_xi.md)
       * [0.8.3.优化用户数据缓存](083you_hua_yong_hu_shu_ju_huan_cun.md)
       * [0.8.4.用户过期时间的处理](084yong_hu_guo_qi_shi_jian_de_chu_li.md)
       * [0.8.5.获取保存用户的详细信息](085huo_qu_bao_cun_yong_hu_de_xiang_xi_xin_xi.md)



###1.成为新浪的开发者
去open.weibo.com上注册一个账号

###2.在开发者平台创建一个属于自己的应用程序

  - App Key 如：412XXXXX60
  - App Secret 如：98392a571XXXXXXXXXXXXXXX971feXXX
 

###3.对自己创建的应用程序, 开始进行OAuth授权
  - 获取未授权的RequestToken(获取登录界面)
  - 
https://api.weibo.com/oauth2/authorize?client_id=你的应用ID号&redirect_uri=http://www.MISSAJJ.com

  - 获取已经授权的RequestToken(让用户登录)
只要用户输入账号密码, 并且点击授权
http://www.MISSAJJ.com/?code=a3581b5124e7c2s5dd01a664c7691a7d
    - 注意: 其中code= 后面的字符串就是已经授权的RequestToken

###4.利用已经授权的RequestToken换取AccessToken





