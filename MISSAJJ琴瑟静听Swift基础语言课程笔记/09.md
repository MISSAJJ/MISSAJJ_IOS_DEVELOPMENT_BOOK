# 懒加载

```swift
lazy var demoView: UIView = {

    let v = UIView(frame: CGRectMake(10, 10, 100, 100))
    v.backgroundColor = UIColor.redColor()

    return v
}()
```

* 格式：

```
lazy var 变量: 类型 = { 创建变量代码 }()
```

* 懒加载的写法本质上是定义并执行一个闭包




