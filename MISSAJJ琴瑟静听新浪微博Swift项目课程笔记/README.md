# Chapter-08 MISSAJJ琴瑟静听 新浪微博 Swift 项目课程笔记

```objc
Update更新：2016年6月8日 By {MISSAJJ琴瑟静听}
 
```
``` 
笔记来源于大神班的课程笔记,
笔记架构和逻辑都很赞,课程也很详细,
在原笔记的基础上融合了自己的项目笔记，
希望能帮助童鞋们巩固记忆!
``` 
###Chapters目录章节 

####★近期正在研究新浪微博的Swift项目,会陆续更新笔记哦,欢迎关注更新★

 

* [★【Chapter-08 MISSAJJ琴瑟静听新浪微博Swift项目课程笔记】](README.md)
   * [0.1. 项目Git部署（使用OSChina开源中国)](oschina.md)    
   * [0.2. 图片素材](imagesassets.md)   
       * [0.2.1. 图标素材 & App 名称](imagesassets-01-icon_default.md)  
   * [0.3. 项目搭建](createproject.md)   
       * [0.3.1. 创建文件](createproject-01-createfiles.md)  
           * [0.3.1.1 创建自定义Log](0311_chuang_jian_zi_ding_yi_log.md)  
       * [0.3.2. 添加子控制器](createproject-02-addchildcontrollers.md)   
           * [0.3.2.0.Name Space 命名空间](0320name_space_ming_ming_kong_jian.md) 
           * [0.3.2.1. throw catch](throw_catch.md)
           * [0.3.2.2. 变量类型推导](variablestype.md)
       * [0.3.3. 添加加号按钮](createproject-03-costommaintabbar.md)
       * [0.3.4. 阶段性小结](createproject-04-summary.md)
       * [0.3.5.storyboard快速创建项目框架](035storyboardkuai_su_chuang_jian_xiang_mu_kuang_ji.md)   
   * [0.4. 未登录页面处理](visitor.md)
       * [0.4.1. 表格视图控制器基类](visitor-01-basetableviewcontroller.md)
       * [0.4.2. 用户登录视图](visitor-02-userloginview.md)
       * [0.4.3. 设置未登录信息](visitor-03-loginlogic.md)
       * [0.4.4. 首页动画](visitor-04-homeanimation.md)
       * [0.4.5. 登录&注册代理回调](visitor-05-logindelegate.md)
       * [0.4.6. 设置全局外观](visitor-06-setupappearance.md)
       * [0.4.7. 阶段性小结](visitor-07-summary.md)
   * [0.5. 代码评审(Code Review)](code_review.md)
   * [0.6.主界面导航栏按钮和下拉菜单](06dao_hang_tiao_biao_ti_cai_dan_an_niu.md)
       * [0.6.1.导航栏左右按钮](061dao_hang_lan_zuo_you_an_niu.md)
       * [0.6.2.导航栏下拉菜单](062dao_hang_lan_xia_la_cai_dan.md)
           * [0.6.2.1.通过Storyboard布局下拉菜单](0621tong_guo_storyboard_bu_ju_xia_la_cai_dan.md)
           * [0.6.2.2.完善下拉菜单转场效果](0622wan_shan_xia_la_cai_dan_zhuan_chang_xiao_guo.md)
           * [0.6.2.3.封装优化下拉菜单的转场效果](0623feng_zhuang_you_hua_xia_la_cai_dan_de_zhuan_ch.md)
   * [0.7.扫描,读取和生成二维码](07sao_63cf2c_du_qu_he_sheng_cheng_er_wei_ma.md)  
       * [0.7.1.搭建二维码界面](071da_jian_er_wei_ma_jie_mian.md) 
       * [0.7.2.二维码冲击波动画](072er_wei_ma_chong_ji_bo_dong_hua.md)  
       * [0.7.3.扫描二维码](073sao_miao_er_wei_ma.md)  
       * [0.7.4.定位二维码](074ding_wei_er_wei_ma.md)  
       * [0.7.5.指定扫描区域](075zhi_ding_sao_miao_qu_yu.md)  
       * [0.7.6.扫描相册二维码](076sao_miao_xiang_ce_er_wei_ma.md)  
       * [0.7.7.生成二维码图片](077sheng_cheng_er_wei_ma_tu_pian.md)  
   * [0.8.新浪微博如何进行OAuth授权](08oauthshou_quan_deng_lu.md)
       * [0.8.1.获取OAuth授权换取AccessToken全过程](081huo_quoauth_shou_quan_huan_qu_accesstoken_quan_.md)
       * [0.8.2.优化OAuth授权用户信息](082you_hua_oauth_shou_quan_yong_hu_xin_xi.md)
       * [0.8.3.优化用户数据缓存](083you_hua_yong_hu_shu_ju_huan_cun.md)
       * [0.8.4.用户过期时间的处理](084yong_hu_guo_qi_shi_jian_de_chu_li.md)
       * [0.8.5.获取保存用户的详细信息](085huo_qu_bao_cun_yong_hu_de_xiang_xi_xin_xi.md)
   * [0.9.新浪微博动画欢迎界面](09xin_lang_wei_bo_dong_hua_huan_ying_jie_mian.md)
   * [10.新浪微博新特性界面和按钮动画](10xin_lang_wei_bo_xin_te_xing_jie_mian_he_an_niu_d.md)
   * [11.检查版本更新](11jian_cha_ban_ben_geng_xin.md)
   * [12.加载微博数据](12jia_zai_wei_bo_shu_ju.md)
   * [13.加载用户数据](13jia_zai_yong_hu_shu_ju.md)


## 新浪微博
作为中国移动互联网的代表性产品之一，新浪微博涵盖了大量的移动互联网元素，通过对新浪微博的研究及模仿可以：
- 产品面
    + 对这些元素在实际产品中的应用有深入的了解和认识

- 技术面
    + 体验Swift的优雅之处
    + 能够体会在项目开发中各知识点的综合运用
    + 能够对封装 & 重构有更好的体会和理解
    + 能够引出相关的知识点，发现自己的知识空缺
    + 能够对大型项目的架构、开发及掌控有更全面的认识和理解

---
## iPhone 项目目标
* 项目掌控能力
    + 从无到有
    + 如何给项目扩充新功能
    + 如何维护大型项目
    + ... ...
* 工具使用能力
    + CocoaPods
    + 三方框架
    + Xcode自带工具
    + ... ...
* 开发技巧能力
    + 各种思想
    + 各种技巧
    + ... ...

---
### 新浪微博接口地址

* 微博开放平台地址
[http://open.weibo.com](http://open.weibo.com)

* 微博接口文档地址
[http://open.weibo.com/wiki/微博API](http://open.weibo.com/wiki/微博API)

---
## 项目主体框架
走向工作岗位之后，一般会遇到两种工作情况：

1. 新项目开发
    * 通常在项目开始之前，公司的产品经理会提供完整的产品原型图，或功能设计文档
    * 通过对这些文档的解读，能够梳理出目标项目的整体架构，从而协助项目框架的搭建

2. 旧项目维护
    * 很多老项目是缺乏文档的，这种情况在一些小公司中表现的尤为突出
    * 要想快速上手一个老项目，首先运行项目，并且整理项目整体框架结构
    * 然后用整理出的框架结构与代码集合进行验证，无疑可以对了解项目的整体架构起到重要的辅助

综上所述，无论是新项目，还是老项目，在开发之前确定项目的主体架构都是非常重要，也是十分必要的！

### 主体架构确认的好处

开发之前，明确项目的主体架构具有以下好处：

1. 明确开发目标，项目一旦启动，始终锁定目标前进！
2. 明确功能模块的数量，方便工期核算
3. 根据开发进度，预判开发周期，及时与相关部门沟通、协调
4. 根据主体架构搭建项目框架，方便团队开发，各个功能模块齐头并进，提高开发效率！
5. 确定项目开发中的重点难点，提前安排攻关能力强的同事进行技术攻关，待需要时能够享受攻关成果，或者及时调整产品设计
6. 新增或调整功能时，能够高屋建瓴，在最合适的位置添加相关功能模块

### 新浪微博主体架构

对界面预览之后，可以发现新浪微博符合经典应用程序架构设计：

- 主视图控制器是一个 `UITabbarController`
- 包含四个 `UINavigationController`，分别是
    * 首页
    * 消息
    * 发现
    * 我

特殊之处：
- `UITabbarController` 中间有一个 "+" 按钮，点击该按钮能够 Modal 显示`微博类型选择`界面，方便用户选择自己需要的微博类型
- 四个 `UINavigationController` 在用户登录前后显示的界面格式是不一样的

#### 和原版新浪微博的区别

由于必须使用新浪微博官方的 API 才能够正常开发，换言之，如果没有登录系统是无法使用新浪微博提供的接口的！

基于上述原因，在实际开发中对未登录之前的界面设计进行简化
