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

