# getter & setter

## 自定义 Person 类

```swift
class Person: NSObject {

    var name: String?
    var age: Int?
}
```

## getter & setter

```swift
var _name: String?

var name: String? {
    get {
        return _name
    }
    set {
        _name = newValue
    }
}
```

* 在 `Swift` 中以上形式的 getter & setter 很少用

## didSet

* 在 OC 中，我们通常希望在给某一个变量赋值之后，去做一些额外的操作
* 最经典的应用就是在自定义 Cell 的时候，通过模型的设置方法完成 Cell 的填充

```swift
var length: Int? {
    didSet {
        timeStr = String(format: "%02d:%02d:%02d", arguments: [length! / 3600, (length! % 3600) / 60, length! % 60])
    }
}
var timeStr: String?
```

## 计算型属性

```swift
var title: String {
    get {
        return "Mr " + (name ?? "")
    }
}
```

* 只实现 `getter` 方法的属性被称为计算型属性，等同于 OC 中的 `ReadOnly` 属性
* 计算型属性本身不占用内存空间
* 不可以给计算型属性设置数值
* 计算型属性可以使用以下代码简写

```swift
var title: String {
    return "Mr " + (name ?? "")
}
```

## 构造函数

```swift
init(dict: [NSObject: AnyObject]) {
    name = dict["name"] as? String
    age = dict["age"] as? Int
}
```

## 析构函数

```swift
deinit {
    print("88")
}
```

