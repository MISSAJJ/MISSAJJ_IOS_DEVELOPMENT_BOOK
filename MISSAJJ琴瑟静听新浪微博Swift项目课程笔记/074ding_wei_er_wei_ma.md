# 定位二维码

---
```objc
Update更新：2016年6月3日 By {MISSAJJ琴瑟静听} 
```
##懒加载-保存描边的图层
```swift
  /// 懒加载: 专门用于保存描边的图层
    private lazy var containerLayer: CALayer = CALayer()
```
##添加容器图层
```swift 
       // 7.添加容器图层
        view.layer.addSublayer(containerLayer)
        containerLayer.frame = view.bounds

```
##调整二维码代理方法
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
        // 转换前: corners { 0.3,0.7 0.5,0.7 0.5,0.4 0.3,0.4 }
        // 转换后: corners { 40.0,230.3 30.9,403.9 216.5,416.3 227.1,244.2 }
        // 通过预览图层将corners值转换为我们能识别的类型
        let objc = previewLayer.transformedMetadataObjectForMetadataObject(metadata)
        
//        NJLog((objc as! AVMetadataMachineReadableCodeObject).corners)
        
        // 2.对扫描到的二维码进行描边
        drawLines(objc as! AVMetadataMachineReadableCodeObject)
    }
```
##绘制定位描边图层
```swift    
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
//        index++
        
        // 2.1将起点移动到某一个点
        path.moveToPoint(point)
        
        // 2.2连接其它线段
        while index < array.count
        {
            CGPointMakeWithDictionaryRepresentation((array[index++] as! CFDictionary), &point)
//            index++
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