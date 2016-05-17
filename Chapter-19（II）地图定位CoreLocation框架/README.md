#Chapter-19（II）地图定位CoreLocation框架

---
```objc
Update更新：2016年5月17日 By {MISSAJJ琴瑟静听}
由于此章节笔记内容比较多，所以特地分了两篇文章，
避免因文章太长而导致手机浏览器或微信内浏览崩溃
```

###四. CLLocation对象详解
####1. 属性解释
```
    > coordinate    : 当前位置所在的经纬度数据
    > altitude      : 海拔
    > speed         : 当前速度
    > course        : 航向(设备移动的方向, 值域范围:0.0 ~ 259.9, 正北方向为0.0)
```

####2. 重要方法
```
    代码: - (CLLocationDistance)distanceFromLocation:(CLLocation *)location
    作用: 计算两个位置对象之间的物理距离, 单位是(米)
```
####3. 场景演练
```
1. 场景演示:打印当前用户的行走方向,偏离角度以及对应的行走距离,
    例如:”北偏东30度方向,移动了8米”
2. 实现步骤: 
    1> 获取对应的方向偏向(例如”正东”,”东偏南”)
    2> 获取对应的偏离角度(并判断是否是正方向)
    3> 计算行走距离
    4> 打印信息
```
####4. 注意事项
```
    使用位置前, 务必判断当前获取的位置是否有效

     代码: if (location.horizontalAccuracy < 0) return;
     功能: 如果水平精确度小于零, 代表虽然可以获取位置对象, 但是数据错误, 不可用 
```

###五、经验小结


####一. 定位的应用场景
```
    1) 导航 
    2) 电商APP,获取用户所在城市(需要与(反)地理编码联合使用) 
    3) 数据采集用户信息(例如,统计app使用分布)
    4) 查找周边(周边好友, 周边商家等等)
 ```   
####二. 开发经验
```
由于定位非常耗电; 所以为了给用户省电, 你可以遵守以下小经验

    1）不需要获取用户位置时,一定要关闭定位服务：
    2）如果能满足项目需求,尽可能的使用”监听显著位置变化”的定位服务(打车app)
    3）如果可以,尽可能使用低精度的desiredAccuracy
    4）如果是数据采集,(一般都是周期性的去轮询用户位置),在轮询期间一定要关闭定位
```
###五. 指南针效果实现
####1. 实现思路
```
    利用"磁力计"传感器,获取设备朝向
    根据设备朝向反向旋转"指南针"图片
```
####2. 代码实现

- 获取设备朝向
```
     1) 导入CoreLocation框架以及对应的主头文件
         #import <CoreLocation/CoreLocation.h>

     2) 创建CLLocationManager对象并设置代理
         self.locationM = [[CLLocationManager alloc] init];
         self.locationM.delegate = self;

     3) 调用方法, 开始获取设备朝向
         [self.locationM startUpdatingHeading];

     4) 在对应的代理方法中获取设备朝向信息
         -(void)locationManager:(CLLocationManager *)manager didUpdateHeading:(CLHeading *)newHeading
         {
             // 旋转图片代码
         }
```

- 旋转图片
- 
```
     // 1.判断当前的角度是否有效(如果此值小于0,代表角度无效)
     if(newHeading.headingAccuracy < 0)
     return;

     // 2.获取当前设备朝向(磁北方向)
     CGFloat angle = newHeading.magneticHeading;

     // 3.转换成为弧度
     CGFloat radian = angle / 180.0 * M_PI;

     // 4.带动画反向旋转指南针
     [UIView animateWithDuration:0.5 animations:^{
         self.compassView.transform = CGAffineTransformMakeRotation(-radian);
     }];
```

####3. 概念补充
```
    磁北角度: newHeading.magneticHeading   ------- 相对于"磁北方向"产生的角度
    真北角度: newHeading.trueHeading           ------- 相对于"真北方向"产生的角度
```
####4. 注意事项

```
  1. 获取设备朝向前, 先判断"磁力计"是否可用
        [CLLocationManager headingAvailable];

    2. 获取朝向前, 判断当前朝向信息是否有效
        if(newHeading.headingAccuracy < 0) return;

    3. 注意与"航向"的区别
        设备朝向是指手机的朝向; "航向"可以理解为设备的移动方向

    4. 使用"磁力计"传感器获取设备朝向, 不需要请求用户授权
        因为设备朝向不涉及用户隐私
```

####5.测试环境

```
    1. XCode版本无要求(建议:XCode7.0不需要开发者账号也可以进行真机调试)
    2. 必须要求真机设备(只有真机设备才有"磁力计"传感器) 
```

###六. 区域监听
####1.概念解释
```
    区   域 :  就是指划定的一块地域范围(比如圆形区域, 则由区域中心, 和半径组成)
    区域监听 : 是指,我们通过代码指定一个区域, 然后当用户持握设备进入或者离开指定区域, 我们都能监听到.
```
####2. 监听指定区域
 
- 导入CoreLocation框架以及对应的主头文件
```
     #import <CoreLocation/CoreLocation.h>
```

- 创建CLLocationManager对象并设置代理
```
     self.locationM = [[CLLocationManager alloc] init];
     self.locationM.delegate = self;
```

- 请求前后台定位, 或前台定位授权, 并在Info.Plist文件中配置相应的Key
```
     [self.locationM requestAlwaysAuthorization];
     // [self.locationM requestWhenInUseAuthorization]; 
```

- 创建一个区域, 并开始监听
```
     // 1. 判断区域监听服务是否可用(定位服务是否关闭, 定位是否授权, 是否开启飞行模式)
     if ([CLLocationManager isMonitoringAvailableForClass:[CLCircularRegion class]]) 
     {

         // 创建区域中心
         CLLocationCoordinate2D center = CLLocationCoordinate2DMake(29.12345, 131.23456);

         // 创建区域（指定区域中心，和区域半径）
         CLLocationDistance radius = 1000;

         // 判断区域半径是否大于最大监听区域半径,如果大于, 就没法监听
         if (radius > self.locationM.maximumRegionMonitoringDistance) {
             radius = self.locationM.maximumRegionMonitoringDistance;
         }
         CLCircularRegion *region = [[CLCircularRegion alloc] initWithCenter:center radius:radius identifier:@"小码哥"];

         // 开始监听指定区域
         [self.locationM startMonitoringForRegion:region];
     }
     else
     {
         NSLog(@"区域监听不可用");
     }
```

- 在对应的代理方法中监听区域状态
```
     // 进去监听区域后调用（调用一次）
     -(void)locationManager:(nonnull CLLocationManager *)manager didEnterRegion:(nonnull CLRegion *)region
     {
         NSLog(@"进入区域---%@", region.identifier);
         [manager stopMonitoringForRegion:region];
     }

     // 离开监听区域后调用（调用一次）
     -(void)locationManager:(nonnull CLLocationManager *)manager didExitRegion:(nonnull CLRegion *)region
     {
         NSLog(@"离开区域---%@", region.identifier);
     }
```

####3. 获取某个区域的当前状态

监听某个区域时, 只有进入或者离开这个区域时, 才能回调对应的方法, 是一个进入或者离开的动作 如果想知道某一个区域的当前状态(识别用户是在区域内部, 还是区域外部), 则需要使用以下方法
```
代   码:
    [self.locationM requestStateForRegion:region];
回调代理: 
    // 请求某个区域状态时, 回调的代理方法
    -(void)locationManager:(CLLocationManager *)manager didDetermineState:(CLRegionState)state forRegion:(CLRegion *)region
{
    switch (state) {
        case CLRegionStateUnknown:
            NSLog(@"未知状态");
            break;
        case CLRegionStateInside:
            NSLog(@"在区域内部");
            break;
        case CLRegionStateOutside:
            NSLog(@"在区域外部");
            break;
        default:
         break;
    }
}
```
####4. 测试环境

XCode版本无要求
iOS模拟器版本无要求

####5. 注意事项
```
1. 想要做区域监听, 在iOS8.0之后, 必须请求位置授权
   代码: [self.locationM requestAlwaysAuthorization];
   原因: 区域监听的原理就是获取用户的位置, 然后在判断该位置是否在制定区域内, 所以会涉及到用户隐私(位置), 而在iOS8.0之后, 想要访问用户位置信息, 就需要主动请求授权;

2. 使用前, 先判断区域监听是否可用
   代码:  [CLLocationManager isMonitoringAvailableForClass:[CLCircularRegion class]]

3. 注意区域半径是否大于最大区域监听半径(如果大于, 则无法监听成功)
   代码: radius > self.locationM.maximumRegionMonitoringDistance
```

####6. 常见问题
```
1. 区域监听, 测试没有效果?
   首先, 确定代码没有问题, 是否有请求授权;
   其次, 尝试修改模拟器位置信息, 触发进入区域或离开区域的动作
   第三, 如果模拟器出现BUG, 定位不到, 也会无法判定当前区域状态; 所以, 最后可以尝试重置模拟器.
```

###七. (反)地理编码
####1. 概念解释
```
地理编码:   是指根据地址关键字, 将其转换成为对应的经纬度等信息;
发地理编码:    是指根据经纬度信息, 将其转换成为对应的省市区街道等信息;
```
####2. 地理编码

- 导入CoreLocation框架以及对应的主头文件

```
     #import <CoreLocation/CoreLocation.h>
```

- 创建CLGeocoder对象
```
     self.geoC = [[CLGeocoder alloc] init];
```

- 根据地址关键字, 进行地理编码
```
     // 直接根据地址进行地理编码（返回结果可能有多个，因为一个地点有重名）
     [self.geoC geocodeAddressString:@"广州" completionHandler:^(NSArray<CLPlacemark *> * __nullable placemarks, NSError * __nullable error) 
     {
         // 包含区，街道等信息的地标对象
         CLPlacemark *placemark = [placemarks firstObject];
         // 城市名称
         NSString *city = placemark.locality;
         // 街道名称
         NSString *street = placemark.thoroughfare;
         // 全称
         NSString *name = placemark.name;
     }]; 
```

####3. 反地理编码

- 导入CoreLocation框架以及对应的主头文件
```
     #import <CoreLocation/CoreLocation.h>
```

- 创建CLGeocoder对象
```
     self.geoC = [[CLGeocoder alloc] init];
```

- 根据经纬度信息, 进行反地理编码
```
     // 根据经纬度信息进行反地理编码
     [self.geoC reverseGeocodeLocation:[[CLLocation alloc] initWithLatitude:21.123 longitude:123.345] completionHandler:^(NSArray<CLPlacemark *> * __nullable placemarks, NSError * __nullable error) 
     {
         // 包含区，街道等信息的地标对象
         CLPlacemark *placemark = [placemarks firstObject];
         // 城市名称
         NSString *city = placemark.locality;
         // 街道名称
         NSString *street = placemark.thoroughfare;
         // 全称
         NSString *name = placemark.name;
     }]; 
```

####4. CLPlacemark 地标对象详解
```
    location            : CLLocation 类型, 位置对象信息, 里面包含经纬度, 海拔等等
    region              : CLRegion 类型, 地标对象对应的区域
    addressDictionary  : NSDictionary 类型, 存放街道,省市等信息
    name                : NSString 类型, 地址全称
    thoroughfare        : NSString 类型, 街道名称
    locality            : NSString 类型, 城市名称
    administrativeArea : NSString 类型, 省名称
    country             : NSString 类型, 国家名称
```

- 5. 测试环境
```
*   必须联网
    XCode版本不限
    iOS模拟器系统版本不限
```
- 6. 常见问题
```
   测试无数据?
   首先, 检查是否有联网;
   其次, 如果是反地理编码,可尝试更换经纬度再次尝试, 有的经纬度没有对应信息
```
- 7. 应用场景
```
一般与定位结合使用, 确定当前位置的具体地理信息
```
###八. 使用第三方框架进行定位
####1. 主要原因
```
    因为使用CoreLocation框架进行获取用户位置信息, 是通过代理进行回调; 而第三方框架将"代理模拟"转换成为"block模式"; 使用起来比较方便, 而且额外增加了超时时间等功能.
```
####2. 框架信息
```
    名称: locationManager
    地址: [link](https://github.com/intuit/LocationManager)
```
####3. 使用方法

```
参照该框架对应的 readME
```
####4. 注意事项
```
    一般集成第三方框架到项目中, 请先确保该框架没有问题, 然后再向项目中集成
```
###九. 补充
- 代理模式到block模式的转换
```
     主要思想就是,先记录下外界传递过来的block, 然后在对应的代理方法里面执行这个block;
```