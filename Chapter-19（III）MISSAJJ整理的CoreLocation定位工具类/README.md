#Chapter-19（III）MISSAJJ整理的CoreLocation定位工具类

```
//
//  MALocationTool.h
//
//
//  Created by MISSAJJ on 16/1/27.
//  Copyright © 2016年 MISSAJJ. All rights reserved.
//
/**
 *  @author https://github.com/MISSAJJ (MISSAJJ), 更新日期: 16-05-20 11:05:38
 *
 *  MALocationTool代码优化：
 1，优化性能：改写懒加载
 2，增加block功能拓展返回传递地理位置数据
 
 // 获取Block当前位置
 
 使用方法：
 [[MALocationTool sharedMALocationTool] getCurrentLocation:^(CLLocation *currentLoc, CLPlacemark *placemark, NSString *error) {
 if ([error length] == 0) {
 NSLog(@"%@   ----   %@", currentLoc, placemark.name);// 位置名
 
 // NSLog(@"thoroughfare,%@",placemark.name.thoroughfare);       // 街道
 // NSLog(@"subThoroughfare,%@",placemark.name.subThoroughfare); // 子街道
 //NSLog(@"locality,%@",placemark.name.locality);               // 市
 // NSLog(@"subLocality,%@",placemark.name.subLocality);         // 区
 //NSLog(@"country,%@",placemark.name.country);                 // 国家
 }
 
 }
 }];
 
 */
#import <Foundation/Foundation.h>
#import "MASingleton.h"
#import <CoreLocation/CoreLocation.h>

//Block申明
typedef void(^ResultBlock)(CLLocation *currentLoc, CLPlacemark *placemark, NSString *error);
 
@interface MALocationTool : NSObject
// 单例声明
singleton_interface(MALocationTool);

@property (nonatomic,assign)double longitude;//经度
@property (nonatomic,assign)double latitude; //纬度


/**
 *  获取当前位置
 *
 *  @param block 获取当前位置后处理的block
 */
- (void)getCurrentLocation:(ResultBlock)block;



//开启定位更新坐标
-(void)updatingLocation;

//关闭定位
-(void)stopUpdatingLocation;

//显示位置授权alert
-(void)showLocationAlert;

//用户对于位置服务的设置状态
-(BOOL)locationServicesEnabled;
@end
```

```
//
//  MALocationTool.m
//
//
//  Created by MISSAJJ on 16/1/27.
//  Copyright © 2016年 MISSAJJ. All rights reserved.
//
/**
 *  @author https://github.com/MISSAJJ (MISSAJJ), 更新日期: 16-05-20 11:05:38
 *
 *  MALocationTool代码优化：
 1，优化性能：改写懒加载
 2，增加block功能拓展返回传递地理位置数据
 
 // 获取Block当前位置
 
 使用方法：
 [[MALocationTool sharedMALocationTool] getCurrentLocation:^(CLLocation *currentLoc, CLPlacemark *placemark, NSString *error) {
 if ([error length] == 0) {
 NSLog(@"%@   ----   %@", currentLoc, placemark.name);// 位置名
 
 // NSLog(@"thoroughfare,%@",placemark.name.thoroughfare);       // 街道
 // NSLog(@"subThoroughfare,%@",placemark.name.subThoroughfare); // 子街道
 //NSLog(@"locality,%@",placemark.name.locality);               // 市
 // NSLog(@"subLocality,%@",placemark.name.subLocality);         // 区
 //NSLog(@"country,%@",placemark.name.country);                 // 国家
 }
 
 
 }
 }];
 
 */


//用法： if(isIOS(8.0)) 表示用户IOS系统大于等于8.0
#define isIOS(version) ([[UIDevice currentDevice].systemVersion floatValue] >= version)


#import "MALocationTool.h"
#import <CoreLocation/CoreLocation.h>

@interface MALocationTool()<CLLocationManagerDelegate, UIAlertViewDelegate >

// 位置管理者
@property (nonatomic, strong) CLLocationManager *locationM;
// geo编码
@property (nonatomic, strong) CLGeocoder *geoCoder;
// 存储外界传递的代码块
@property (nonatomic, copy) ResultBlock resultBlock;

@end
@implementation MALocationTool





singleton_implementation(MALocationTool)

#pragma mark -懒加载
-(CLLocationManager *)locationM
{
    if (!_locationM) {
        //1 创建位置管理者
        _locationM = [[CLLocationManager alloc] init];
        
        //**-------ios8.0+定位适配---------- */
        if(isIOS(8.0)) {
            
            /* 请求前台定位授权
             默认情况下, 只能在前台获取用户位置
             如果想要获取后台位置, 需要勾选后台模式 location updates , 但是会出现蓝条
             [_locationM requestWhenInUseAuthorization];
             
             请求前后台定位授权
             默认在前后台都可以获取用户位置信息, 无论是否勾选后台模式locaiton updates, 而且不会出现蓝条
             如果当前的授权状态!=用户为选择状态, 那么这个方法不会有效
             */
            // 获取info.plist文件信息
            NSDictionary *dic = [[NSBundle mainBundle] infoDictionary];
            [dic enumerateKeysAndObjectsUsingBlock:^(id key, id obj, BOOL *stop) {
                //NSLog(@"%@---%@", key, obj);
            }];
            // 获取前后台定位描述
            NSString *alwaysStr = [dic valueForKey:@"NSLocationAlwaysUsageDescription"];
            // 获取前台定位描述
            NSString *whenInUseStr = [dic valueForKey:@"NSLocationWhenInUseUsageDescription"];
            
            if ([alwaysStr length] > 0)
            {
                [_locationM requestAlwaysAuthorization]; // 永久授权
            }
            else if([whenInUseStr length] > 0)
            {
                [_locationM requestWhenInUseAuthorization]; //使用中授权
            }else
            {
                NSLog(@"在iOS8.0之后定位,请在info.plist文件中配置NSLocationAlwaysUsageDescription 或者 NSLocationWhenInUseUsageDescription ");
            }
            
            // 判断iOS9.0 兼容iOS9.0前台授权模式下的后台获取位置(会出现蓝条)
            if (isIOS(9.0)) {
                // 获取后台模式数组
                NSArray *backModes = [dic valueForKey:@"UIBackgroundModes"];
                // 判断后台模式中是否包含位置更新服务
                if ([backModes containsObject:@"location"])
                {
                    _locationM.allowsBackgroundLocationUpdates = YES;
                }
            }
            
        }

        // 1.1 告诉外界位置的方案: 代理, block 通知
        _locationM.delegate = self;
        
        // 设置每隔多远定位一次(1次  111km/100m)
        // 最新的位置距离上一次位置之间的距离大于100m, 才会通过代理告诉外界
        _locationM.distanceFilter = 100.f;
        
       
        _locationM.desiredAccuracy = kCLLocationAccuracyBest;
        /*
         kCLLocationAccuracyBestForNavigation // 最适合导航
         kCLLocationAccuracyBest; // 最好的
         kCLLocationAccuracyNearestTenMeters; // 附近10米
         kCLLocationAccuracyHundredMeters; // 100米
         kCLLocationAccuracyKilometer; // 1000米
         kCLLocationAccuracyThreeKilometers; // 3000米
         定位精确度
         定位精确度越高, 越耗电, 而且, 定位时间越长
         */
        
        
               // 其它适配方案
        //        if([_locationM respondsToSelector:@selector(requestAlwaysAuthorization)])
        //        {
        //            [_locationM requestAlwaysAuthorization];
        //        }
        
    }
    return _locationM;
}

-(CLGeocoder *)geoCoder
{
    if (_geoCoder == nil) {
        _geoCoder = [[CLGeocoder alloc] init];
    }
    return _geoCoder;
}



- (void)getCurrentLocation:(ResultBlock)block
{
    // 记录代码块
    self.resultBlock = block;
    
    
    // 判断是否开启定位服务
    if ([CLLocationManager locationServicesEnabled]) {
        // 开始更新用户位置
        [self.locationM startUpdatingLocation];
    }
    else
    {
        self.resultBlock(nil, nil, @"定位服务未开启");
    }
}



//开启定位
-(void)updatingLocation{
    
    /*
     使用位置管理者, 开始获取用户位置
     开发经验: start 开始某个服务  stop 停止某个服务
     一旦调用了这个方法, 那么就会不断的获取用户位置信息, 然后告诉外界
     默认情况,只能在前台获取用户位置信息, 如果我们想要在后台获取位置, 必须勾选后台模式 location updates
     标准定位服务(基于gps/wifi/基站)
    
     监听重大位置的改变(基于基站进行定位 , 要求, 设备必须有电话模块)
     [self.locationM startMonitoringSignificantLocationChanges];
    */
    
    [self.locationM startUpdatingLocation];
}

//关闭定位
-(void)stopUpdatingLocation{
    
    [self.locationM stopUpdatingLocation];
}

#pragma mark -CLLocationManagerDelegate
/**
 *  获取到用户位置之后调用
 *
 *  @param manager   位置管理者
 *  @param locations 位置数组
 */
-(void)locationManager:(CLLocationManager *)manager didUpdateLocations:(NSArray<CLLocation *> *)locations

{
    
    //NSLog(@"定位到了%@",locations);
    
    CLLocation *currLocation = [locations lastObject];
    CLLocationCoordinate2D currCoordinate = currLocation.coordinate;
    //NSLog(@"经度：%.15f,纬度：%.15f",newCoordinate.longitude,newCoordinate.latitude);
    
    _longitude = currCoordinate.longitude;
    _latitude = currCoordinate.latitude;
    NSLog(@"self.longitude经度：%.15f,self.latitude纬度：%.15f",_longitude,_latitude);
  
    // 获取到位置信息后,再进行地理编码
    [self.geoCoder reverseGeocodeLocation:[locations lastObject] completionHandler:^(NSArray *placemarks, NSError *error) {
        
        if (self.resultBlock) {
            self.resultBlock([locations lastObject], [placemarks firstObject], nil);
        }
        
        for (CLPlacemark * place in placemarks) {
            NSLog(@"name,%@",place.name);                       // 位置名
            // NSLog(@"thoroughfare,%@",place.thoroughfare);       // 街道
            // NSLog(@"subThoroughfare,%@",place.subThoroughfare); // 子街道
            //NSLog(@"locality,%@",place.locality);               // 市
            // NSLog(@"subLocality,%@",place.subLocality);         // 区
            //NSLog(@"country,%@",place.country);                 // 国家
        }

       
    }];
    
    // 一般我们开发中, 获取到用户位置信息之后, 做一些业务逻辑操作
    // 针对于定位一次的情况, 可以在定位到之后 停止获取用户位置
   [manager stopUpdatingLocation];
    
    
    
    //保存最新用户经纬度
    // [self saveUserLocation];
    
    // 计算两个坐标距离
    //float distance = [currLocation distanceFromLocation:oldLocation];
    //NSLog(@"%f",distance);
    
    
}
// 定位失误时触发
- (void)locationManager:(CLLocationManager *)manager didFailWithError:(NSError *)error
{
    NSLog(@"定位失误时触发error:%@",error);
    
    [manager stopUpdatingLocation];
    
    //定位失败将经纬度清零，控制
    _longitude = 0;
    _latitude = 0;
}

//保存用户经纬度
-(void)saveUserLocation
{
    
    NSDate* now = [NSDate date];
    NSUserDefaults *location = [NSUserDefaults standardUserDefaults];
    [location setDouble:self.longitude forKey:LONGITUDE];
    [location setDouble:self.latitude forKey:LATITUDE];
    [location setObject:now forKey:SAVELOCATIONDATE];
    //Save version info.
    NSString* strVersion =[[[NSBundle mainBundle] infoDictionary] valueForKey:@"CFBundleShortVersionString"];
    [location setValue:strVersion forKey:APPVERSION];
    [location synchronize];
    // NSLog(@"self.longitude经度：%.15f,self.latitude纬度：%.15f",self.longitude,self.latitude);
    
    
}



// 如果授权状态发生变化时,调用
// status : 当前的授权状态
-(void)locationManager:(CLLocationManager *)manager didChangeAuthorizationStatus:(CLAuthorizationStatus)status
{
    switch (status) {
        case kCLAuthorizationStatusNotDetermined:
        {
            NSLog(@"用户未决定");
            break;
        }
        case kCLAuthorizationStatusRestricted:
        {
            NSLog(@"访问受限");
            //显示alert
            [self showLocationAlert];
            self.resultBlock(nil, nil, @"访问受限");
            break;
        }
            // 定位关闭时和对此APP授权为never时调用
        case kCLAuthorizationStatusDenied:
        {
            //显示alert
            [self showLocationAlert];
             
            // 判断当前设备是否支持定位, 并且定位服务是否开启()
            if([CLLocationManager locationServicesEnabled])
            {
                
                NSLog(@"定位开启,被拒绝");
                self.resultBlock(nil, nil, @"被拒绝");
            }else
            {
                NSLog(@"定位服务关闭，不可用");
            }
            break;
        }
            // 获取前后台定位授权
        case kCLAuthorizationStatusAuthorizedAlways:
        {
            //case kCLAuthorizationStatusAuthorized: // 失效，不建议使用
            NSLog(@"获取前后台定位授权kCLAuthorizationStatusAuthorizedAlways");
            break;
        }
        case kCLAuthorizationStatusAuthorizedWhenInUse:
        {
            NSLog(@"获取前台定位授权kCLAuthorizationStatusAuthorizedWhenInUse");
            break;
        }
        default:
            break;
    }
}





//用户对于位置服务的设置状态
-(BOOL)locationServicesEnabled{
    
    
    CLAuthorizationStatus status = [CLLocationManager authorizationStatus];
    // NSLog(@"=status====%d========",status);
    
    
    if (status == kCLAuthorizationStatusNotDetermined) { //用户未决定
        return NO;
    }
    
    if (status == kCLAuthorizationStatusRestricted) { //受限制
        return NO;
    }
    
    if (status == kCLAuthorizationStatusDenied) { //拒绝
        return NO;
    }
    if (![CLLocationManager locationServicesEnabled]) {
        return NO;
    }
    
    return YES;
}

-(void)alertView:(UIAlertView *)alertView clickedButtonAtIndex:(NSInteger)buttonIndex
{
    //跳转设置位置授权界面
    [self showLocationPermissionSetting];
    
}


#pragma mark===显示位置授权alert
-(void)showLocationAlert;
{
    
    UIAlertView *alvertView=[[UIAlertView alloc]initWithTitle:GetLocalizedString(@"appName",nil) message:GetLocalizedString(@"Location_Description",nil) delegate:nil cancelButtonTitle:GetLocalizedString(@"YES",nil) otherButtonTitles: nil];
    alvertView.delegate = self;
    [alvertView show];
}
#pragma mark===跳转设置位置授权界面
-(void)showLocationPermissionSetting;
{
    
    // iOS8.0+
    NSURL *url = [NSURL URLWithString:UIApplicationOpenSettingsURLString];
    if([[UIApplication sharedApplication] canOpenURL:url])
    {
        [[UIApplication sharedApplication] openURL:url];
    }
    
}

-(void)locationManagerDidPauseLocationUpdates:(CLLocationManager *)manager
{
    
    NSLog(@"locationManagerDidPauseLocationUpdates");
    
}

//-(void)startLocation{
//
//
//    //获得保存时间
//    NSDate * oldData = [[NSUserDefaults standardUserDefaults] objectForKey:SAVELOCATIONDATE];
//
//
//    //如果有存档日期
//    if (oldData){
//
//
//        //如果存档的日期超过15分钟
//        if ([[NSDate date] timeIntervalSince1970] - [oldData timeIntervalSince1970] >  NEWS_MIN_DURATION )
//        {
//            // 使用位置管理者, 开始获取用户位置
//            // 开发经验: start 开始某个服务  stop 停止某个服务
//            // 一旦调用了这个方法, 那么就会不断的获取用户位置信息, 然后告诉外界
//            // 默认情况,只能在前台获取用户位置信息, 如果我们想要在后台获取位置, 必须勾选后台模式 location updates
//            // 标准定位服务(基于gps/wifi/基站)
//            [_locationM startUpdatingLocation];
//
//            // 监听重大位置的改变(基于基站进行定位 , 要求, 设备必须有电话模块)
//            // [self.locationM startMonitoringSignificantLocationChanges];
//
//        }else{
//            NSLog(@"没有超过15分钟不更新");
//        }
//
//
//    } else { //从未存档过
//
//        [_locationM startUpdatingLocation];
//
//    }
//
//
//}


@end

```