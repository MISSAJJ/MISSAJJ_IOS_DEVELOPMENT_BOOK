#  扫描相册二维码

---
```objc
Update更新：2016年6月3日 By {MISSAJJ琴瑟静听} 
```
##打开相册

```swift
    @IBAction func photoBtnClick(sender: AnyObject) {
        // 打开相册
        // 1.判断是否能够打开相册
        /*
        case PhotoLibrary  相册
        case Camera 相机
        case SavedPhotosAlbum 图片库
        */
        if !UIImagePickerController.isSourceTypeAvailable(UIImagePickerControllerSourceType.PhotoLibrary)
        {
            return
        }
        
        // 2.创建相册控制器
        let imagePickerVC = UIImagePickerController()
        
        imagePickerVC.delegate = self
        // 3.弹出相册控制器
        presentViewController(imagePickerVC, animated: true, completion: nil)
        
    }
    
```


##通过代理方法取出扫描数据

```swift
extension QRCodeViewController: UINavigationControllerDelegate, UIImagePickerControllerDelegate
{
    // 过时
//    func imagePickerController(picker: UIImagePickerController, didFinishPickingImage image: UIImage, editingInfo: [String : AnyObject]?) {
//        
//    }
    
    func imagePickerController(picker: UIImagePickerController, didFinishPickingMediaWithInfo info: [String : AnyObject]) {
//        NJLog(info)
        
        // 1.取出选中的图片
        guard let image = info[UIImagePickerControllerOriginalImage] as? UIImage else
        {
            return
        }
        
        guard let ciImage = CIImage(image: image) else
        {
            return
        }
        
        // 2.从选中的图片中读取二维码数据
        // 2.1创建一个探测器
        let detector = CIDetector(ofType: CIDetectorTypeQRCode, context: nil, options: [CIDetectorAccuracy: CIDetectorAccuracyLow])
        // 2.2利用探测器探测数据
        let results = detector.featuresInImage(ciImage)
        // 2.3取出探测到的数据
        for result in results
        {
           //取出数据后,是网页的话再作webview跳转功能
            NJLog((result as! CIQRCodeFeature).messageString)
        }
        
        // 注意: 如果实现了该方法, 当选中一张图片时系统就不会自动关闭相册控制器
        picker.dismissViewControllerAnimated(true, completion: nil)
    }
}

```