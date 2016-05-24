# 逻辑分支

## 简单体验

```swift
var i = 10

if i > 0 {
    print("OK")
}
```

* 阶段性小结
    * `Swift` 中没有 C 语言中的`非零即真`概念
    * 在逻辑判断时必须显示地指明具体的判断条件
    * `if` 语句条件的 `()` 可以省略
    * 但是 `{}` 不能省略

## 三目

```swift
var a = 10
var b = 50

var result = a > b ? a : b
print(result)
```

* 阶段性小结
    * `Swift` 中的 `三目` 运算保持了和 OC 一致的风格

## 可选项

### 演练 1

```swift
let url = NSURL(string: "http://www.520it.com/")

if url != nil {
    NSURLSession.sharedSession().dataTaskWithURL(url!, completionHandler: { (data, _, _) -> Void in
        print(NSString(data: data!, encoding: NSUTF8StringEncoding))
    }).resume()
}
```

* 阶段性小结
    * 在 `Swift` 中，不是所有的对象实例化方法都会返回值，在实际开发中需要注意实例化函数的返回类型，例如：

    ```swift
convenience init?(string URLString: String)
```

    * 如果有 `?` 表示改方法有可能无法实例化到正确的对象
    * 这种函数返回的对象，被称为 `可选项`，即有可能有值，也有可能没有值
    * 实际开发时，需要针对这种对象加以判断，并且在分支内部使用 `!`，指明改对象确实是存在的
    * 相比在 `OC` 的开发，尤其在日常练习时，会给定一个能够运行的值，而在实际运行时，一旦条件不满足，会直接闪退，这样用户体验会非常不好

> `Swift` 的设计者考虑到因为对类型的强制要求，会让代码很难看，因此提供了一个变通的解决方案

### 演练 2

```swift
if let url = NSURL(string: "http://520it.com") {
    NSURLSession.sharedSession().dataTaskWithURL(url, completionHandler: { (data, _, _) -> Void in
        print(NSString(data: data!, encoding: NSUTF8StringEncoding))
    }).resume()
}
```

* 阶段性小结
    * 使用 `if let 常量 = 可选构造函数` 的方式能够确保分支内部常量一定是有值的
    * 并且在分支内部不再需要使用 `!`
    * 这是 `Swift` 代码中的一个非常重要的使用技巧

* 提示
    * 尽管 `Swift` 提供了类型校验的手段，但是要写出 `优雅` 的 Swift 代码，还是需要多加练习的，否则一不小心就会出现分支嵌套层次很深的代码
    * 有关 `?` 和 `!` 的选择，可以借助 Xcode 的辅助工具，但是强烈建议每次遇到提示时，要多加思考，反复揣摩

### 演练3

```swift
var name: String?
print(name?.lengthOfBytesUsingEncoding(NSUTF8StringEncoding))

name = "lnj"
print(name?.lengthOfBytesUsingEncoding(NSUTF8StringEncoding))

let l = 10
print(l + (name?.lengthOfBytesUsingEncoding(NSUTF8StringEncoding) ?? 0))
```

* 阶段性小结
    * `??` 是一个非常有用的操作符，能够快速对 `nil` 进行判断
    * 如果对象是 `nil`，则使用 `??` 后面的值代替前面的 `nil` 值参与计算
    * 在使用 `??` 时，整个部分需要使用 `()` 包装
    * 这一技巧在 `UITableView` 的数据源方法中尤为重要

