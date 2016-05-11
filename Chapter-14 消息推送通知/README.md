 
#推送通知的介绍
---
###什么是推送通知?

- 注意：这里说的推送通知跟NSNotification有所区别
 - NSNotification是抽象的，不可见的
 - 推送通知是可见的（能用肉眼看到）
- 作用:让不在前台(后台或者关闭)的APP知道APP内部发生的事情
- 通知的分类
 - 本地通知
   - 概念:由APP本身给应用程序推送消息,不需要服务器的支持
   - 常见场景:记账软件定时提醒记账/番茄工作法中提醒你时间等等
   - 注意:不是非常常用.
   
 - 远程通知
   - 概念:由服务器推送消息给用户,需要服务器的支持
   - 常见场景:微信提醒新消息/淘宝提醒有新活动/视频软件提供您有最新电影
   - 注意:非常常用.但是如果仅仅是给用户提醒,客户端(你)做的事情就非常简单.

- 推送通知的呈现样式(了解)
   - 在屏幕顶部显示一块横幅
    
![image](Snip20151027_56.png)
   - 在屏幕中间弹出一个UIAlertView
   
![image](Snip20151027_57.png)
   - 锁屏界面也可以显示
   
![image](Snip20151027_58.png)
   - 收到通知时,同时播放音效.
   - 收到通知时,改变APP图标上的数字

###本地通知
一.本地通知的介绍

- 直接由应用程序(程序中写入对应代码)给用户发出通知
- 本地通知需要用到一个重要的类:UILocalNotification
- 本地通知的实现步骤
 - 创建本地通知
 - 设置本地通知要发出的内容等信息
   - 发出时间
   - 发出内容
   - 播放的音效
 - 调度本地通知

二.实现本地通知

1.注册通知

- iOS8之后,如果想要发出通知(无论本地还是远程),必须先进行注册.(iOS8之前不需要)
- 通常是在didFinishLaunchingWithOptions中进行注册
- 代码如下:
```
UIUserNotificationSettings *settings = [UIUserNotificationSettings settingsForTypes:UIUserNotificationTypeBadge | UIUserNotificationTypeSound | UIUserNotificationTypeAlert categories:nil];
[application registerUserNotificationSettings:settings];
```

2.创建并且发出通知

- 创建本地通知
```
    UILocalNotification *localNote = [[UILocalNotification alloc] init];
```
- 设置本地通知的内容
```
      // 2.设置本地通知的信息
      // 2.1.设置提示信息
      localNote.alertBody = @"吃饭了吗?";
      // 2.2.设置通知弹出的时间
      localNote.fireDate = [NSDate dateWithTimeIntervalSinceNow:5.0];
      // 2.3.设置滑块显示的文字
      localNote.alertAction = @"快点";
      // 2.4.是否让上面的文字生效
      localNote.hasAction = NO;
      // 2.5.设置通知中心的标题
      localNote.alertTitle = @"小码哥哥";
      // 2.6.设置通知的声音
      localNote.soundName = @"buyao.wav";
      // 2.7.设置应用程序图标右上角的数字
      localNote.applicationIconBadgeNumber = 10;
```
- 调度本地通知(调度之后某个时刻会弹出通知)
```
      // 3.调度通知
      [[UIApplication sharedApplication] scheduleLocalNotification:localNote];
```