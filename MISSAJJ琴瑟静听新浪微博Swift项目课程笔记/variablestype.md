# 变量类型推导

* 在 swift 开发中，对象的类型是`自动推导`的
* 对象的准确类型由创建对象时的代码决定
* 如果定义变量时，右侧代码具有歧义，可以在左侧以 `:类型` 指定变量的类型，例如：
```swift
// 个人推荐
let i: CGFloat = 10
```

* 也可以写成
```swift
var j = 10 as? CGFloat
```

* 如果某些方法返回的数据类型是 `AnyObject`/`AnyClass`，则需要在右侧使用 `as 类型` 表明类型，并且根据返回值`是否是可选项`，添加 `!` 或者 `?`，例如：
```swift
let ns = NSBundle.mainBundle().infoDictionary!["CFBundleExecutable"] as! String
```

* 如果某些方法返回类型是 `AnyObject`/`AnyClass`，但是对象类型是动态生成的，也就是说，编码时同样无法确定改对象的准确类型，可以在左侧使用 `: AnyObject` 或者 `: AnyClass` 告诉编译器暂不处理，例如：
```swift
let cls: AnyClass = NSClassFromString(ns + "." + vcName)!
```

提示：`as?` 和 `as!` 是刚接触 swift 最令人烦恼的语法之一，苹果也在这个语法规则上多次做过调整，在学习时建议：

* 多用 `option + click` 查看对象信息
* 借助 Xcode 智能提示修改
* 多思考，多练习




