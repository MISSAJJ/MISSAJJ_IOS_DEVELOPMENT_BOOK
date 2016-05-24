# 闭包

* 闭包定义
* 闭包简化 - 尾随闭包
* 闭包参数
* 闭包返回值
* 闭包的循环引用

```swift
weak var weakSelf = self
demo("zhangsan") { (_) -> Int in
    print(weakSelf?.view.backgroundColor)

    return 20
}
```
