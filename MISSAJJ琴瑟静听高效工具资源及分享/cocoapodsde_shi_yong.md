# CocoaPods 的使用

 
---
```objc
Update更新：2016年5月23日 By {MISSAJJ琴瑟静听}
```
```objc
- Podfile.lock 文件
    - 最后一次更新Pods时, 所有第三方框架的版本号
- 常用指令的区别
    - pod install
        - 会根据 Podfile.lock 文件中列举的版本号来安装第三方框架
        - 如果一开始 Podfile.lock 文件不存在, 就会按照 Podfile 文件列举的版本号来安装第三方框架
        - 安装框架之前, 默认会执行 pod repo update 指令
    - pod update
        - 将所有第三方框架更新到最新版本, 并且创建一个新的 Podfile.lock 文件
        - 安装框架之前, 默认会执行 pod repo update 指令
    - pod install --no-repo-update
    - pod update --no-repo-update
        - 安装框架之前, 不会执行 pod repo update 指令

```