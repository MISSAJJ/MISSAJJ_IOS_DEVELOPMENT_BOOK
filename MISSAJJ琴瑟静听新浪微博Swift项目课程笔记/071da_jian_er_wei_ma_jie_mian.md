#  搭建二维码界面

---
```objc
Update更新：2016年6月3日 By {MISSAJJ琴瑟静听} 
```
##扫描二维码界面图

![Images](images/二维码界面需求图.png)


##storyboard搭建二维码界面
- 创建QRCode.storyboard 和 QRCodeViewController.swift

 
![image](images/storyboard搭建二维码界面.png)
 

```swift 
//  QRCodeViewController.swift 

import UIKit

class QRCodeViewController: UIViewController {
    @IBOutlet weak var customTabbar: UITabBar!

    override func viewDidLoad() {
        super.viewDidLoad()

        // 1.设置默认选中第一个二维码Tabbar
        customTabbar.selectedItem = customTabbar.items?.first
        
        //后期可以完善条形码的读取功能
    }

    @IBAction func photoBtnClick(sender: AnyObject) {
    }
    @IBAction func closeBtnClick(sender: AnyObject) {
        dismissViewControllerAnimated(true, completion: nil)
    }
}

```
##从主界面跳转到二维码界面

```swift
@objc private func rightBtnClick()
    {
        // 1.创建二维码控制器
        let sb = UIStoryboard(name: "QRCode", bundle: nil)
        let vc = sb.instantiateInitialViewController()!
        // 2.弹出二维码控制器
        presentViewController(vc, animated: true, completion: nil)
    }

```
