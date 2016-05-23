#Chapter-03（I）地图定位CoreLocation框架

---
```objc
Update更新：2016年5月20日 By {MISSAJJ琴瑟静听}
由于此章节笔记内容比较多，所以特地分了两篇文章，
避免因文章太长而导致手机浏览器或微信内浏览崩溃
```
 
###一. IOS8.0之前的定位
####1. 前台定位

- 导入CoreLocation框架以及对应的主头文件
```objc
     #import <CoreLocation/CoreLocation.h>
```
- 创建CLLocationManager对象并设置代理
```objc
     self.locationM = [[CLLocationManager alloc] init];
     self.locationM.delegate = self;
```
- 调用方法,开始更新用户位置信息
```objc
     [self.locationM startUpdatingLocation];
```
- 在对应的代理方法中获取位置信息
```objc
     -(void)locationManager:(nonnull CLLocationManager *)manager didUpdateLocations:(nonnull NSArray<CLLocation > *)locations  
     {
         NSLog(@"每当请求到位置信息时, 都会调用此方法");  
     }
```
####2. 后台定位

- 在前台定位基础上, 勾选后台模式Location updates image

![image](勾选后台模式location udpates.png)

####3. 额外设置

- 每隔多少米定位一次
```objc
代码: self.locationM.distanceFilter = 100;
功能: 只有当最新的位置与上一次获取的位置之间的距离, 大于这个值时, 才会通过代理告诉外界
```

- 设置定位精确度
```objc
     代   码: self.locationM.desiredAccuracy = kCLLocationAccuracyBest;
     功   能: 通过设置此属性, 获取不同精确度的位置信息
     注意事项: 精确度越高，越耗电，定位所需时间越长
     枚举注解:   
```
 

 枚举值 | 含义 
 ---- | ---- 
 kCLLocationAccuracyBestForNavigation | 最适合导航
 kCLLocationAccuracyBest | 精度最好的
 kCLLocationAccuracyNearestTenMeters | 附近10米 
 kCLLocationAccuracyHundredMeters | 附近100米 
 kCLLocationAccuracyKilometer | 附近1000米 
 kCLLocationAccuracyThreeKilometers | 附近3000米

 
###3. 知识补充
```objc
1. 定位常识
    1) 标准定位服务(基于gps/基站/wifi定位, 具体使用哪种,苹果有自己规则) 
        > 程序关闭,就没法获取位置 
    2) 显著的位置变化定位服务(使用基站进行定位,所以必须要求设备有电话模块) 
        > 当app被完全关闭时,也可以接收到位置通知,并让app进入到后台处理 
        > 定位精度相比于上面,精度不大,所以耗电小,而且定位更新频率依据基站密度而定 

2. 应用场景
    1) 如果要求定位及时,精度较高,并且运行时间较短,可使用标准定位; 
    2) 如果长时间监控用户位置,用户移动速度比较快(例如打车软件),可使用后者 
```
###4. 测试环境:
```objc
1. XCode7.0之前版本,例如XCode6.4版本
2. 模拟器选择iOS8.0之前的版本
    * 原因 : XCode7.0(包含7.0)之后不支持iOS8.0之前的模拟器
```
###5.常见问题总结
```objc
    定位不到, 对应的代理方法不执行:
    首先,检查运行的模拟器是否是iOS8.0之前的系统版本
    其次,检查模拟器是否设置位置数据
    第三,确保代码无问题(一般都是代理没有设置,或者位置管理器对象是局部变量,亦或是位置管理器对象没有被强引用)
    第四,绝逼是模拟器BUG, 请重置模拟器(是重置,不是重启)
```

###二. iOS8.0之后定位
####1. 前台定位

- 导入CoreLocation框架以及对应的主头文件
```objc
     #import <CoreLocation/CoreLocation.h>
```
- 创建CLLocationManager对象并设置代理
```objc
     self.locationM = [[CLLocationManager alloc] init];
     self.locationM.delegate = self;
```
- 请求前台定位授权, 并在Info.Plist文件中配置Key ( Nslocationwheninuseusagedescription )
```objc
     [self.locationM requestWhenInUseAuthorization];
```
- 调用方法,开始更新用户位置信息
```objc
     [self.locationM startUpdatingLocation];
```
- 在对应的代理方法中获取位置信息
```objc
     -(void)locationManager:(nonnull CLLocationManager *)manager didUpdateLocations:(nonnull NSArray<CLLocation > *)locations  
     {
         NSLog(@"每当请求到位置信息时, 都会调用此方法");  
     }
```
####2. 后台定位
#####方案一:

    在前台定位基础上, 勾选后台模式Location updates 
    
![image](勾选后台模式location udpates.png)


#####注意:此时授权状态如果是前台定位, 那么当APP退到后台时, 屏幕顶部会出现蓝条


#####方案二:

- 请求前后台定位授权,并在info.plist文件中配置KEY ( NSLocationAlwaysUsageDescription )
```objc
     [self.locationM requestAlwaysAuthorization];
```
- 注意:不需要勾选后台模式, 也可以进行后台定位

- 注意:此时授权状态如果是前后台定位, 那么即使APP退到后台时, 屏幕顶部会也不会出现蓝条

####3. 监听用户授权状态

- 实现以下代理方法即可
```objc
     // 当用户授权状态发生变化时调用
     -(void)locationManager:(nonnull CLLocationManager *)manager didChangeAuthorizationStatus:(CLAuthorizationStatus)status
     {
     switch (status) {
     // 用户还未决定
     case kCLAuthorizationStatusNotDetermined:
     {
         NSLog(@"用户还未决定");
         break;
     }
     // 访问受限(苹果预留选项,暂时没用)
     case kCLAuthorizationStatusRestricted:
     {
         NSLog(@"访问受限");
         break;
     }
     // 定位关闭时和对此APP授权为never时调用
     case kCLAuthorizationStatusDenied:
     {
         // 定位是否可用（是否支持定位或者定位是否开启）
         if([CLLocationManager locationServicesEnabled])
         {
             NSLog(@"定位开启，但被拒");
             // 在此处, 应该提醒用户给此应用授权, 并跳转到"设置"界面让用户进行授权
             // 在iOS8.0之后跳转到"设置"界面代码
             NSURL *settingURL = [NSURL URLWithString:UIApplicationOpenSettingsURLString];
             if([[UIApplication sharedApplication] canOpenURL:settingURL])
             {
                 [[UIApplication sharedApplication] openURL:settingURL];
             }
         }else
         {
             NSLog(@"定位关闭，不可用");
         }
         break;
     }
         // 获取前后台定位授权
     case kCLAuthorizationStatusAuthorizedAlways:
     //  case kCLAuthorizationStatusAuthorized: // 失效，不建议使用
     {
         NSLog(@"获取前后台定位授权");
         break;
     }
         // 获得前台定位授权
     case kCLAuthorizationStatusAuthorizedWhenInUse:
     {
         NSLog(@"获得前台定位授权");
         break;
     }
     default:
         break;
     }
     }
```
####4. 测试环境:

```objc
1. XCode版本无要求
2. 模拟器选择iOS8.0之后的版本
```

####5. 常见问题总结

```objc
    定位不到, 对应的代理方法不执行:
    首先,检查是否请求授权, 并设置了对应的KEY
    其次,检查模拟器是否设置位置数据
    第三,确保代码无问题(一般都是代理没有设置,或者位置管理器对象是局部变量,亦或是位置管理器对象没有被强引用)
    第四,绝逼是模拟器BUG, 请重置模拟器(是重置,不是重启)
```
###三. iOS9.0 定位补充
####1. 定位变化
- 前台定位
```objc
    (同iOS8.0之后一致, 无任何变化, 都需要主动请求授权)
```
- 后台定位

#####方案一:

    在前台定位基础上, 勾选后台模式Location updates, 并且设置以下属性为YES
```objc
     if ([[UIDevice currentDevice].systemVersion floatValue] >= 9.0)
      {
          self.locationM.allowsBackgroundLocationUpdates = YES;
      }
```
#####方案二:

    请求前后台定位授权,并在info.plist文件中配置KEY ( NSLocationAlwaysUsageDescription )
```objc
     [self.locationM requestAlwaysAuthorization];
```
####2. 新的API

- 单次定位请求;
```objc
     代   码: [self.locationM requestAlwaysAuthorization];
     功   能: 获取一次位置信息
     实现逻辑: 
         (1) 按照定位精确度从低到高进行排序，逐个进行定位.如果在有效时间内, 定位到了精确度最好的位置, 那么就把对应的位置通过代理告知外界.
         (2) 如果获取到的位置不是精确度最高的那个，也会在定位超时后，通过代理告诉外界.
     注意事项: 
         (1) 必须实现代理的-locationManager:didFailWithError:方法
         (2) 不能与startUpdatingLocation方法同时使用
```

####3. 测试环境:

```objc
1. XCode版本要求7.0版本以上
2. 模拟器选择iOS9.0之后的版本
```

####4. 常见问题总结
```objc
    单次定位在模拟器上测试不出效果?
    答: 因为模拟器的位置是固定的, 所以无法测试出效果, 请使用真机进行测试.
```