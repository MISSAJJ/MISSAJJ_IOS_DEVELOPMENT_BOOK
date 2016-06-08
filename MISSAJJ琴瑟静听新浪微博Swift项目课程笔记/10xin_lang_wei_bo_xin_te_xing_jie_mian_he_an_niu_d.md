# 新浪微博新特性界面和按钮动画
---
```objc
Update更新：2016年6月8日 By {MISSAJJ琴瑟静听} 
```

###新特性界面动画演示

![image](images/新特性界面.gif)



###新特性界面搭建

- Newfeature.storyboard
- 通过collectionView架构新特性界面

![image](images/新特性界面.png)




###新特性界面代码部分

- 创建NewfeatureViewController.swift

- 新特性界面的个数

```swift 
/// 新特性界面的个数
    private var maxCount = 4
```

- UICollectionView布局

```swift  
// MARK: - 自定义布局
class MANewfeatureLayout: UICollectionViewFlowLayout
{
    // 准备布局
    override func prepareLayout() {
        // 1.设置每个cell的尺寸
        itemSize = UIScreen.mainScreen().bounds.size
        // 2.设置cell之间的间隙
        minimumInteritemSpacing = 0
        minimumLineSpacing = 0
        // 3.设置滚动方向
        scrollDirection = UICollectionViewScrollDirection.Horizontal
        
        // 4.设置分页
        collectionView?.pagingEnabled = true
        // 5.禁用回弹
        collectionView?.bounces = false
        // 6.取出滚动条
        collectionView?.showsHorizontalScrollIndicator = false
        collectionView?.showsVerticalScrollIndicator = false
    }
}
```
- 设置UICollectionView数据源

```swift  
extension NewfeatureViewController: UICollectionViewDataSource
{
    
    // 1.告诉系统有多少组
    func numberOfSectionsInCollectionView(collectionView: UICollectionView) -> Int {
        return 1
    }
    // 2.告诉系统每组多少行
    func collectionView(collectionView: UICollectionView, numberOfItemsInSection section: Int) -> Int {
        return maxCount
    }
    // 3.告诉系统每行显示什么内容
    func collectionView(collectionView: UICollectionView, cellForItemAtIndexPath indexPath: NSIndexPath) -> UICollectionViewCell {
        // 1.获取cell
        let cell = collectionView.dequeueReusableCellWithReuseIdentifier("newfeatureCell", forIndexPath: indexPath) as! MANewfeatureCell
        //cell.backgroundColor = (indexPath.item % 2 == 0) ? UIColor.redColor() : UIColor.purpleColor()
        // 2.设置数据
        cell.index = indexPath.item
        
        // 3.返回cell
        return cell
    }
}
```
- UICollectionView代理方法

```swift  
extension NewfeatureViewController: UICollectionViewDelegate
{
    func collectionView(collectionView: UICollectionView, didEndDisplayingCell cell: UICollectionViewCell, forItemAtIndexPath indexPath: NSIndexPath) {
        // 注意: 传入的cell和indexPath都是上一页的, 而不是当前页
        // 1.手动获取当前显示的cell对应的indexPath
        let index = collectionView.indexPathsForVisibleItems().last!
        
        // 2.根据指定的indexPath获取当前显示的cell
        let currentCell = collectionView.cellForItemAtIndexPath(index) as! MANewfeatureCell
        
        // 3.判断当前是否是最后一页
        if index.item == (maxCount - 1)
        {
            // 做按钮动画
            currentCell.startAniamtion()
        }
    }
}
```

- 自定义cell


```swift  
// MARK: - 自定义Cell
class MANewfeatureCell: UICollectionViewCell
{
    var index: Int = 0
        {
        didSet{

            // 1.生成图片名称
            let name = "new_feature_\(index + 1)"
            // 2.设置图片
            iconView.image = UIImage(named: name)
            startButton.hidden = true
        }
    }
    
    required init?(coder aDecoder: NSCoder) {
        super.init(coder: aDecoder)
        
        // 初始化UI
        setupUI()
    }
    
    // MARK: - 外部控制方法
    func startAniamtion()
    {
        startButton.hidden = false
        // 执行放大动画
        /*
        第一个参数: 动画时间
        第二个参数: 延迟时间
        第三个参数: 震幅 0.0~1.0, 值越小震动越列害
        第四个参数: 加速度, 值越大震动越列害
        第五个参数: 动画附加属性
        第六个参数: 执行动画的block
        第七个参数: 执行完毕后回调的block
        */
        startButton.transform = CGAffineTransformMakeScale(0.0, 0.0)
        startButton.userInteractionEnabled = false
        UIView.animateWithDuration(2.0, delay: 0.0, usingSpringWithDamping: 0.5, initialSpringVelocity: 10, options: UIViewAnimationOptions(rawValue: 0), animations: { () -> Void in
            self.startButton.transform = CGAffineTransformIdentity
            
            }, completion: { (_) -> Void in
                self.startButton.userInteractionEnabled = true
        })
    }
```
- 通过SnapKit框架来轻松布局
  - 移动到最后一张新特性图片,会有一个动画出现按钮
  - SnapKit框架支持swift,可以轻松布局


```swift

    // MARK: - 内部控制方法
    private func setupUI()
    {
        // 1.添加子控件
       contentView.addSubview(iconView)
       contentView.addSubview(startButton)
        
        // 2.布局子控件
        /*  使用Auto Layout中的VFL(Visual format language)
        iconView.translatesAutoresizingMaskIntoConstraints = false
        var cons = NSLayoutConstraint.constraintsWithVisualFormat("H:|-0-[iconView]-0-|", options: NSLayoutFormatOptions(rawValue: 0), metrics: nil, views: ["iconView": iconView])
        cons += NSLayoutConstraint.constraintsWithVisualFormat("V:|-0-[iconView]-0-|", options: NSLayoutFormatOptions(rawValue: 0), metrics: nil, views: ["iconView": iconView])
        contentView.addConstraints(cons)
        */
        
        //cocoaPods引入SnapKit框架布局
        iconView.snp_makeConstraints { (make) -> Void in
//            make.left.equalTo(0)
//            make.right.equalTo(0)
//            make.top.equalTo(0)
//            make.bottom.equalTo(0)
            make.edges.equalTo(0)
        }
        
        startButton.snp_makeConstraints { (make) -> Void in
            make.centerX.equalTo(contentView)
            make.bottom.equalTo(contentView).offset(-160)
        }
    }
    
    @objc private func startBtnClick()
    {
        MALog("")
    }

    
    // MARK: - 懒加载
    /// 图片容器
    private lazy var iconView = UIImageView()
    
    /// 开始按钮
    private lazy var startButton: UIButton = {
       let btn = UIButton(imageName: nil, backgroundImageName: "new_feature_button")
        btn.addTarget(self, action: Selector("startBtnClick"), forControlEvents: UIControlEvents.TouchUpInside)
        return btn
    }()
}


```

###新特性界面完整代码
```swift 
//  NewfeatureViewController.swift 

import UIKit
import SnapKit   //cocoaPods引入SnapKit

class NewfeatureViewController: UIViewController {
    /// 新特性界面的个数
    private var maxCount = 4
    override func viewDidLoad() {
        super.viewDidLoad()
 
    }
}

extension NewfeatureViewController: UICollectionViewDataSource
{
    
    // 1.告诉系统有多少组
    func numberOfSectionsInCollectionView(collectionView: UICollectionView) -> Int {
        return 1
    }
    // 2.告诉系统每组多少行
    func collectionView(collectionView: UICollectionView, numberOfItemsInSection section: Int) -> Int {
        return maxCount
    }
    // 3.告诉系统每行显示什么内容
    func collectionView(collectionView: UICollectionView, cellForItemAtIndexPath indexPath: NSIndexPath) -> UICollectionViewCell {
        // 1.获取cell
        let cell = collectionView.dequeueReusableCellWithReuseIdentifier("newfeatureCell", forIndexPath: indexPath) as! MANewfeatureCell
         
        // 2.设置数据
        cell.index = indexPath.item
        
        // 3.返回cell
        return cell
    }
}

extension NewfeatureViewController: UICollectionViewDelegate
{
    func collectionView(collectionView: UICollectionView, didEndDisplayingCell cell: UICollectionViewCell, forItemAtIndexPath indexPath: NSIndexPath) {
        // 注意: 传入的cell和indexPath都是上一页的, 而不是当前页 
        // 1.手动获取当前显示的cell对应的indexPath
        let index = collectionView.indexPathsForVisibleItems().last!
        NJLog(index.item)
        // 2.根据指定的indexPath获取当前显示的cell
        let currentCell = collectionView.cellForItemAtIndexPath(index) as! MANewfeatureCell
        // 3.判断当前是否是最后一页
        if index.item == (maxCount - 1)
        {
            // 做动画
            currentCell.startAniamtion()
        }
    }
}

// MARK: - 自定义Cell
class MANewfeatureCell: UICollectionViewCell
{
    var index: Int = 0
        {
        didSet{

            // 1.生成图片名称
            let name = "new_feature_\(index + 1)"
            // 2.设置图片
            iconView.image = UIImage(named: name)
            startButton.hidden = true
        }
    }
    
    required init?(coder aDecoder: NSCoder) {
        super.init(coder: aDecoder)
        
        // 初始化UI
        setupUI()
    }
    
    // MARK: - 外部控制方法
    func startAniamtion()
    {
        startButton.hidden = false
        // 执行放大动画 
        startButton.transform = CGAffineTransformMakeScale(0.0, 0.0)
        startButton.userInteractionEnabled = false
        UIView.animateWithDuration(2.0, delay: 0.0, usingSpringWithDamping: 0.5, initialSpringVelocity: 10, options: UIViewAnimationOptions(rawValue: 0), animations: { () -> Void in
            self.startButton.transform = CGAffineTransformIdentity
            
            }, completion: { (_) -> Void in
                self.startButton.userInteractionEnabled = true
        })
    }
    
    // MARK: - 内部控制方法
    private func setupUI()
    {
        // 1.添加子控件
       contentView.addSubview(iconView)
       contentView.addSubview(startButton)
        
        // 2.布局子控件
 
        iconView.snp_makeConstraints { (make) -> Void in 
            make.edges.equalTo(0)
        }
        
        startButton.snp_makeConstraints { (make) -> Void in
            make.centerX.equalTo(contentView)
            make.bottom.equalTo(contentView).offset(-160)
        }
    }
    
    @objc private func startBtnClick()
    {
        MALog("")
    }

    
    // MARK: - 懒加载
    /// 图片容器
    private lazy var iconView = UIImageView()
    
    /// 开始按钮
    private lazy var startButton: UIButton = {
       let btn = UIButton(imageName: nil, backgroundImageName: "new_feature_button")
        btn.addTarget(self, action: Selector("startBtnClick"), forControlEvents: UIControlEvents.TouchUpInside)
        return btn
    }()
}

// MARK: - 自定义布局
class MANewfeatureLayout: UICollectionViewFlowLayout
{
    // 准备布局
    override func prepareLayout() {
        // 1.设置每个cell的尺寸
        itemSize = UIScreen.mainScreen().bounds.size
        // 2.设置cell之间的间隙
        minimumInteritemSpacing = 0
        minimumLineSpacing = 0
        // 3.设置滚动方向
        scrollDirection = UICollectionViewScrollDirection.Horizontal
        
        // 4.设置分页
        collectionView?.pagingEnabled = true
        // 5.禁用回弹
        collectionView?.bounces = false
        // 6.取出滚动条
        collectionView?.showsHorizontalScrollIndicator = false
        collectionView?.showsVerticalScrollIndicator = false
    }
}

```