# 0.6.2.2.完善下拉菜单转场效果

---
```objc
Update更新：2016年6月2日 By {MISSAJJ琴瑟静听} 
```
OC项目的转场动画一般都是用第三方的资源,如今通过SDK来自行实现和调整转场动画这部分课程,有些晕头转向,连续反复看了很多遍才陆续清晰了思路,希望大家还能看得明白.


##需求动画效果
![image](images/完善下拉菜单转场动画.gif)


##HomeTableViewController优化转场动画

```Swift
 @objc private func titleBtnClick(btn: TitleButton)
    {
        // 1.修改按钮的状态
        btn.selected = !btn.selected
        
        // 2.显示菜单
        // 2.1创建菜单
        let sb = UIStoryboard(name: "Popover", bundle: nil)
        guard let menuView = sb.instantiateInitialViewController() else
        {
            return
        }
        // 自定义专场动画
        // 设置转场代理
        menuView.transitioningDelegate = self
        // 设置转场动画样式
        menuView.modalPresentationStyle = UIModalPresentationStyle.Custom
        
        // 2.2弹出菜单
        presentViewController(menuView, animated: true, completion: nil)
    }
     
    /// 定义标记记录当前是否是展现
    private var isPresent = false
}

```

## HomeTableViewController代理方法调整
```Swift

extension HomeTableViewController: UIViewControllerTransitioningDelegate
{
    // 该方法用于返回一个负责转场动画的对象
    // 可以在该对象中控制弹出视图的尺寸等
    func presentationControllerForPresentedViewController(presented: UIViewController, presentingViewController presenting: UIViewController, sourceViewController source: UIViewController) -> UIPresentationController?
    {
        return XMGPresentationController(presentedViewController: presented, presentingViewController: presenting)
    }
    
    // 该方法用于返回一个负责转场如何出现的对象
     func animationControllerForPresentedController(presented: UIViewController, presentingController presenting: UIViewController, sourceController source: UIViewController) -> UIViewControllerAnimatedTransitioning?
     {
        isPresent = true
        return self
     }
    
    // 该方法用于返回一个负责转场如何消失的对象
    func animationControllerForDismissedController(dismissed: UIViewController) -> UIViewControllerAnimatedTransitioning?
    {
        isPresent = false
        return self
    }
}

extension HomeTableViewController: UIViewControllerAnimatedTransitioning
{
    // 告诉系统展现和消失的动画时长
    // 暂时用不上
    func transitionDuration(transitionContext: UIViewControllerContextTransitioning?) -> NSTimeInterval
    {
        return 0.5
    }
    
    // 专门用于管理modal如何展现和消失的, 无论是展现还是消失都会调用该方法
    /*
     注意点: 只要我们实现了这个代理方法, 那么系统就不会再有默认的动画了
     也就是说默认的modal从下至上的移动系统不帮再帮我们添加了, 所有的动画操作都需要我们自己实现, 包括需要展现的视图也需要我们自己添加到容器视图上(containerView)
    */
    // transitionContext: 所有动画需要的东西都保存在上下文中, 换而言之就是可以通过transitionContext获取到我们想要的东西
    func animateTransition(transitionContext: UIViewControllerContextTransitioning)
    {
        // 0.判断当前是展现还是消失
        if isPresent
        {
            // 展现
            // 1.获取需要弹出视图
            /*
            let toVC = transitionContext.viewControllerForKey(UITransitionContextToViewControllerKey)
            let fromVC = transitionContext.viewControllerForKey(UITransitionContextFromViewControllerKey)
            NJLog(toVC)
            NJLog(fromVC)
            */
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
            
        }else
        {
            // 消失
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
##MAPresentationController调整重写转场代码

```Swift

//  MAPresentationController.swift
//  XMGWB
//
//  Created by MISSAJJ on 16/6/2.
//  Copyright © 2016年 MISSAJJ. All rights reserved.

import UIKit

class MAPresentationController: UIPresentationController {

    /*
    1.如果不自定义转场modal出来的控制器会移除原有的控制器
    2.如果自定义转场modal出来的控制器不会移除原有的控制器
    3.如果不自定义转场modal出来的控制器的尺寸和屏幕一样
    4.如果自定义转场modal出来的控制器的尺寸我们可以自己在containerViewWillLayoutSubviews方法中控制
    5.containerView 非常重要, 容器视图, 所有modal出来的视图都是添加到containerView上的
    6.presentedView() 非常重要, 通过该方法能够拿到弹出的视图
    */
    override init(presentedViewController: UIViewController, presentingViewController: UIViewController) {
        super.init(presentedViewController: presentedViewController, presentingViewController: presentingViewController)
    }
    
    // 用于布局转场动画弹出的控件
    override func containerViewWillLayoutSubviews()
    {
        // 设置弹出视图的尺寸
        presentedView()?.frame = CGRect(x: 100, y: 45, width: 200, height: 200)
        
        // 添加蒙版
        containerView?.insertSubview(coverButton, atIndex: 0)
        coverButton.addTarget(self, action: #selector(MAPresentationController.coverBtnClick), forControlEvents: UIControlEvents.TouchUpInside)
    }
    
    // MARK: - 内部控制方法
    @objc private func coverBtnClick()
    {
//        NJLog(presentedViewController)
//        NJLog(presentingViewController)
        // 让菜单消失
        presentedViewController.dismissViewControllerAnimated(true, completion: nil)
    }
    
    // MARK: - 懒加载
    private lazy var coverButton: UIButton = {
        let btn = UIButton()
        btn.frame = UIScreen.mainScreen().bounds
        btn.backgroundColor = UIColor.clearColor()
        return btn
    }()
}

```