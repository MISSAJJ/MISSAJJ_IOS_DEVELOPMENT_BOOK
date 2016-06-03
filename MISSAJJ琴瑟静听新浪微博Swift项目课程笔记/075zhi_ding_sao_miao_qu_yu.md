#  指定扫描区域

##调整懒加载输出对象代码

```swift
/// 输出对象
    private lazy var output: AVCaptureMetadataOutput = {
       let out = AVCaptureMetadataOutput()
        // 设置输出对象解析数据时感兴趣的范围
        // 默认值是 CGRect(x: 0, y: 0, width: 1, height: 1)
        // 通过对这个值的观察, 我们发现传入的是比例
        // 注意: 参照是以横屏的左上角作为, 而不是以竖屏
//        out.rectOfInterest = CGRect(x: 0, y: 0, width: 0.5, height: 0.5)
        
        // 1.获取屏幕的frame
        let viewRect = self.view.frame
        // 2.获取扫描容器的frame
        let containerRect = self.customContainerView.frame
        let x = containerRect.origin.y / viewRect.height;
        let y = containerRect.origin.x / viewRect.width;
        let width = containerRect.height / viewRect.height;
        let height = containerRect.width / viewRect.width;
        
         out.rectOfInterest = CGRect(x: x, y: y, width: width, height: height)
        
        return out
    }()


```

##先清除定位图层,再生成定位图层
```swift
  extension QRCodeViewController: AVCaptureMetadataOutputObjectsDelegate
{
    /// 只要扫描到结果就会调用
    func captureOutput(captureOutput: AVCaptureOutput!, didOutputMetadataObjects metadataObjects: [AnyObject]!, fromConnection connection: AVCaptureConnection!)
    {
        // 1.显示结果
        customLabel.text =  metadataObjects.last?.stringValue
        
        clearLayers()
        
        // 2.拿到扫描到的数据
        guard let metadata = metadataObjects.last as? AVMetadataObject else
        {
            return
        }
        // 通过预览图层将corners值转换为我们能识别的类型
        let objc = previewLayer.transformedMetadataObjectForMetadataObject(metadata)
        // 2.对扫描到的二维码进行描边
        drawLines(objc as! AVMetadataMachineReadableCodeObject)
    }
    
    /// 绘制描边
    private func drawLines(objc: AVMetadataMachineReadableCodeObject)
    {
        
        // 0.安全校验
        guard let array = objc.corners else
        {
            return
        }
        
        // 1.创建图层, 用于保存绘制的矩形
        let layer = CAShapeLayer()
        layer.lineWidth = 2
        layer.strokeColor = UIColor.greenColor().CGColor
        layer.fillColor = UIColor.clearColor().CGColor
        
        // 2.创建UIBezierPath, 绘制矩形
        let path = UIBezierPath()
        var point = CGPointZero
        var index = 0
        CGPointMakeWithDictionaryRepresentation((array[index++] as! CFDictionary), &point)
        
        // 2.1将起点移动到某一个点
        path.moveToPoint(point)
        
        // 2.2连接其它线段
        while index < array.count
        {
            CGPointMakeWithDictionaryRepresentation((array[index++] as! CFDictionary), &point)
            path.addLineToPoint(point)
        }
        // 2.3关闭路径
        path.closePath()
        
        layer.path = path.CGPath
        // 3.将用于保存矩形的图层添加到界面上
        containerLayer.addSublayer(layer)
    }
    
    /// 清空描边
    private func clearLayers()
    {
        guard let subLayers = containerLayer.sublayers else
        {
            return
        }
        for layer in subLayers
        {
            layer.removeFromSuperlayer()
        }
    }
}  
```
