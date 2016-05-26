# 设置全局外观

* 修改 AppDelegate 代码如下

```swift
/// 设置全局外观
private func setupAppearance() {
    UINavigationBar.appearance().tintColor = UIColor.orangeColor()
    UITabBar.appearance().tintColor = UIColor.orangeColor()
}
```

## 注意

1. 应用程序外观一经设置，全局有效，通常在 `AppDelegate` 中设置
2. `AppDelegate` 是应用程序的入口，是快速阅读程序的入口，整理文件夹的时候不要把 `AppDelegate` 藏起来
