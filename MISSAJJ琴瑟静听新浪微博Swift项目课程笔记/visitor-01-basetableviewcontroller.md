# 表格视图控制器基类

## 功能需求

* 判断用户是否登录，如果没有登录
    * 使用用户登录视图替换表格视图控制器的默认视图
    * 在导航栏左侧添加 `注册` 按钮
    * 在导航栏右侧添加 `登录` 按钮

## 代码实现

* 新建 `BaseTableViewController`
* 将功能主界面的视图控制器基类设置为 `BaseTableViewController`
    * HomeTableViewController
    * MessageTableViewController
    * DiscoverTableViewController
    * ProfileTableViewController

* 增加 用户登录标记

```swift
class BaseTableViewController: UITableViewController {

    ///  用户登录标记
    var userLogon = false

    override func loadView() {

        print(userLogon)

        super.loadView()
    }
}
```

* 根据用户登录标记判断是否加载默认视图

```swift
override func loadView() {

    userLogon = false

    userLogon ? super.loadView() : setupVisitorView()
}

///  设置访客视图
private func setupVisitorView() {
    view = UIView()
}
```

> 修改 `userLogon` 的值，运行测试界面效果

### 添加导航栏按钮

```swift
///  设置访客视图
private func setupVisitorView() {
    view = UIView()

    // 添加导航栏按钮
    navigationItem.leftBarButtonItem = UIBarButtonItem(title: "注册", style: UIBarButtonItemStyle.Plain, target: nil, action: nil)
    navigationItem.rightBarButtonItem = UIBarButtonItem(title: "登录", style: UIBarButtonItemStyle.Plain, target: nil, action: nil)
}
```
