#Chapter-20（I）蓝牙交互CoreBlueTooth

---
```objc
Update更新：2016年5月20日 By {MISSAJJ琴瑟静听}
```
 
###CoreBlueTooth简介：
* 可用于第三方蓝牙设备交互，设备必须支持蓝牙4.0
* iPhone的设备必须是4S或者更新
* iPad设备必须是iPad mini或者更新
* iOS的系统必须是iOS 6或者更新
* 蓝牙4.0以`低功耗`著称，所以一般被称为BLE（bluetooth low energy）
* 使用模拟器调试
	- Xcode 4.6
	- iOS 6.1
* 应用场景
	+ 运动手环
	+ 智能家居 
	+ 拉卡拉蓝牙刷卡器

###核心概念
* CBCentralManager：中心设备（用来连接到外部设备的管家）
* CBPeripheralManager：外部设备（第三方的蓝牙4.0设备）

![image](BLE.jpeg)

###开发步骤
* 建立中心管家

```objc
// 1. 创建中心管家,并且设置代理
self.cmgr = [[CBCentralManager alloc]initWithDelegate:self queue:nil];
```
* 扫描外设（discover）

```objc
// 2. 在代理方法中扫描外部设备
 /**
  *  scanForPeripheralsWithServices ：如果传入指定的数组，那么就只会扫描数组中对应ID的设备
  *                                   如果传入nil，那么就是扫描所有可以发现的设备
  *  扫描完外部设备就会通知CBCentralManager的代理
  */
 - (void)centralManagerDidUpdateState:(CBCentralManager *)central
{
    if ([central state] == CBCentralManagerStatePoweredOn) {
        [self.cmgr scanForPeripheralsWithServices:nil options:nil];
    }
}
```

```objc
/**
 *  发现外部设备，每发现一个就会调用这个方法
 *  所以可以使用一个数组来存储每次扫描完成的数组
 */
- (void)centralManager:(CBCentralManager *)central didDiscoverPeripheral:(CBPeripheral *)peripheral advertisementData:(NSDictionary<NSString *,id> *)advertisementData RSSI:(NSNumber *)RSSI
{
    // 有可能会导致重复添加扫描到的外设
    // 所以需要先判断数组中是否包含这个外设
    if(![self.peripherals containsObject:peripheral]){
        [self.peripherals addObject:peripheral];
    }
}
```

* 连接外设

```objc
/**
 *  模拟开始连接方法
 */
- (void)start
{
    // 3. 连接外设
    for (CBPeripheral *ppl in self.peripherals) {
        // 扫描外设的服务
        // 这个操作应该交给外设的代理方法来做
        // 设置代理
        ppl.delegate = self;
        [self.cmgr connectPeripheral:ppl options:nil];
    }
}
```

* 扫描外设中的服务和特征
	- 服务和特征的关系
	
		`每个蓝牙4.0的设备都是通过服务和特征来展示自己的，一个设备必然包含一个或多个服务，每个服务下面又包含若干个特征。`

```objc
/**
 *  连接外设成功调用
 */
- (void)centralManager:(CBCentralManager *)central didConnectPeripheral:(CBPeripheral *)peripheral
{
    // 查找外设服务
    [peripheral discoverServices:nil];
}
```

```objc
/**
 *  发现服务就会调用代理方法
 *
 *  @param peripheral 外设
 */
- (void)peripheral:(CBPeripheral *)peripheral didDiscoverServices:(NSError *)error
{
    // 扫描到设备的所有服务
    NSArray *services = peripheral.services;
    // 根据服务再次扫描每个服务对应的特征
    for (CBService *ses in services) {
        [peripheral discoverCharacteristics:nil forService:ses];
    }
}
```


* 与外设做数据交互
	- 在指定的特征下做相应的操作

```objc
/**
 *  发现服务对应的特征
 */
- (void)peripheral:(CBPeripheral *)peripheral didDiscoverCharacteristicsForService:(CBService *)service error:(NSError *)error
{
    // 服务对应的特征
    NSArray *ctcs = service.characteristics;
    // 遍历所有的特征
    for (CBCharacteristic *character in ctcs) {
        // 根据特征的唯一标示过滤
        if ([character.UUID.UUIDString isEqualToString:@"XMG"]) {
            NSLog(@"可以吃饭了");
        }
    }
}
```
* 断开连接

```objc
/**
 *  断开连接
 */
- (void)stop
{
    // 断开所有连接上的外设
    for (CBPeripheral *per in self.peripherals) {
        [self.cmgr cancelPeripheralConnection:per];
    }
}
```