#Chapter-16 ReactiveCocoa编程思想和框架使用
---
```objc
Update更新：2016年5月15日 By {MISSAJJ琴瑟静听}
```
### 1.ReactiveCocoa简介

ReactiveCocoa（简称为`RAC`）,是由Github开源的一个应用于iOS和OS开发的新框架,Cocoa是苹果整套框架的简称，因此很多苹果框架喜欢以Cocoa结尾。

### 2.ReactiveCocoa作用

在我们iOS开发过程中，经常会响应某些事件来处理某些业务逻辑，例如按钮的点击，上下拉刷新，网络请求，属性的变化（通过KVO）或者用户位置的变化（通过CoreLocation）。但是这些事件都用不同的方式来处理，比如action、delegate、KVO、callback等。

其实这些事件，都可以通过RAC处理，ReactiveCocoa为事件提供了很多处理方法，而且利用RAC处理事件很方便，可以把要处理的事情，和监听的事情的代码放在一起，这样非常方便我们管理，就不需要跳到对应的方法里。非常符合我们开发中`高聚合，低耦合`的思想。

### 3.编程思想

在开发中我们也不能太依赖于某个框架，否则这个框架不更新了，导致项目后期没办法维护，比如之前Facebook提供的`Three20框架`，在当时也是神器，但是后来不更新了，也就没什么人用了。因此我感觉学习一个框架，还是有必要了解它的`编程思想`。

先简单介绍下目前咱们已知的`编程思想`。

3.1 `面向过程`：处理事情以过程为核心，一步一步的实现。

3.2 `面向对象`：万物皆对象

3.3 `链式编程思想`：是将多个操作（多行代码）通过点号(.)链接在一起成为一句代码,使代码可读性好。a(1).b(2).c(3)

*	`链式编程特点`：方法的返回值是block,block必须有返回值（本身对象），block参数（需要操作的值）

*	`代表`：masonry框架。

*	`练习一`:模仿masonry，写一个加法计算器，练习链式编程思想。

3.4 `响应式编程思想`：不需要考虑调用顺序，只需要知道考虑结果，类似于蝴蝶效应，产生一个事件，会影响很多东西，这些事件像流一样的传播出去，然后影响结果，借用面向对象的一句话，万物皆是流。

*	`代表`：KVO运用。

*	`练习二`:KVO底层实现。


3.5 `函数式编程思想`：是把操作尽量写成一系列嵌套的函数或者方法调用。

*	`函数式编程本质`:就是往方法中传入Block,方法中嵌套Block调用，把代码聚合起来管理
*	`函数式编程特点`：每个方法必须有返回值（本身对象）,把函数或者Block当做参数,block参数（需要操作的值）block返回值（操作结果）

*	`代表`：ReactiveCocoa。

*	`练习三`:用函数式编程实现，写一个加法计算器,并且加法计算器自带判断是否等于某个值.

### 4.ReactiveCocoa编程思想
ReactiveCocoa结合了几种编程风格：

`函数式编程（Functional Programming）`

`响应式编程（Reactive Programming）`

所以，你可能听说过ReactiveCocoa被描述为函数响应式编程（FRP）框架。

以后使用RAC解决问题，就不需要考虑调用顺序，直接考虑结果，把每一次操作都写成一系列嵌套的方法中，使代码高聚合，方便管理。


### 5.如何导入ReactiveCocoa框架
通常都会使用CocoaPods（用于管理第三方框架的插件）帮助我们导入。

PS:CocoaPods教程（http://code4app.com/article/cocoapods-install-usage）

`练习四`:创建一个新的工程，演示下，框架的导入。

`注意`：
*   podfile如果只描述pod 'ReactiveCocoa', '~> 4.0.2-alpha-1'，会导入不成功
  
![](Snip20150926_1.png)
*   报错信息

![](Snip20150926_2.png)
*   需要在podfile加上use_frameworks，重新pod install 才能导入成功。


![](Snip20150926_3.png)


### 6.ReactiveCocoa常见类。

学习框架首要之处:个人认为先要搞清楚框架中常用的类，在RAC中最核心的类RACSiganl,搞定这个类就能用ReactiveCocoa开发了。

`练习五`:介绍常见类

`RACSiganl`:信号类,一般表示将来有数据传递，只要有数据改变，信号内部接收到数据，就会马上发出数据。

*	信号类(RACSiganl)，只是表示当数据改变时，信号内部会发出数据，它本身不具备发送信号的能力，而是交给内部一个订阅者去发出。

*	默认一个信号都是冷信号，也就是值改变了，也不会触发，只有订阅了这个信号，这个信号才会变为热信号，值改变了才会触发。

*	如何订阅信号：调用信号RACSignal的subscribeNext就能订阅。

*	`RACSiganl简单使用:`

```
	// RACSignal使用步骤：
    // 1.创建信号 + (RACSignal *)createSignal:(RACDisposable * (^)(id<RACSubscriber> subscriber))didSubscribe
    // 2.订阅信号,才会激活信号. - (RACDisposable *)subscribeNext:(void (^)(id x))nextBlock
    // 3.发送信号 - (void)sendNext:(id)value


    // RACSignal底层实现：
    // 1.创建信号，首先把didSubscribe保存到信号中，还不会触发。
    // 2.当信号被订阅，也就是调用signal的subscribeNext:nextBlock
    // 2.2 subscribeNext内部会创建订阅者subscriber，并且把nextBlock保存到subscriber中。
    // 2.1 subscribeNext内部会调用siganl的didSubscribe
    // 3.siganl的didSubscribe中调用[subscriber sendNext:@1];
    // 3.1 sendNext底层其实就是执行subscriber的nextBlock

    // 1.创建信号
    RACSignal *siganl = [RACSignal createSignal:^RACDisposable *(id<RACSubscriber> subscriber) {

        // block调用时刻：每当有订阅者订阅信号，就会调用block。

        // 2.发送信号
        [subscriber sendNext:@1];

        // 如果不在发送数据，最好发送信号完成，内部会自动调用[RACDisposable disposable]取消订阅信号。
        [subscriber sendCompleted];

        return [RACDisposable disposableWithBlock:^{

            // block调用时刻：当信号发送完成或者发送错误，就会自动执行这个block,取消订阅信号。

            // 执行完Block后，当前信号就不在被订阅了。

            NSLog(@"信号被销毁");

        }];
    }];

    // 3.订阅信号,才会激活信号.
    [siganl subscribeNext:^(id x) {
        // block调用时刻：每当有信号发出数据，就会调用block.
        NSLog(@"接收到数据:%@",x);
    }];

```

`RACSubscriber`:表示订阅者的意思，用于发送信号，这是一个协议，不是一个类，只要遵守这个协议，并且实现方法才能成为订阅者。通过create创建的信号，都有一个订阅者，帮助他发送数据。

`RACDisposable`:用于取消订阅或者清理资源，当信号发送完成或者发送错误的时候，就会自动触发它。

*	`使用场景`:不想监听某个信号时，可以通过它主动取消订阅信号。

`RACSubject`:RACSubject:信号提供者，自己可以充当信号，又能发送信号。

*	`使用场景`:通常用来代替代理，有了它，就不必要定义代理了。


`RACReplaySubject`:重复提供信号类，RACSubject的子类。
*   `RACReplaySubject`与`RACSubject`区别:
    *   RACReplaySubject可以先发送信号，在订阅信号，RACSubject就不可以。
*	`使用场景一`:如果一个信号每被订阅一次，就需要把之前的值重复发送一遍，使用重复提供信号类。
*	`使用场景二`:可以设置capacity数量来限制缓存的value的数量,即只缓充最新的几个值。

*	`RACSubject和RACReplaySubject简单使用:`

```
    // RACSubject使用步骤
    // 1.创建信号 [RACSubject subject]，跟RACSiganl不一样，创建信号时没有block。
    // 2.订阅信号 - (RACDisposable *)subscribeNext:(void (^)(id x))nextBlock
    // 3.发送信号 sendNext:(id)value

    // RACSubject:底层实现和RACSignal不一样。
    // 1.调用subscribeNext订阅信号，只是把订阅者保存起来，并且订阅者的nextBlock已经赋值了。
    // 2.调用sendNext发送信号，遍历刚刚保存的所有订阅者，一个一个调用订阅者的nextBlock。

    // 1.创建信号
    RACSubject *subject = [RACSubject subject];

    // 2.订阅信号
    [subject subscribeNext:^(id x) {
        // block调用时刻：当信号发出新值，就会调用.
        NSLog(@"第一个订阅者%@",x);
    }];
    [subject subscribeNext:^(id x) {
        // block调用时刻：当信号发出新值，就会调用.
        NSLog(@"第二个订阅者%@",x);
    }];

    // 3.发送信号
    [subject sendNext:@"1"];


    // RACReplaySubject使用步骤:
    // 1.创建信号 [RACSubject subject]，跟RACSiganl不一样，创建信号时没有block。
    // 2.可以先订阅信号，也可以先发送信号。
    // 2.1 订阅信号 - (RACDisposable *)subscribeNext:(void (^)(id x))nextBlock
    // 2.2 发送信号 sendNext:(id)value

    // RACReplaySubject:底层实现和RACSubject不一样。
    // 1.调用sendNext发送信号，把值保存起来，然后遍历刚刚保存的所有订阅者，一个一个调用订阅者的nextBlock。
    // 2.调用subscribeNext订阅信号，遍历保存的所有值，一个一个调用订阅者的nextBlock

    // 如果想当一个信号被订阅，就重复播放之前所有值，需要先发送信号，在订阅信号。
    // 也就是先保存值，在订阅值。

    // 1.创建信号
    RACReplaySubject *replaySubject = [RACReplaySubject subject];

    // 2.发送信号
    [replaySubject sendNext:@1];
    [replaySubject sendNext:@2];

    // 3.订阅信号
    [replaySubject subscribeNext:^(id x) {

        NSLog(@"第一个订阅者接收到的数据%@",x);
    }];

    // 订阅信号
    [replaySubject subscribeNext:^(id x) {

        NSLog(@"第二个订阅者接收到的数据%@",x);
    }];

```

*	`RACSubject替换代理`

```
	// 需求:
	// 1.给当前控制器添加一个按钮，modal到另一个控制器界面
	// 2.另一个控制器view中有个按钮，点击按钮，通知当前控制器

步骤一：在第二个控制器.h，添加一个RACSubject代替代理。
@interface TwoViewController : UIViewController

@property (nonatomic, strong) RACSubject *delegateSignal;

@end

步骤二：监听第二个控制器按钮点击
@implementation TwoViewController
- (IBAction)notice:(id)sender {
    // 通知第一个控制器，告诉它，按钮被点了

     // 通知代理
     // 判断代理信号是否有值
    if (self.delegateSignal) {
        // 有值，才需要通知
        [self.delegateSignal sendNext:nil];
    }
}
@end

步骤三：在第一个控制器中，监听跳转按钮，给第二个控制器的代理信号赋值，并且监听.
@implementation OneViewController
- (IBAction)btnClick:(id)sender {

    // 创建第二个控制器
    TwoViewController *twoVc = [[TwoViewController alloc] init];

    // 设置代理信号
    twoVc.delegateSignal = [RACSubject subject];

    // 订阅代理信号
    [twoVc.delegateSignal subscribeNext:^(id x) {

        NSLog(@"点击了通知按钮");
    }];

    // 跳转到第二个控制器
    [self presentViewController:twoVc animated:YES completion:nil];

}
@end

```

`RACTuple`:元组类,类似NSArray,用来包装值.

`RACSequence`:RAC中的集合类，用于代替NSArray,NSDictionary,可以使用它来快速遍历数组和字典。

`使用场景`：1.字典转模型

`RACSequence和RACTuple简单使用`

```
    // 1.遍历数组
    NSArray *numbers = @[@1,@2,@3,@4];

    // 这里其实是三步
    // 第一步: 把数组转换成集合RACSequence numbers.rac_sequence
    // 第二步: 把集合RACSequence转换RACSignal信号类,numbers.rac_sequence.signal
    // 第三步: 订阅信号，激活信号，会自动把集合中的所有值，遍历出来。
    [numbers.rac_sequence.signal subscribeNext:^(id x) {

        NSLog(@"%@",x);
    }];


    // 2.遍历字典,遍历出来的键值对会包装成RACTuple(元组对象)
    NSDictionary *dict = @{@"name":@"xmg",@"age":@18};
    [dict.rac_sequence.signal subscribeNext:^(RACTuple *x) {

        // 解包元组，会把元组的值，按顺序给参数里面的变量赋值
        RACTupleUnpack(NSString *key,NSString *value) = x;

        // 相当于以下写法
//        NSString *key = x[0];
//        NSString *value = x[1];

        NSLog(@"%@ %@",key,value);

    }];


    // 3.字典转模型
    // 3.1 OC写法
    NSString *filePath = [[NSBundle mainBundle] pathForResource:@"flags.plist" ofType:nil];

    NSArray *dictArr = [NSArray arrayWithContentsOfFile:filePath];

    NSMutableArray *items = [NSMutableArray array];

    for (NSDictionary *dict in dictArr) {
        FlagItem *item = [FlagItem flagWithDict:dict];
        [items addObject:item];
    }

    // 3.2 RAC写法
    NSString *filePath = [[NSBundle mainBundle] pathForResource:@"flags.plist" ofType:nil];

    NSArray *dictArr = [NSArray arrayWithContentsOfFile:filePath];

    NSMutableArray *flags = [NSMutableArray array];

    _flags = flags;

    // rac_sequence注意点：调用subscribeNext，并不会马上执行nextBlock，而是会等一会。
    [dictArr.rac_sequence.signal subscribeNext:^(id x) {
        // 运用RAC遍历字典，x：字典

        FlagItem *item = [FlagItem flagWithDict:x];

        [flags addObject:item];

    }];

    NSLog(@"%@",  NSStringFromCGRect([UIScreen mainScreen].bounds));


    // 3.3 RAC高级写法:
    NSString *filePath = [[NSBundle mainBundle] pathForResource:@"flags.plist" ofType:nil];

    NSArray *dictArr = [NSArray arrayWithContentsOfFile:filePath];
    // map:映射的意思，目的：把原始值value映射成一个新值
    // array: 把集合转换成数组
    // 底层实现：当信号被订阅，会遍历集合中的原始值，映射成新值，并且保存到新的数组里。
    NSArray *flags = [[dictArr.rac_sequence map:^id(id value) {

        return [FlagItem flagWithDict:value];

    }] array];

```

`RACMulticastConnection`:用于当一个信号，被多次订阅时，为了保证创建信号时，避免多次调用创建信号中的block，造成副作用，可以使用这个类处理。

`使用注意`:RACMulticastConnection通过RACSignal的-publish或者-muticast:方法创建.

`RACMulticastConnection简单使用`:

```
    // RACMulticastConnection使用步骤:
    // 1.创建信号 + (RACSignal *)createSignal:(RACDisposable * (^)(id<RACSubscriber> subscriber))didSubscribe
    // 2.创建连接 RACMulticastConnection *connect = [signal publish];
    // 3.订阅信号,注意：订阅的不在是之前的信号，而是连接的信号。 [connect.signal subscribeNext:nextBlock]
    // 4.连接 [connect connect]

    // RACMulticastConnection底层原理:
    // 1.创建connect，connect.sourceSignal -> RACSignal(原始信号)  connect.signal -> RACSubject
    // 2.订阅connect.signal，会调用RACSubject的subscribeNext，创建订阅者，而且把订阅者保存起来，不会执行block。
    // 3.[connect connect]内部会订阅RACSignal(原始信号)，并且订阅者是RACSubject
    // 3.1.订阅原始信号，就会调用原始信号中的didSubscribe
    // 3.2 didSubscribe，拿到订阅者调用sendNext，其实是调用RACSubject的sendNext
    // 4.RACSubject的sendNext,会遍历RACSubject所有订阅者发送信号。
    // 4.1 因为刚刚第二步，都是在订阅RACSubject，因此会拿到第二步所有的订阅者，调用他们的nextBlock


    // 需求：假设在一个信号中发送请求，每次订阅一次都会发送请求，这样就会导致多次请求。
    // 解决：使用RACMulticastConnection就能解决.

    // 1.创建请求信号
   RACSignal *signal = [RACSignal createSignal:^RACDisposable *(id<RACSubscriber> subscriber) {


        NSLog(@"发送请求");

        return nil;
    }];
    // 2.订阅信号
    [signal subscribeNext:^(id x) {

        NSLog(@"接收数据");

    }];
    // 2.订阅信号
    [signal subscribeNext:^(id x) {

        NSLog(@"接收数据");

    }];

    // 3.运行结果，会执行两遍发送请求，也就是每次订阅都会发送一次请求


    // RACMulticastConnection:解决重复请求问题
    // 1.创建信号
    RACSignal *signal = [RACSignal createSignal:^RACDisposable *(id<RACSubscriber> subscriber) {


        NSLog(@"发送请求");
        [subscriber sendNext:@1];

        return nil;
    }];

    // 2.创建连接
    RACMulticastConnection *connect = [signal publish];

    // 3.订阅信号，
    // 注意：订阅信号，也不能激活信号，只是保存订阅者到数组，必须通过连接,当调用连接，就会一次性调用所有订阅者的sendNext:
    [connect.signal subscribeNext:^(id x) {

        NSLog(@"订阅者一信号");

    }];

    [connect.signal subscribeNext:^(id x) {

        NSLog(@"订阅者二信号");

    }];

    // 4.连接,激活信号
    [connect connect];

```

`RACCommand`:RAC中用于处理事件的类，可以把事件如何处理,事件中的数据如何传递，包装到这个类中，他可以很方便的监控事件的执行过程。

`使用场景`:监听按钮点击，网络请求

`RACCommand简单使用`

```

 	// 一、RACCommand使用步骤:
    // 1.创建命令 initWithSignalBlock:(RACSignal * (^)(id input))signalBlock
    // 2.在signalBlock中，创建RACSignal，并且作为signalBlock的返回值
    // 3.执行命令 - (RACSignal *)execute:(id)input

    // 二、RACCommand使用注意:
    // 1.signalBlock必须要返回一个信号，不能传nil.
    // 2.如果不想要传递信号，直接创建空的信号[RACSignal empty];
    // 3.RACCommand中信号如果数据传递完，必须调用[subscriber sendCompleted]，这时命令才会执行完毕，否则永远处于执行中。

    // 三、RACCommand设计思想：内部signalBlock为什么要返回一个信号，这个信号有什么用。
    // 1.在RAC开发中，通常会把网络请求封装到RACCommand，直接执行某个RACCommand就能发送请求。
    // 2.当RACCommand内部请求到数据的时候，需要把请求的数据传递给外界，这时候就需要通过signalBlock返回的信号传递了。

    // 四、如何拿到RACCommand中返回信号发出的数据。
    // 1.RACCommand有个执行信号源executionSignals，这个是signal of signals(信号的信号),意思是信号发出的数据是信号，不是普通的类型。
    // 2.订阅executionSignals就能拿到RACCommand中返回的信号，然后订阅signalBlock返回的信号，就能获取发出的值。

    // 五、监听当前命令是否正在执行executing

    // 六、使用场景,监听按钮点击，网络请求


	// 1.创建命令
    RACCommand *command = [[RACCommand alloc] initWithSignalBlock:^RACSignal *(id input) {


        NSLog(@"执行命令");

        // 创建空信号,必须返回信号
        //        return [RACSignal empty];

        // 2.创建信号,用来传递数据
        return [RACSignal createSignal:^RACDisposable *(id<RACSubscriber> subscriber) {

            [subscriber sendNext:@"请求数据"];

            // 注意：数据传递完，最好调用sendCompleted，这时命令才执行完毕。
            [subscriber sendCompleted];

            return nil;
        }];

    }];

    // 强引用命令，不要被销毁，否则接收不到数据
    _conmmand = command;


    // 3.执行命令
    [self.conmmand execute:@1];

    // 4.订阅RACCommand中的信号
    [command.executionSignals subscribeNext:^(id x) {

        [x subscribeNext:^(id x) {

            NSLog(@"%@",x);
        }];

    }];

    // RAC高级用法
    // switchToLatest:用于signal of signals，获取signal of signals发出的最新信号,也就是可以直接拿到RACCommand中的信号
    [command.executionSignals.switchToLatest subscribeNext:^(id x) {

        NSLog(@"%@",x);
    }];

    // 5.监听命令是否执行完毕,默认会来一次，可以直接跳过，skip表示跳过第一次信号。
    [[command.executing skip:1] subscribeNext:^(id x) {

        if ([x boolValue] == YES) {
            // 正在执行
            NSLog(@"正在执行");

        }else{
            // 执行完成
            NSLog(@"执行完成");
        }

    }];

```

`RACScheduler`:RAC中的队列，用GCD封装的。

`RACUnit` :表⽰stream不包含有意义的值,也就是看到这个，可以直接理解为nil.

`RACEvent`: 把数据包装成信号事件(signal event)。它主要通过RACSignal的-materialize来使用，然并卵。


### 7.ReactiveCocoa开发中常见用法。

7.1 代替代理:

*	`rac_signalForSelector`：用于替代代理。

7.2 代替KVO :

*	`rac_valuesAndChangesForKeyPath`：用于监听某个对象的属性改变。

7.3 监听事件:

*	`rac_signalForControlEvents`：用于监听某个事件。

7.4 代替通知:

*	`rac_addObserverForName`:用于监听某个通知。

7.5 监听文本框文字改变:

*	`rac_textSignal`:只要文本框发出改变就会发出这个信号。

7.6 处理当界面有多次请求时，需要都获取到数据时，才能展示界面

*	`rac_liftSelector:withSignalsFromArray:Signals`:当传入的Signals(信号数组)，每一个signal都至少sendNext过一次，就会去触发第一个selector参数的方法。
*	使用注意：几个信号，参数一的方法就几个参数，每个参数对应信号发出的数据。

7.7 代码演示

```

  	// 1.代替代理
    // 需求：自定义redView,监听红色view中按钮点击
    // 之前都是需要通过代理监听，给红色View添加一个代理属性，点击按钮的时候，通知代理做事情
    // rac_signalForSelector:把调用某个对象的方法的信息转换成信号，就要调用这个方法，就会发送信号。
    // 这里表示只要redV调用btnClick:,就会发出信号，订阅就好了。
    [[redV rac_signalForSelector:@selector(btnClick:)] subscribeNext:^(id x) {
        NSLog(@"点击红色按钮");
    }];

    // 2.KVO
    // 把监听redV的center属性改变转换成信号，只要值改变就会发送信号
    // observer:可以传入nil
    [[redV rac_valuesAndChangesForKeyPath:@"center" options:NSKeyValueObservingOptionNew observer:nil] subscribeNext:^(id x) {

        NSLog(@"%@",x);

    }];

    // 3.监听事件
    // 把按钮点击事件转换为信号，点击按钮，就会发送信号
    [[self.btn rac_signalForControlEvents:UIControlEventTouchUpInside] subscribeNext:^(id x) {

        NSLog(@"按钮被点击了");
    }];

    // 4.代替通知
    // 把监听到的通知转换信号
    [[[NSNotificationCenter defaultCenter] rac_addObserverForName:UIKeyboardWillShowNotification object:nil] subscribeNext:^(id x) {
        NSLog(@"键盘弹出");
    }];

    // 5.监听文本框的文字改变
   [_textField.rac_textSignal subscribeNext:^(id x) {

       NSLog(@"文字改变了%@",x);
   }];

   // 6.处理多个请求，都返回结果的时候，统一做处理.
    RACSignal *request1 = [RACSignal createSignal:^RACDisposable *(id<RACSubscriber> subscriber) {

        // 发送请求1
        [subscriber sendNext:@"发送请求1"];
        return nil;
    }];

    RACSignal *request2 = [RACSignal createSignal:^RACDisposable *(id<RACSubscriber> subscriber) {
        // 发送请求2
        [subscriber sendNext:@"发送请求2"];
        return nil;
    }];

    // 使用注意：几个信号，参数一的方法就几个参数，每个参数对应信号发出的数据。
    [self rac_liftSelector:@selector(updateUIWithR1:r2:) withSignalsFromArray:@[request1,request2]];


}
// 更新UI
- (void)updateUIWithR1:(id)data r2:(id)data1
{
    NSLog(@"更新UI%@  %@",data,data1);
}

```

### 8.ReactiveCocoa常见宏。

8.1 `RAC(TARGET, [KEYPATH, [NIL_VALUE]])`:用于给某个对象的某个属性绑定。

`基本用法`

```
	// 只要文本框文字改变，就会修改label的文字
    RAC(self.labelView,text) = _textField.rac_textSignal;

```

8.2 `RACObserve(self, name) `:监听某个对象的某个属性,返回的是信号。

`基本用法`

```
[RACObserve(self.view, center) subscribeNext:^(id x) {

        NSLog(@"%@",x);
    }];

```

8.3  `@weakify(Obj)和@strongify(Obj)`,一般两个都是配套使用,解决循环引用问题.

8.4 `RACTuplePack`：把数据包装成RACTuple（元组类）

`基本用法`
```
	// 把参数中的数据包装成元组
    RACTuple *tuple = RACTuplePack(@10,@20);
```



8.5 `RACTupleUnpack`：把RACTuple（元组类）解包成对应的数据。

`基本用法`
```
	// 把参数中的数据包装成元组
    RACTuple *tuple = RACTuplePack(@"xmg",@20);

    // 解包元组，会把元组的值，按顺序给参数里面的变量赋值
    // name = @"xmg" age = @20
    RACTupleUnpack(NSString *name,NSNumber *age) = tuple;
```

 

### 9.ReactiveCocoa常见操作方法介绍。

* 9.1 ReactiveCocoa操作须知
    *   所有的信号（RACSignal）都可以进行操作处理，因为所有操作方法都定义在RACStream.h中，因此只要继承RACStream就有了操作处理方法。

* 9.2 ReactiveCocoa操作思想
    *   运用的是Hook（钩子）思想，Hook是一种用于改变API(应用程序编程接口：方法)执行结果的技术.
    *   Hook用处：截获API调用的技术。
    *   Hook原理：在每次调用一个API返回结果之前，先执行你自己的方法，改变结果的输出。

* 9.3 ReactiveCocoa核心方法bind

    *   ReactiveCocoa操作的核心方法是`bind`（绑定）,而且RAC中核心开发方式，也是`绑定`，之前的开发方式是`赋值`，而用RAC开发，应该把重心放在绑定，也就是可以在创建一个对象的时候，就绑定好以后想要做的事情，而不是等赋值之后在去做事情。

    *   列如：把数据展示到控件上，之前都是重写控件的setModel方法，用RAC就可以在一开始创建控件的时候，就绑定好数据。

    *   在开发中很少使用bind方法，bind属于RAC中的底层方法，RAC已经封装了很多好用的其他方法，底层都是调用bind，用法比bind简单.
    * `bind`方法简单介绍和使用。

```
    // 假设想监听文本框的内容，并且在每次输出结果的时候，都在文本框的内容拼接一段文字“输出：”

    // 方式一:在返回结果后，拼接。
        [_textField.rac_textSignal subscribeNext:^(id x) {

            NSLog(@"输出:%@",x);

        }];

    // 方式二:在返回结果前，拼接，使用RAC中bind方法做处理。
    // bind方法参数:需要传入一个返回值是RACStreamBindBlock的block参数
    // RACStreamBindBlock是一个block的类型，返回值是信号，参数（value,stop），因此参数的block返回值也是一个block。

    // RACStreamBindBlock:
    // 参数一(value):表示接收到信号的原始值，还没做处理
    // 参数二(*stop):用来控制绑定Block，如果*stop = yes,那么就会结束绑定。
    // 返回值：信号，做好处理，在通过这个信号返回出去，一般使用RACReturnSignal,需要手动导入头文件RACReturnSignal.h。

    // bind方法使用步骤:
    // 1.传入一个返回值RACStreamBindBlock的block。
    // 2.描述一个RACStreamBindBlock类型的bindBlock作为block的返回值。
    // 3.描述一个返回结果的信号，作为bindBlock的返回值。
    // 注意：在bindBlock中做信号结果的处理。

    // 底层实现:
    // 1.源信号调用bind,会重新创建一个绑定信号。
    // 2.当绑定信号被订阅，就会调用绑定信号中的didSubscribe，生成一个bindingBlock。
    // 3.当源信号有内容发出，就会把内容传递到bindingBlock处理，调用bindingBlock(value,stop)
    // 4.调用bindingBlock(value,stop)，会返回一个内容处理完成的信号（RACReturnSignal）。
    // 5.订阅RACReturnSignal，就会拿到绑定信号的订阅者，把处理完成的信号内容发送出来。

    // 注意:不同订阅者，保存不同的nextBlock，看源码的时候，一定要看清楚订阅者是哪个。
    // 这里需要手动导入#import <ReactiveCocoa/RACReturnSignal.h>，才能使用RACReturnSignal。

    [[_textField.rac_textSignal bind:^RACStreamBindBlock{

        // 什么时候调用:
        // block作用:表示绑定了一个信号.

        return ^RACStream *(id value, BOOL *stop){

            // 什么时候调用block:当信号有新的值发出，就会来到这个block。

            // block作用:做返回值的处理

            // 做好处理，通过信号返回出去.
            return [RACReturnSignal return:[NSString stringWithFormat:@"输出:%@",value]];
        };

    }] subscribeNext:^(id x) {

        NSLog(@"%@",x);

    }];

```

* 9.4ReactiveCocoa操作方法之映射(flattenMap,Map)

    *   `flattenMap`，`Map`用于把源信号内容映射成新的内容。

`flattenMap`简单使用

```
   // 监听文本框的内容改变，把结构重新映射成一个新值.

  // flattenMap作用:把源信号的内容映射成一个新的信号，信号可以是任意类型。

    // flattenMap使用步骤:
    // 1.传入一个block，block类型是返回值RACStream，参数value
    // 2.参数value就是源信号的内容，拿到源信号的内容做处理
    // 3.包装成RACReturnSignal信号，返回出去。

    // flattenMap底层实现:
    // 0.flattenMap内部调用bind方法实现的,flattenMap中block的返回值，会作为bind中bindBlock的返回值。
    // 1.当订阅绑定信号，就会生成bindBlock。
    // 2.当源信号发送内容，就会调用bindBlock(value, *stop)
    // 3.调用bindBlock，内部就会调用flattenMap的block，flattenMap的block作用：就是把处理好的数据包装成信号。
    // 4.返回的信号最终会作为bindBlock中的返回信号，当做bindBlock的返回信号。
    // 5.订阅bindBlock的返回信号，就会拿到绑定信号的订阅者，把处理完成的信号内容发送出来。



    [[_textField.rac_textSignal flattenMap:^RACStream *(id value) {

        // block什么时候 : 源信号发出的时候，就会调用这个block。

        // block作用 : 改变源信号的内容。

        // 返回值：绑定信号的内容.
        return [RACReturnSignal return:[NSString stringWithFormat:@"输出:%@",value]];

    }] subscribeNext:^(id x) {

        // 订阅绑定信号，每当源信号发送内容，做完处理，就会调用这个block。

        NSLog(@"%@",x);

    }];

```


 `Map`简单使用:



```
 // 监听文本框的内容改变，把结构重新映射成一个新值.

    // Map作用:把源信号的值映射成一个新的值

    // Map使用步骤:
    // 1.传入一个block,类型是返回对象，参数是value
    // 2.value就是源信号的内容，直接拿到源信号的内容做处理
    // 3.把处理好的内容，直接返回就好了，不用包装成信号，返回的值，就是映射的值。

    // Map底层实现:
    // 0.Map底层其实是调用flatternMap,Map中block中的返回的值会作为flatternMap中block中的值。
    // 1.当订阅绑定信号，就会生成bindBlock。
    // 3.当源信号发送内容，就会调用bindBlock(value, *stop)
    // 4.调用bindBlock，内部就会调用flattenMap的block
    // 5.flattenMap的block内部会调用Map中的block，把Map中的block返回的内容包装成返回的信号。
    // 5.返回的信号最终会作为bindBlock中的返回信号，当做bindBlock的返回信号。
    // 6.订阅bindBlock的返回信号，就会拿到绑定信号的订阅者，把处理完成的信号内容发送出来。

       [[_textField.rac_textSignal map:^id(id value) {
        // 当源信号发出，就会调用这个block，修改源信号的内容
        // 返回值：就是处理完源信号的内容。
        return [NSString stringWithFormat:@"输出:%@",value];
    }] subscribeNext:^(id x) {

        NSLog(@"%@",x);
    }];


```

*   `FlatternMap和Map的区别`
    * 1.FlatternMap中的Block返回信号。
    * 2.Map中的Block返回对象。
    * 3.开发中，如果信号发出的值不是信号，映射一般使用Map
    * 4.开发中，如果信号发出的值是信号，映射一般使用FlatternMap。

*  `总结`：signalOfsignals用FlatternMap。

```
    // 创建信号中的信号
    RACSubject *signalOfsignals = [RACSubject subject];
    RACSubject *signal = [RACSubject subject];

    [[signalOfsignals flattenMap:^RACStream *(id value) {

     // 当signalOfsignals的signals发出信号才会调用

        return value;

    }] subscribeNext:^(id x) {

        // 只有signalOfsignals的signal发出信号才会调用，因为内部订阅了bindBlock中返回的信号，也就是flattenMap返回的信号。
        // 也就是flattenMap返回的信号发出内容，才会调用。

        NSLog(@"%@aaa",x);
    }];

    // 信号的信号发送信号
    [signalOfsignals sendNext:signal];

    // 信号发送内容
    [signal sendNext:@1];

```

* 9.5 ReactiveCocoa操作方法之组合。
    * `concat`:按一定顺序拼接信号，当多个信号发出的时候，有顺序的接收信号。

```
    RACSignal *signalA = [RACSignal createSignal:^RACDisposable *(id<RACSubscriber> subscriber) {

        [subscriber sendNext:@1];

        [subscriber sendCompleted];

        return nil;
    }];
    RACSignal *signalB = [RACSignal createSignal:^RACDisposable *(id<RACSubscriber> subscriber) {

        [subscriber sendNext:@2];

        return nil;
    }];

    // 把signalA拼接到signalB后，signalA发送完成，signalB才会被激活。
    RACSignal *concatSignal = [signalA concat:signalB];

    // 以后只需要面对拼接信号开发。
    // 订阅拼接的信号，不需要单独订阅signalA，signalB
    // 内部会自动订阅。
    // 注意：第一个信号必须发送完成，第二个信号才会被激活
    [concatSignal subscribeNext:^(id x) {

        NSLog(@"%@",x);

    }];

    // concat底层实现:
    // 1.当拼接信号被订阅，就会调用拼接信号的didSubscribe
    // 2.didSubscribe中，会先订阅第一个源信号（signalA）
    // 3.会执行第一个源信号（signalA）的didSubscribe
    // 4.第一个源信号（signalA）didSubscribe中发送值，就会调用第一个源信号（signalA）订阅者的nextBlock,通过拼接信号的订阅者把值发送出来.
    // 5.第一个源信号（signalA）didSubscribe中发送完成，就会调用第一个源信号（signalA）订阅者的completedBlock,订阅第二个源信号（signalB）这时候才激活（signalB）。
    // 6.订阅第二个源信号（signalB）,执行第二个源信号（signalB）的didSubscribe
    // 7.第二个源信号（signalA）didSubscribe中发送值,就会通过拼接信号的订阅者把值发送出来.

 ```

   * `then`:用于连接两个信号，当第一个信号完成，才会连接then返回的信号。

```
     // then:用于连接两个信号，当第一个信号完成，才会连接then返回的信号
    // 注意使用then，之前信号的值会被忽略掉.
    // 底层实现：1、先过滤掉之前的信号发出的值。2.使用concat连接then返回的信号
    [[[RACSignal createSignal:^RACDisposable *(id<RACSubscriber> subscriber) {

        [subscriber sendNext:@1];
        [subscriber sendCompleted];
        return nil;
    }] then:^RACSignal *{
        return [RACSignal createSignal:^RACDisposable *(id<RACSubscriber> subscriber) {
            [subscriber sendNext:@2];
            return nil;
        }];
    }] subscribeNext:^(id x) {

        // 只能接收到第二个信号的值，也就是then返回信号的值
        NSLog(@"%@",x);
    }];
```


* `merge`:把多个信号合并为一个信号，任何一个信号有新值的时候就会调用.

```
    // merge:把多个信号合并成一个信号
    //创建多个信号
    RACSignal *signalA = [RACSignal createSignal:^RACDisposable *(id<RACSubscriber> subscriber) {

        [subscriber sendNext:@1];


        return nil;
    }];

    RACSignal *signalB = [RACSignal createSignal:^RACDisposable *(id<RACSubscriber> subscriber) {

        [subscriber sendNext:@2];

        return nil;
    }];

    // 合并信号,任何一个信号发送数据，都能监听到.
    RACSignal *mergeSignal = [signalA merge:signalB];

    [mergeSignal subscribeNext:^(id x) {

        NSLog(@"%@",x);

    }];

    // 底层实现：
    // 1.合并信号被订阅的时候，就会遍历所有信号，并且发出这些信号。
    // 2.每发出一个信号，这个信号就会被订阅
    // 3.也就是合并信号一被订阅，就会订阅里面所有的信号。
    // 4.只要有一个信号被发出就会被监听。
```

* `zipWith`:把两个信号压缩成一个信号，只有当两个信号同时发出信号内容时，并且把两个信号的内容合并成一个元组，才会触发压缩流的next事件。

```
     RACSignal *signalA = [RACSignal createSignal:^RACDisposable *(id<RACSubscriber> subscriber) {

        [subscriber sendNext:@1];


        return nil;
    }];

    RACSignal *signalB = [RACSignal createSignal:^RACDisposable *(id<RACSubscriber> subscriber) {

        [subscriber sendNext:@2];

        return nil;
    }];



    // 压缩信号A，信号B
    RACSignal *zipSignal = [signalA zipWith:signalB];

    [zipSignal subscribeNext:^(id x) {

        NSLog(@"%@",x);
    }];

    // 底层实现:
    // 1.定义压缩信号，内部就会自动订阅signalA，signalB
    // 2.每当signalA或者signalB发出信号，就会判断signalA，signalB有没有发出个信号，有就会把最近发出的信号都包装成元组发出。
```

   * `combineLatest`:将多个信号合并起来，并且拿到各个信号的最新的值,必须每个合并的signal至少都有过一次sendNext，才会触发合并的信号。

```
      RACSignal *signalA = [RACSignal createSignal:^RACDisposable *(id<RACSubscriber> subscriber) {

        [subscriber sendNext:@1];

        return nil;
    }];

    RACSignal *signalB = [RACSignal createSignal:^RACDisposable *(id<RACSubscriber> subscriber) {

        [subscriber sendNext:@2];

        return nil;
    }];

    // 把两个信号组合成一个信号,跟zip一样，没什么区别
    RACSignal *combineSignal = [signalA combineLatestWith:signalB];

    [combineSignal subscribeNext:^(id x) {

        NSLog(@"%@",x);
    }];

    // 底层实现：
    // 1.当组合信号被订阅，内部会自动订阅signalA，signalB,必须两个信号都发出内容，才会被触发。
    // 2.并且把两个信号组合成元组发出。
```

* `reduce`聚合:用于信号发出的内容是元组，把信号发出元组的值聚合成一个值

```
     RACSignal *signalA = [RACSignal createSignal:^RACDisposable *(id<RACSubscriber> subscriber) {

        [subscriber sendNext:@1];

        return nil;
    }];

    RACSignal *signalB = [RACSignal createSignal:^RACDisposable *(id<RACSubscriber> subscriber) {

        [subscriber sendNext:@2];

        return nil;
    }];

    // 聚合
    // 常见的用法，（先组合在聚合）。combineLatest:(id<NSFastEnumeration>)signals reduce:(id (^)())reduceBlock
    // reduce中的block简介:
    // reduceblcok中的参数，有多少信号组合，reduceblcok就有多少参数，每个参数就是之前信号发出的内容
    // reduceblcok的返回值：聚合信号之后的内容。
   RACSignal *reduceSignal = [RACSignal combineLatest:@[signalA,signalB] reduce:^id(NSNumber *num1 ,NSNumber *num2){

       return [NSString stringWithFormat:@"%@ %@",num1,num2];

   }];

    [reduceSignal subscribeNext:^(id x) {

        NSLog(@"%@",x);
    }];

    // 底层实现:
    // 1.订阅聚合信号，每次有内容发出，就会执行reduceblcok，把信号内容转换成reduceblcok返回的值。
```


* 9.6 ReactiveCocoa操作方法之过滤。

    * `filter`:过滤信号，使用它可以获取满足条件的信号.

    ```
    // 过滤:
    // 每次信号发出，会先执行过滤条件判断.
    [_textField.rac_textSignal filter:^BOOL(NSString *value) {
            return value.length > 3;
    }];
    ```

    * `ignore`:忽略完某些值的信号.

    ```
        // 内部调用filter过滤，忽略掉ignore的值
    [[_textField.rac_textSignal ignore:@"1"] subscribeNext:^(id x) {

        NSLog(@"%@",x);
    }];

    ```

    * `distinctUntilChanged`:当上一次的值和当前的值有明显的变化就会发出信号，否则会被忽略掉。

    ```
        // 过滤，当上一次和当前的值不一样，就会发出内容。
    // 在开发中，刷新UI经常使用，只有两次数据不一样才需要刷新
    [[_textField.rac_textSignal distinctUntilChanged] subscribeNext:^(id x) {

        NSLog(@"%@",x);
    }];

    ```

    * `take`:从开始一共取N次的信号

    ```
    // 1、创建信号
    RACSubject *signal = [RACSubject subject];

    // 2、处理信号，订阅信号
    [[signal take:1] subscribeNext:^(id x) {

        NSLog(@"%@",x);
    }];

    // 3.发送信号
    [signal sendNext:@1];

    [signal sendNext:@2];
    ```

    * `takeLast`:取最后N次的信号,前提条件，订阅者必须调用完成，因为只有完成，就知道总共有多少信号.

    ```
    // 1、创建信号
    RACSubject *signal = [RACSubject subject];

    // 2、处理信号，订阅信号
    [[signal takeLast:1] subscribeNext:^(id x) {

        NSLog(@"%@",x);
    }];

    // 3.发送信号
    [signal sendNext:@1];

    [signal sendNext:@2];

    [signal sendCompleted];
    ```

    * `takeUntil`:(RACSignal *):获取信号直到执行完这个信号

    ```
    // 监听文本框的改变，知道当前对象被销毁
    [_textField.rac_textSignal takeUntil:self.rac_willDeallocSignal];

    ```


* `skip`:(NSUInteger):跳过几个信号,不接受。

    ```
    // 表示输入第一次，不会被监听到，跳过第一次发出的信号
    [[_textField.rac_textSignal skip:1] subscribeNext:^(id x) {

        NSLog(@"%@",x);
    }];
    ```

    * `switchToLatest`:用于signalOfSignals（信号的信号），有时候信号也会发出信号，会在signalOfSignals中，获取signalOfSignals发送的最新信号。

    ```
    RACSubject *signalOfSignals = [RACSubject subject];
    RACSubject *signal = [RACSubject subject];
    [signalOfSignals sendNext:signal];
    [signal sendNext:@1];

    // 获取信号中信号最近发出信号，订阅最近发出的信号。
    // 注意switchToLatest：只能用于信号中的信号
    [signalOfSignals.switchToLatest subscribeNext:^(id x) {

        NSLog(@"%@",x);
    }];

    ```


* 9.7 ReactiveCocoa操作方法之秩序。
    * `doNext`: 执行Next之前，会先执行这个Block
	* `doCompleted`: 执行sendCompleted之前，会先执行这个Block

	```
 [[[[RACSignal createSignal:^RACDisposable *(id<RACSubscriber> subscriber) {
        [subscriber sendNext:@1];
        [subscriber sendCompleted];
        return nil;
    }] doNext:^(id x) {
      // 执行[subscriber sendNext:@1];之前会调用这个Block
        NSLog(@"doNext");;
    }] doCompleted:^{
         // 执行[subscriber sendCompleted];之前会调用这个Block
        NSLog(@"doCompleted");;

    }] subscribeNext:^(id x) {

        NSLog(@"%@",x);
    }];

    ```
    * 9.8 ReactiveCocoa操作方法之线程。
    * `deliverOn`: 内容传递切换到制定线程中，副作用在原来线程中,把在创建信号时block中的代码称之为副作用。

    * `subscribeOn`: 内容传递和副作用都会切换到制定线程中。

* 9.9 ReactiveCocoa操作方法之时间。
    *  `timeout`：超时，可以让一个信号在一定的时间后，自动报错。

    ```
     RACSignal *signal = [[RACSignal createSignal:^RACDisposable *(id<RACSubscriber> subscriber) {
        return nil;
    }] timeout:1 onScheduler:[RACScheduler currentScheduler]];

    [signal subscribeNext:^(id x) {

        NSLog(@"%@",x);
    } error:^(NSError *error) {
        // 1秒后会自动调用
        NSLog(@"%@",error);
    }];

    ```

    *  `interval` 定时：每隔一段时间发出信号

	```
	[[RACSignal interval:1 onScheduler:[RACScheduler currentScheduler]] subscribeNext:^(id x) {

        NSLog(@"%@",x);
    }];

	```

    *  `delay` 延迟发送next。

    ```
        RACSignal *signal = [[[RACSignal createSignal:^RACDisposable *(id<RACSubscriber> subscriber) {

        [subscriber sendNext:@1];
        return nil;
    }] delay:2] subscribeNext:^(id x) {

        NSLog(@"%@",x);
    }];

    ```

* 9.10 ReactiveCocoa操作方法之重复。
    * `retry`重试 ：只要失败，就会重新执行创建信号中的block,直到成功.

```
     __block int i = 0;
    [[[RACSignal createSignal:^RACDisposable *(id<RACSubscriber> subscriber) {

            if (i == 10) {
                [subscriber sendNext:@1];
            }else{
                NSLog(@"接收到错误");
                [subscriber sendError:nil];
            }
            i++;

        return nil;

    }] retry] subscribeNext:^(id x) {

        NSLog(@"%@",x);

    } error:^(NSError *error) {


    }];
    ```

* `replay`重放：当一个信号被多次订阅,反复播放内容

```
        RACSignal *signal = [[RACSignal createSignal:^RACDisposable *(id<RACSubscriber> subscriber) {


        [subscriber sendNext:@1];
        [subscriber sendNext:@2];

        return nil;
    }] replay];

    [signal subscribeNext:^(id x) {

        NSLog(@"第一个订阅者%@",x);

    }];

    [signal subscribeNext:^(id x) {

        NSLog(@"第二个订阅者%@",x);

    }];

```

* `throttle`节流:当某个信号发送比较频繁时，可以使用节流，在某一段时间不发送信号内容，过了一段时间获取信号的最新内容发出。

```
        RACSubject *signal = [RACSubject subject];

    _signal = signal;

    // 节流，在一定时间（1秒）内，不接收任何信号内容，过了这个时间（1秒）获取最后发送的信号内容发出。
    [[signal throttle:1] subscribeNext:^(id x) {

        NSLog(@"%@",x);
    }];

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

```
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

```
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

```
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

```
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


```
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


```
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

