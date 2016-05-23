# （V） ReactiveCocoa（RAC）编程思想和MVVM架构进阶
 
---
```objc
Update更新：2016年5月20日 By {MISSAJJ琴瑟静听}
由于此章节笔记内容比较多，所以特地分了五篇文章，
避免因文章太长而导致手机浏览器或微信内浏览崩溃
```

### 10.介绍MVVM架构思想。

10.1 程序为什么要架构：`便于程序员开发和维护代码。`

10.2 常见的架构思想:

*	`MVC` M:模型 V:视图 C:控制器

*	`MVVM` M:模型 V:视图+控制器 VM:视图模型

*	`MVCS` M:模型 V:视图 C:控制器 C:服务类

*	`VIPER` V:视图 I:交互器 P:展示器 E:实体 R:路由 (http://www.cocoachina.com/ios/20140703/9016.html)

10.3 MVVM介绍

*	`模型`(M):保存视图数据。

*	`视图+控制器`(V):展示内容 + 如何展示

*	`视图模型`(VM):处理展示的业务逻辑，包括按钮的点击，数据的请求和解析等等。

### 11.ReactiveCocoa + MVVM 实战一：登录界面
*	11.1需求+分析+步骤

```objc

/* 需求：1.监听两个文本框的内容，有内容才允许按钮点击
        2.默认登录请求.

   用MVVM：实现，之前界面的所有业务逻辑
   分析：1.之前界面的所有业务逻辑都交给控制器做处理
        2.在MVVM架构中把控制器的业务全部搬去VM模型，也就是每个控制器对应一个VM模型.

   步骤：1.创建LoginViewModel类，处理登录界面业务逻辑.
        2.这个类里面应该保存着账号的信息，创建一个账号Account模型
        3.LoginViewModel应该保存着账号信息Account模型。
        4.需要时刻监听Account模型中的账号和密码的改变，怎么监听？
        5.在非RAC开发中，都是习惯赋值，在RAC开发中，需要改变开发思维，由赋值转变为绑定，可以在一开始初始化的时候，就给Account模型中的属性绑定，并不需要重写set方法。
        6.每次Account模型的值改变，就需要判断按钮能否点击，在VM模型中做处理，给外界提供一个能否点击按钮的信号.
        7.这个登录信号需要判断Account中账号和密码是否有值，用KVO监听这两个值的改变，把他们聚合成登录信号.
        8.监听按钮的点击，由VM处理，应该给VM声明一个RACCommand，专门处理登录业务逻辑.
        9.执行命令，把数据包装成信号传递出去
        10.监听命令中信号的数据传递
        11.监听命令的执行时刻
 */
```

*	11.2 控制器的代码

```objc
@interface ViewController ()

@property (nonatomic, strong) LoginViewModel *loginViewModel;

@property (weak, nonatomic) IBOutlet UITextField *accountField;
@property (weak, nonatomic) IBOutlet UITextField *pwdField;

@property (weak, nonatomic) IBOutlet UIButton *loginBtn;


@end

- (LoginViewModel *)loginViewModel
{
    if (_loginViewModel == nil) {

        _loginViewModel = [[LoginViewModel alloc] init];
    }
    return _loginViewModel;
}

// 视图模型绑定
- (void)bindModel
{
    // 给模型的属性绑定信号
    // 只要账号文本框一改变，就会给account赋值
    RAC(self.loginViewModel.account, account) = _accountField.rac_textSignal;
    RAC(self.loginViewModel.account, pwd) = _pwdField.rac_textSignal;

    // 绑定登录按钮
    RAC(self.loginBtn,enabled) = self.loginViewModel.enableLoginSignal;

   // 监听登录按钮点击
    [[_loginBtn rac_signalForControlEvents:UIControlEventTouchUpInside] subscribeNext:^(id x) {

        // 执行登录事件
        [self.loginViewModel.LoginCommand execute:nil];
    }];
}

```
*	11.3 VM的代码

```objc
@interface LoginViewModel : NSObject

@property (nonatomic, strong) Account *account;


// 是否允许登录的信号
@property (nonatomic, strong, readonly) RACSignal *enableLoginSignal;

@property (nonatomic, strong, readonly) RACCommand *LoginCommand;

@end

@implementation LoginViewModel
- (Account *)account
{
    if (_account == nil) {
        _account = [[Account alloc] init];
    }
    return _account;
}
- (instancetype)init
{
    if (self = [super init]) {
        [self initialBind];
    }
    return self;
}


// 初始化绑定
- (void)initialBind
{
    // 监听账号的属性值改变，把他们聚合成一个信号。
    _enableLoginSignal = [RACSignal combineLatest:@[RACObserve(self.account, account),RACObserve(self.account, pwd)] reduce:^id(NSString *account,NSString *pwd){

        return @(account.length && pwd.length);

    }];

    // 处理登录业务逻辑
    _LoginCommand = [[RACCommand alloc] initWithSignalBlock:^RACSignal *(id input) {

        NSLog(@"点击了登录");
        return [RACSignal createSignal:^RACDisposable *(id<RACSubscriber> subscriber) {

            // 模仿网络延迟
            dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(0.5 * NSEC_PER_SEC)), dispatch_get_main_queue(), ^{

                [subscriber sendNext:@"登录成功"];

                // 数据传送完毕，必须调用完成，否则命令永远处于执行状态
                [subscriber sendCompleted];
            });

            return nil;
        }];
    }];

    // 监听登录产生的数据
    [_LoginCommand.executionSignals.switchToLatest subscribeNext:^(id x) {

        if ([x isEqualToString:@"登录成功"]) {
            NSLog(@"登录成功");
        }
    }];

    // 监听登录状态
    [[_LoginCommand.executing skip:1] subscribeNext:^(id x) {
        if ([x isEqualToNumber:@(YES)]) {

            // 正在登录ing...
            // 用蒙版提示
            [MBProgressHUD showMessage:@"正在登录..."];


        }else
        {
            // 登录成功
            // 隐藏蒙版
            [MBProgressHUD hideHUD];
        }
    }];
}

```


### 12.ReactiveCocoa + MVVM 实战二：网络请求数据

*	12.1 接口：这里先给朋友介绍一个免费的网络数据接口，`豆瓣`。可以经常用来练习一些网络请求的小Demo.

*	12.2 需求+分析+步骤

```objc
/*
    需求：请求豆瓣图书信息，url:https://api.douban.com/v2/book/search?q=基础

    分析：请求一样，交给VM模型管理

    步骤:
        1.控制器提供一个视图模型（requesViewModel），处理界面的业务逻辑
        2.VM提供一个命令，处理请求业务逻辑
        3.在创建命令的block中，会把请求包装成一个信号，等请求成功的时候，就会把数据传递出去。
        4.请求数据成功，应该把字典转换成模型，保存到视图模型中，控制器想用就直接从视图模型中获取。
        5.假设控制器想展示内容到tableView，直接让视图模型成为tableView的数据源，把所有的业务逻辑交给视图模型去做，这样控制器的代码就非常少了。
 */

```

*	12.3控制器代码


```objc
@interface ViewController ()

@property (nonatomic, weak) UITableView *tableView;

@property (nonatomic, strong) RequestViewModel *requesViewModel;


@end

@implementation ViewController
- (RequestViewModel *)requesViewModel
{
    if (_requesViewModel == nil) {
        _requesViewModel = [[RequestViewModel alloc] init];
    }
    return _requesViewModel;
}

- (void)viewDidLoad {
    [super viewDidLoad];
    // Do any additional setup after loading the view, typically from a nib.

    // 创建tableView
    UITableView *tableView = [[UITableView alloc] initWithFrame:self.view.bounds];
    tableView.dataSource = self.requesViewModel;
    self.requesViewModel.tableView = tableView;
    [self.view addSubview:tableView];

    // 执行请求
    [self.requesViewModel.reuqesCommand execute:nil];

}


@end


```



*	12.4视图模型(VM)代码


```objc
@interface RequestViewModel : NSObject<UITableViewDataSource>


	// 请求命令
	@property (nonatomic, strong, readonly) RACCommand *reuqesCommand;

	//模型数组
	@property (nonatomic, strong, readonly) NSArray *models;

	// 控制器中的view
	@property (nonatomic, weak) UITableView *tableView;

@end

@implementation RequestViewModel

- (instancetype)init
{
    if (self = [super init]) {

        [self initialBind];
    }
    return self;
}


- (void)initialBind
{
    _reuqesCommand = [[RACCommand alloc] initWithSignalBlock:^RACSignal *(id input) {

        RACSignal *requestSignal = [RACSignal createSignal:^RACDisposable *(id<RACSubscriber> subscriber) {


            NSMutableDictionary *parameters = [NSMutableDictionary dictionary];
            parameters[@"q"] = @"基础";

            // 发送请求
            [[AFHTTPRequestOperationManager manager] GET:@"https://api.douban.com/v2/book/search" parameters:parameters success:^(AFHTTPRequestOperation * _Nonnull operation, id  _Nonnull responseObject) {
                NSLog(@"%@",responseObject);

                // 请求成功调用
                // 把数据用信号传递出去
                [subscriber sendNext:responseObject];

                [subscriber sendCompleted];


            } failure:^(AFHTTPRequestOperation * _Nonnull operation, NSError * _Nonnull error) {
                // 请求失败调用

            }];

            return nil;
        }];




        // 在返回数据信号时，把数据中的字典映射成模型信号，传递出去
        return [requestSignal map:^id(NSDictionary *value) {
            NSMutableArray *dictArr = value[@"books"];

            // 字典转模型，遍历字典中的所有元素，全部映射成模型，并且生成数组
            NSArray *modelArr = [[dictArr.rac_sequence map:^id(id value) {

                return [Book bookWithDict:value];
            }] array];

            return modelArr;
        }];

    }];

    // 获取请求的数据
    [_reuqesCommand.executionSignals.switchToLatest subscribeNext:^(NSArray *x) {

        // 有了新数据，刷新表格
        _models = x;

        // 刷新表格
        [self.tableView reloadData];

    }];
}

#pragma mark - UITableViewDataSource

- (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section
{
    return self.models.count;
}

- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath
{
    static NSString *ID = @"cell";
    UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:ID];
    if (cell == nil) {

        cell = [[UITableViewCell alloc] initWithStyle:UITableViewCellStyleSubtitle reuseIdentifier:ID];
    }

    Book *book = self.models[indexPath.row];
    cell.detailTextLabel.text = book.subtitle;
    cell.textLabel.text = book.title;

    return cell;
}

@end
```

###13.RAC归纳总结

###一.信号类:表示有数据产生

*	`RACSignal`

*	`RACDynamicSignal` -> didSubscribe(block)

*	`RACSubject` -> subscribers(数组)

*	`RACReplaySubject` -> valuesReceived(数组)


###二.不同的信号订阅方式不同

*	`RACDynamicSignal`:
	*	1.创建订阅者RACSubscriber
	*	2.执行didSubscribe

*	`RACSubject`: 
	*	1.创建订阅者RACSubscriber
	*	2.保存订阅者

*	`RACReplaySubject`:
	*	 1.创建订阅者RACSubscriber
	*	 2.拿到当前创建订阅者,发送之前保存的所有值

###三.订阅者:发送数据

*	`RACSubscriber` -> nextBlock(block)

*	`RACSubject` -> subscribers(数组)

*	`RACReplaySubject` -> valuesReceived(数组)

###四.不同订阅者,发送数据方式不同

*	[`RACSubscriber` sendNext]: 
	*	执行nextBlock
	
*	[`RACSubject` sendNext]: 
	*	遍历自己所有订阅者,发送数据
	
*	[`RACReplaySubject` sendNext]:
	*	1.保存发送的值
	*	2.遍历自己所有订阅者,发送数据


