# 封装优化下拉菜单的转场效果

---
```objc
Update更新：2016年6月2日 By {MISSAJJ琴瑟静听} 
```

直接上代码,慢慢看吧...


##创建转场效果管理类MAPresentationManager

```swift
//  MAPresentationManager.swift
//  MISSAJJ
//
//  Created by MISSAJJ on 16/6/2.
//  Copyright © 2016年 MISSAJJ. All rights reserved.
//

import UIKit

/// 自定转场展现
let MAPresentationManagerDidPresented = "MAPresentationManagerDidPresented"
/// 自定义转场消失
let MAPresentationManagerDidDismissed = "MAPresentationManagerDismissed"

class MAPresentationManager: NSObject , UIViewControllerTransitioningDelegate, UIViewControllerAnimatedTransitioning
{
    /// 定义标记记录当前是否是展现
    private var isPresent = false
    
    /// 保存菜单的尺寸
    var presentFrame = CGRectZero
    
    // MARK: - UIViewControllerTransitioningDelegate
    // 该方法用于返回一个负责转场动画的对象
    // 可以在该对象中控制弹出视图的尺寸等
    func presentationControllerForPresentedViewController(presented: UIViewController, presentingViewController presenting: UIViewController, sourceViewController source: UIViewController) -> UIPresentationController?
    {
        let pc = MAPresentationController(presentedViewController: presented, presentingViewController: presenting)
        pc.presentFrame =  presentFrame
        return pc
    }
    
    // 该方法用于返回一个负责转场如何出现的对象
    func animationControllerForPresentedController(presented: UIViewController, presentingController presenting: UIViewController, sourceController source: UIViewController) -> UIViewControllerAnimatedTransitioning?
    {
        isPresent = true
        // 发送一个通知, 告诉调用者状态发生了改变
        NSNotificationCenter.defaultCenter().postNotificationName(MAPresentationManagerDidPresented, object: self)
        return self
    }
    
    // 该方法用于返回一个负责转场如何消失的对象
    func animationControllerForDismissedController(dismissed: UIViewController) -> UIViewControllerAnimatedTransitioning?
    {
        isPresent = false
        // 发送一个通知, 告诉调用者状态发生了改变
        NSNotificationCenter.defaultCenter().postNotificationName(MAPresentationManagerDidDismissed, object: self)
        return self
    }
    
    // MARK: - UIPresentationController
    // 告诉系统展现和消失的动画时长
    // 暂时用不上
    func transitionDuration(transitionContext: UIViewControllerContextTransitioning?) -> NSTimeInterval
    {
        return 0.5
    }
    
    // 专门用于管理modal如何展现和消失的, 无论是展现还是消失都会调用该方法
    func animateTransition(transitionContext: UIViewControllerContextTransitioning)
    {
        // 0.判断当前是展现还是消失
        if isPresent
        {
            // 展现
            willPresentedController(transitionContext)
            
        }else
        {
            // 消失
            willDismissedController(transitionContext)
        }
    }
    
    /// 执行展现动画
    private func willPresentedController(transitionContext: UIViewControllerContextTransitioning)
    {
        // 1.获取需要弹出视图
        // 通过ToViewKey取出的就是toVC对应的view
        guard let toView = transitionContext.viewForKey(UITransitionContextToViewKey) else
        {
            return
        }
        
        // 2.将需要弹出的视图添加到containerView上
        transitionContext.containerView()?.addSubview(toView)
        
        // 3.执行动画
        toView.transform = CGAffineTransformMakeScale(1.0, 0.0)
        // 设置锚点
        toView.layer.anchorPoint = CGPoint(x: 0.5, y: 0.0)
        UIView.animateWithDuration(transitionDuration(transitionContext), animations: { () -> Void in
            toView.transform = CGAffineTransformIdentity
            }) { (_) -> Void in
                // 注意: 自定转场动画, 在执行完动画之后一定要告诉系统动画执行完毕了
                transitionContext.completeTransition(true)
        }
    }
    /// 执行消失动画
    private func willDismissedController(transitionContext: UIViewControllerContextTransitioning)
    {
        // 1.拿到需要消失的视图
        guard let fromView = transitionContext.viewForKey(UITransitionContextFromViewKey) else
        {
            return
        }
        // 2.执行动画让视图消失
        UIView.animateWithDuration(transitionDuration(transitionContext), animations: { () -> Void in
            // 突然消失的原因: CGFloat不准确, 导致无法执行动画, 遇到这样的问题只需要将CGFloat的值设置为一个很小的值即可
            fromView.transform = CGAffineTransformMakeScale(1.0, 0.00001)
            }, completion: { (_) -> Void in
                // 注意: 自定转场动画, 在执行完动画之后一定要告诉系统动画执行完毕了
                transitionContext.completeTransition(true)
        })
    }

}
```

##主界面的调用调整
```swift
 
//  HomeTableViewController.swift
//  MISSAJJ
//
//  Created by MISSAJJ on 16/6/2.
//  Copyright © 2016年 MISSAJJ. All rights reserved.
//

import UIKit

class HomeTableViewController: BaseTableViewController {

    override func viewDidLoad() {
        super.viewDidLoad()
        
        
        view.backgroundColor = UIColor.redColor()
        // 1.判断用户是否登录
        if !isLogin
        {
            // 设置访客视图
            visitorView?.setupVisitorInfo(nil, title: "关注一些人，回这里看看有什么惊喜")
            return
        }
        
        // 2.初始化导航条
        setupNav()
        
 //***********注册和移除通知:管理转场的显示和消失*************//   
        // 3.注册通知
        NSNotificationCenter.defaultCenter().addObserver(self, selector: Selector("titleChange"), name: MAPresentationManagerDidPresented, object: animatorManager)
        NSNotificationCenter.defaultCenter().addObserver(self, selector: Selector("titleChange"), name: MAPresentationManagerDidDismissed, object: animatorManager)
    }
    deinit
    {
        // 移除通知
        NSNotificationCenter.defaultCenter().removeObserver(self)
    }
//************************************************//   
    
    
    // MARK: - 内部控制方法
    private func setupNav()
    {
        // 1.添加左右按钮
        navigationItem.leftBarButtonItem = UIBarButtonItem(imageName: "navigationbar_friendattention", target: self, action: Selector("leftBtnClick"))
        navigationItem.rightBarButtonItem = UIBarButtonItem(imageName: "navigationbar_pop", target: self, action: Selector("rightBtnClick"))
        
        // 2.添加标题按钮
        navigationItem.titleView = titleButton
    }
    
    @objc private func titleChange()
    {
        titleButton.selected = !titleButton.selected
    }
    @objc private func titleBtnClick(btn: TitleButton)
    {
        // 1.修改按钮的状态
//        btn.selected = !btn.selected
        
        // 2.显示菜单
        // 2.1创建菜单
        let sb = UIStoryboard(name: "Popover", bundle: nil)
        guard let menuView = sb.instantiateInitialViewController() else
        {
            return
        }
        
        //***************代理改为 animatorManager************//  
        // 自定义专场动画
        // 设置转场代理
        menuView.transitioningDelegate = animatorManager
        
        //************************************************//  
        // 设置转场动画样式
        menuView.modalPresentationStyle = UIModalPresentationStyle.Custom
        
        // 2.2弹出菜单
        presentViewController(menuView, animated: true, completion: nil)
    }
    
    @objc private func leftBtnClick()
    {
        NJLog("")
    }
    @objc private func rightBtnClick()
    {
        NJLog("")
    }
   //******************懒加载 转场管理类**************************//  
    // MARK: - 懒加载
    private lazy var animatorManager: MAPresentationManager = {
       let manager = MAPresentationManager()
       //设置下拉菜单的大小
        manager.presentFrame = CGRect(x: 100, y: 45, width: 200, height: 300)
        return manager
    }()
   //************************************************//  
    /// 标题按钮
    private lazy var titleButton: TitleButton = {
        let btn = TitleButton()
        btn.setTitle("MISSAJJ", forState: UIControlState.Normal)
        btn.addTarget(self, action: #selector(HomeTableViewController.titleBtnClick(_:)), forControlEvents: UIControlEvents.TouchUpInside)
        return btn
    }()
}


```
