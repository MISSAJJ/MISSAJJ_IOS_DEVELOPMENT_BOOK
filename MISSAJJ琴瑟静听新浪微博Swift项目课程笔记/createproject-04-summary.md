# 阶段性小结

* 整体开发思路与使用 OC 几乎一致
* Swift 语法更加简洁，字符串拼接非常简单

```swift
vc.tabBarItem.selectedImage = UIImage(named: imageName + "_highlighted")
```

* Swift 对类型校验更加严格，不同类型的变量不允许直接计算

```swift
v.frame = CGRectOffset(rect, CGFloat(index) * w, 0)
```

* Swift 中的懒加载写法

```swift
lazy var composedButton: UIButton = {

    let btn = UIButton()

    btn.setImage(UIImage(named: "tabbar_compose_icon_add"), forState: UIControlState.Normal)
    btn.setImage(UIImage(named: "tabbar_compose_icon_add_highlighted"), forState: UIControlState.Highlighted)
    btn.setBackgroundImage(UIImage(named: "tabbar_compose_button"), forState: UIControlState.Normal)
    btn.setBackgroundImage(UIImage(named: "tabbar_compose_button_highlighted"), forState: UIControlState.Highlighted)

    // 懒加载中需要使用 self.
    self.addSubview(btn)

    return btn
}()
```

> 懒加载的代码是一个闭包，因此在代码内部需要使用 `self.`

* 不希望暴露的方法，应该使用 `private` 修饰符
* 按钮点击事件的调用是由 `运行循环` 监听并且以`消息机制`传递的，因此，按钮监听函数不能设置为 `private`

![](./images/TabBar/按钮点击事件.png)

* Swift 中的类名是包含命名空间的，如果希望用字符串动态创建并且实例化类，需要按照以下代码格式

```swift
    let namespace = NSBundle.mainBundle().infoDictionary!["CFBundleExecutable"] as! String
    let clsName = namespace + "." + vcName
    // 告诉编译器暂时就是AnyClass
    let cls: AnyClass!  = NSClassFromString(ns + "." + vcName)
    // 告诉编译器真实类型是UIViewController
    let vcCls = cls as! UIViewController.Type
    // 实例化控制器
    let vc = vcCls.init()
```

> tabBar 的 items 是在 `视图将要出现之前` 才被创建的
