#Chapter-08 CocoaPods的使用
---
```objc
Update更新：2016年5月20日 By {MISSAJJ琴瑟静听}
```
```objc
- Podfile.lock文件
    - 最后一次更新Pods时, 所有第三方框架的版本号
- 常用指令的区别
    - pod install
        - 会根据Podfile.lock文件中列举的版本号来安装第三方框架
        - 如果一开始Podfile.lock文件不存在, 就会按照Podfile文件列举的版本号来安装第三方框架
        - 安装框架之前, 默认会执行pod repo update指令
    - pod update
        - 将所有第三方框架更新到最新版本, 并且创建一个新的Podfile.lock文件
        - 安装框架之前, 默认会执行pod repo update指令
    - pod install --no-repo-update
    - pod update --no-repo-update
        - 安装框架之前, 不会执行pod repo update指令

```