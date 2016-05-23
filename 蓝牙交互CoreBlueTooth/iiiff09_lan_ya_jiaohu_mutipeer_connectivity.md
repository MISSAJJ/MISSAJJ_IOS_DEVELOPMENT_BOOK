# （III）蓝牙交互Mutipeer Connectivity
 

---
```objc
Update更新：2016年5月20日 By {MISSAJJ琴瑟静听}
```
 
##蓝牙MutipeerConnectivity
###简介
* iOS 7引入的一个全新框架
* 多点连接
* 替代GameKit框架
* 多用于文件的传输
* iOS设备不联网也能跟附近的人聊天
	* FireChat
	* See You Around
	* 以上近场聊天App都是基于mutipeerConnectivity框架
* 搜索和传输的方式
	* 双方WIFI和蓝牙都没有打开：无法实现
	* 双方都开启蓝牙：通过蓝牙发现和传输
	* 双方都开启WIFI：通过WIFI Direct发现和传输，速度接近AirDrop
	* 双方同时开启了WIFI和蓝牙：模拟AirDrop，通过低功耗蓝牙技术扫描发现握手，然后通过WIFI Direct传输 

###案例界面搭建
###连接设备
* 创建MCSession对象用于存放当前连接的会话

```objc
// 创建MCSession对象
// initWithPeer：设备的ID
// 用于存放当前的连接的会话
self.mc_Session = [ [MCSession alloc]initWithPeer:[[MCPeerID alloc] initWithDisplayName:[UIDevice currentDevice].name]];
```

* 开启广播对象，通知正在搜索的设备他们是可用的

```objc
/**
 *   只要开启了可被搜索的广播，那么连接蓝牙按钮就不可点
 */
- (IBAction)foundConnect:(id)sender {
    UISwitch *switchBtn = (UISwitch *)sender;
    if (switchBtn.isOn) {
        // 广播对象，告诉其它的设备他们是可用的
        if (self.advertiserAssistant == nil) {
            // 创建广播对象
            //  initWithServiceType： 广播类型的标示（因为广播可能比较多，所以最好每个广播绑定一个唯一标示）
            //  session：当前会话
            //  discoveryInfo： 广播信息
            self.advertiserAssistant = [[MCAdvertiserAssistant alloc] initWithServiceType:SERVICE_TYPE discoveryInfo:nil session:self.mc_Session];
        }
        // 开启广播
        [self.advertiserAssistant start];
        self.connectBT.enabled = NO;
    }else{
        self.connectBT.enabled = YES;
    }
    
}
```

* 开始搜索蓝牙设备

```objc
- (IBAction)connectBlueTooth {
    // 创建搜索蓝牙设备控制器
    MCBrowserViewController *mbVC = [[MCBrowserViewController alloc]initWithServiceType:SERVICE_TYPE session:self.mc_Session];
    // 设置控制器代理
    mbVC.delegate = self;
    // 跳转到搜索控制器
    [self presentViewController:mbVC animated:YES completion:nil];
}
```

###选择数据

```objc
- (IBAction)selectImage {
    // 1. 创建图片选择器
    UIImagePickerController *imgPicker = [[UIImagePickerController alloc]init];
    // 2. 判断图片选择器是否可用
    if ([UIImagePickerController isSourceTypeAvailable:UIImagePickerControllerSourceTypeSavedPhotosAlbum]) {
        // 选择器打开的类型
        imgPicker.sourceType = UIImagePickerControllerSourceTypeSavedPhotosAlbum;
        // 设置代理
        imgPicker.delegate = self;
        // 打开图片选择器
        [self presentViewController:imgPicker animated:YES completion:nil];
    }
}
```
* 图片选择器代理中获取数据

```objc
// 选择完毕调用
- (void)imagePickerController:(UIImagePickerController *)picker didFinishPickingMediaWithInfo:(NSDictionary<NSString *,id> *)info
{
//    NSLog(@"info == %@",info);
    // 设置显示图片
    self.showImage.image = info[UIImagePickerControllerOriginalImage];
    // 隐藏图片显示器
    [picker dismissViewControllerAnimated:YES completion:nil];
}
```
* 在搜索控制器的连接完成的代理方法中隐藏搜索控制器

```objc
/**
 *  连接完成
 *
 *  @param browserViewController 搜索控制器
 */
- (void)browserViewControllerDidFinish:(MCBrowserViewController *)browserViewController
{
    [browserViewController dismissViewControllerAnimated:YES completion:nil];
    NSLog(@"%s %d",__func__,__LINE__);
}
```

###发送数据
* 实现搜索控制器的代理方法，获取连接上的设备ID

```objc
#pragma mark - MCBrowserViewControllerDelegate
/**
 *  连接成功
 *
 *  @param browserViewController 搜索控制器
 *  @param peerID                连接上的设备ID
 *  @param info                  连接的信息
 *
 *  @return                      YES ： 只发送连接上的用户
 */
- (BOOL)browserViewController:(MCBrowserViewController *)browserViewController
      shouldPresentNearbyPeer:(MCPeerID *)peerID
            withDiscoveryInfo:(nullable NSDictionary<NSString *, NSString *> *)info
{
    self.peerID = peerID;
    NSLog(@"info == %@   peer = %@",info, peerID);
    return YES;
}
```

* 将图片发送给连接上的ID

```objc
- (IBAction)sendImage {
    // 获取图片
    UIImage *image = self.showImage.image;
    // 将图片转换成NSData类型
    NSData *data = UIImagePNGRepresentation(image);
    /**
     *  发送数据
     *  toPeers ： 发给的设备ID的数组
     *  withMode： 发送模式，是否是安全模式
     */
    if (self.peerID != nil) {
        [self.mc_Session sendData:data toPeers:@[self.peerID] withMode:MCSessionSendDataUnreliable error:nil];
    }
}
```

###接收数据
* 实现MCSession的代理方法接受数据

```objc
- (void)session:(MCSession *)session didReceiveData:(NSData *)data fromPeer:(MCPeerID *)peerID
{
    NSLog(@"%s %d",__func__,__LINE__);
    NSLog(@"data = %@ , peer = %@",data,peerID);
}
```

###显示数据
* 在MCSeesion代理方法中设置获取过来的数据

```objc
// 接收的数据
- (void)session:(MCSession *)session didReceiveData:(NSData *)data fromPeer:(MCPeerID *)peerID
{
    NSLog(@"%s %d",__func__,__LINE__);
//    NSLog(@"data = %@ , peer = %@",data,peerID);
    UIImage *image = [[UIImage alloc]initWithData:data];
    if(image != nil){
        // 设置数据
        dispatch_async(dispatch_get_main_queue(), ^{
            self.showImage.image = image;
        });
    }
}
```
