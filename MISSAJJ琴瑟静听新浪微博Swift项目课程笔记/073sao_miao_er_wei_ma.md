#  扫描二维码

---
```objc
Update更新：2016年6月3日 By {MISSAJJ琴瑟静听} 
```
##扫描二维码的支持框架AVFoundation

- 导入框架头文件

```swift
import AVFoundation

```
##懒加载对象
```swift
 // MARK: - 懒加载
    /// 输入对象
    private lazy var input: AVCaptureDeviceInput? = {
        let device = AVCaptureDevice.defaultDeviceWithMediaType(AVMediaTypeVideo)
        return try? AVCaptureDeviceInput(device: device)
    }()
    
    /// 会话
    private lazy var session: AVCaptureSession = AVCaptureSession()
    
    /// 输出对象
    private lazy var output: AVCaptureMetadataOutput = AVCaptureMetadataOutput()
    
    /// 预览图层
    private lazy var previewLayer: AVCaptureVideoPreviewLayer = AVCaptureVideoPreviewLayer(session: self.session)
}

```

##开始扫描二维码
 
```swift
 // MARK: - 内部控制方法
    private func scanQRCode()
    {
        // 1.判断输入能否添加到会话中
        if !session.canAddInput(input)
        {
            return
        }
        // 2.判断输出能够添加到会话中
        if !session.canAddOutput(output)
        {
            return
        }
        // 3.添加输入和输出到会话中
        session.addInput(input)
        session.addOutput(output)
        
        // 4.设置输出能够解析的数据类型
        // 注意点: 设置数据类型一定要在输出对象添加到会话之后才能设置
        output.metadataObjectTypes = output.availableMetadataObjectTypes
        
        // 5.设置监听:监听输出解析到的数据
        output.setMetadataObjectsDelegate(self, queue: dispatch_get_main_queue())
        
        // 6.添加预览图层
        view.layer.insertSublayer(previewLayer, atIndex: 0)
        previewLayer.frame = view.bounds
        
        // 7.开始扫描
        session.startRunning()
        
    }


```

##实现代理方法

```swift
extension QRCodeViewController: AVCaptureMetadataOutputObjectsDelegate
{
    /// 只要扫描到结果就会调用
    func captureOutput(captureOutput: AVCaptureOutput!, didOutputMetadataObjects metadataObjects: [AnyObject]!, fromConnection connection: AVCaptureConnection!)
    {
        customLabel.text =  metadataObjects.last?.stringValue
    }
}
```