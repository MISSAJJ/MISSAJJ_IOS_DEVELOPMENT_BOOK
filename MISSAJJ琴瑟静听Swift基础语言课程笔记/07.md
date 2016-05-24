# 函数

## 简单演练

```swift
func sum(a: Int, b: Int) -> Int {
    return a + b
}
```

* 阶段性小结
    * 函数定义格式：`func 函数名(参数: 参数类型...) -> 返回值 { // 代码实现 }`
    * 如果没有返回值， `-> 返回值` 可以省略
    * `->` 是一个很有意思的符号
    * 默认情况下，在调用函数时，第一个参数名是省略的

## 参数名的特殊处理

### 强制要求参数名

```swift
func sum1(x a: Int, y b: Int) -> Int {
    return a + b
}
```

### 省略参数名

```swift
func sum2(a: Int, _ b: Int) -> Int {
    return a + b
}
```




