# 登录&注册代理回调

* 定义协议

```swift
/// 访客视图协议
protocol VisitorViewDelegate: NSObjectProtocol
{
    /// 将要登录
    func visitorViewLogin()
    /// 将要注册
    func visitorViewRegister()
}
```

> 定义协议时，需要继承自 `NSObjectProtocol`，否则无法设置代理的类型

* 定义代理

```swift
/// 定义代理 - 一定要用 weak
    weak var delegate: VisitorViewDelegate?
```

* 按钮回调

```swift
/// 点击注册按钮
func registerBtnClick()
    {
        // OC 中需要 isResponse
        delegate?.visitorViewRegister()
    }

/// 点击登录按钮
func loginBtnClick()
    {
        delegate?.visitorViewLogin()
    }
```

* 遵守协议

```swift
class BaseTableViewController: UITableViewController, VisitorViewDelegate
```
* 设置代理

```swift
// 设置代理
visitorLoginView?.delegate = self
```

* 实现方法

```swift
// MARK: - 访客视图协议方法
/// 用户注册
func visitorViewRegister() {
        print(__FUNCTION__)
    }

/// 用户登录
func visitorViewLogin() {
        print(__FUNCTION__)
    }
```

* 修改导航条按钮监听方法

```swift
navigationItem.leftBarButtonItem = UIBarButtonItem(title: "注册", style: UIBarButtonItemStyle.Plain, target: self, action: "visitorViewRegister")
navigationItem.rightBarButtonItem = UIBarButtonItem(title: "登录", style: UIBarButtonItemStyle.Plain, target: self, action: "visitorViewLogin")
```

> 运行测试
