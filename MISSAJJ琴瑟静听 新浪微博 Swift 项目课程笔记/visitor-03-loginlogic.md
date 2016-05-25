# 登录视图代码实现

* 设置访客视图信息

```swift
///  设置访客视图信息
///
///  :param: iconName 图标名称
///  :param: message  消息文字
///  :param: isHome   是否首页
func setupVisitorInfo(isHome: Bool, imageName: String, message: String){
        iconView.hidden = !isHome
        messageLabel.text = message
        homeIconView.image = UIImage(named: imageName)
    }
```

* 在 `BaseTableViewController` 中添加登录视图属性

```swift
/// 访客登录视图
var visitorLoginView: VisitorLoginView?
```

* 在 `setupVisitorView` 中记录登录视图

```swift
// 替换根视图
visitorLoginView = VisitorLoginView()
view = visitorLoginView
```

## 修改功能视图控制器中的代码

* HomeTableViewController

```swift
visitorView?.setupVisitorInfo(true, imageName: "visitordiscover_feed_image_house", message: "关注一些人，回这里看看有什么惊喜")
```

* MessageTableViewController

```swift
visitorView?.setupVisitorInfo(false, imageName: "visitordiscover_image_message", message: "登录后，别人评论你的微博，发给你的消息，都会在这里收到通知")
```

* DiscoverTableViewController

```swift
visitorView?.setupVisitorInfo(false, imageName: "visitordiscover_image_message", message: "登录后，最新、最热微博尽在掌握，不再会与实事潮流擦肩而过")
```

* ProfileTableViewController

```swift
visitorView?.setupVisitorInfo(false, imageName: "visitordiscover_image_profile", message: "登录后，你的微博、相册、个人资料会显示在这里，展示给别人")
```

