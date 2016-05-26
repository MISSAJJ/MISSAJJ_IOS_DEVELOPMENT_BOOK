# 未登录页面处理

## 课程目标

1. 自定义 `TableViewController` 的基类 `BaseTableViewController`
2. 自定义登录视图
3. 统一导航栏按钮外观

## 应用实例

* 在实际应用开发中，有可能会出现：
    * 功能框架已经构建完成
    * 产品经理提出新的功能需求
    * 而新提出的功能需求，会对已有的架构产生影响
    * 例如，在新浪微博中，已经搭建好程序架构，但是如何应对用户登录的处理呢？

## 新的需求 —— 未登录页面

### 界面截图

* 首页

<img src="./images/未登录界面截图/首页.png" width="320" height="568" />

* 消息

<img src="./images/未登录界面截图/消息.png" width="320" height="568" />

* 发现

<img src="./images/未登录界面截图/发现.png" width="320" height="568" />

* 我

<img src="./images/未登录界面截图/我.png" width="320" height="568" />

## 架构分析及调整

* 现有架构图

<img src="./images/未登录界面截图/现有架构图.png" width="603" height="243" />

* 新增 `BaseTableViewController`

<img src="./images/未登录界面截图/新增BassTableVC.png" width="743" height="228" />

* `BaseTableViewController` 基本功能逻辑

<img src="./images/未登录界面截图/BaseTableVC功能逻辑.png" width="466" height="270" />





