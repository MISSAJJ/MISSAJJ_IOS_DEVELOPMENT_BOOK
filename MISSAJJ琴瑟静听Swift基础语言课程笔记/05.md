# 数组

## 简单体验

```swift
let arr = ["zhangsan", "lisi"]
print(arr)

// 遍历每一个元素
for a in arr {
    print(a)
}

// 像 OC 一样打印
print(arr as NSArray)

```

## 数组中保存的对象类型

```swift
// 数组中保存的都是字符串
let arr = ["zhangsan", "lisi"]

// 数组中保存的是 NSObject
let arr1 = ["zhangsan", 1]
```

* 阶段性小结
    * 数组使用 [] 定义，这一点与 OC 相同
    * 如果初始化时，所有内容类型一致，择数组中保存的是该类型的内容
    * 如果初始化时，所有内容类型不一致，择数组中保存的是 `NSObject`

## 常见数组操作

```swift
// 定义只能保存字符串类型数组
var array: [String]

// 初始化数组
array = ["zhangsan"]

// 添加元素
array.append("lisi")

print(array)

// 删除元素
array.removeAtIndex(1)
print(array)

// 删除所有元素
array.removeAll(keepCapacity: true)
print(array.capacity)

// 注意数组容量的变化
for i in 0..<10 {
    array.append("\(i)")
    print("\(array) --- \(array.capacity)")
}

// 实例化新的数组
var array2 = [String]()
array2.append("1")
array2.append("2")

// 拼接数组
array += array2

print(array)
```

* 阶段性小结
    * 如果定义数组时指定了保存对象的类型，择不能向数组中添加其他类型的内容
    * 可以使用 `[String]()`
    * `let` 定义的数组是`不可变的`
    * `var` 定义的数组是`可变的`

