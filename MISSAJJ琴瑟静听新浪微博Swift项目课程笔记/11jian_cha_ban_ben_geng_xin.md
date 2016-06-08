#  检查版本更新

###界面切换流程

![image](images/界面切换流程图.png)


###通过注册和监听,切换根控制器

```Swift
 
 // 2.注册监听
        NSNotificationCenter.defaultCenter().addObserver(self, selector: Selector("changeRootViewController:"), name: MASwitchRootViewController, object: nil)
        
```
```Swift
/// 切换根控制器的通知
let MASwitchRootViewController =  "MASwitchRootViewController"

```