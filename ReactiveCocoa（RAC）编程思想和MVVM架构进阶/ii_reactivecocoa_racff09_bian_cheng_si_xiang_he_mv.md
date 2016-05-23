# (II)  ReactiveCocoa（RAC）编程思想和MVVM架构进阶

 
---
```objc
Update更新：2016年5月20日 By {MISSAJJ琴瑟静听}
由于此章节笔记内容比较多，所以特地分了五篇文章，
避免因文章太长而导致手机浏览器或微信内浏览崩溃
```


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

```objc

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

```objc
	// 只要文本框文字改变，就会修改label的文字
    RAC(self.labelView,text) = _textField.rac_textSignal;

```

8.2 `RACObserve(self, name) `:监听某个对象的某个属性,返回的是信号。

`基本用法`

```objc
[RACObserve(self.view, center) subscribeNext:^(id x) {

        NSLog(@"%@",x);
    }];

```

8.3  `@weakify(Obj)和@strongify(Obj)`,一般两个都是配套使用,解决循环引用问题.

8.4 `RACTuplePack`：把数据包装成RACTuple（元组类）

`基本用法`
```objc
	// 把参数中的数据包装成元组
    RACTuple *tuple = RACTuplePack(@10,@20);
```



8.5 `RACTupleUnpack`：把RACTuple（元组类）解包成对应的数据。

`基本用法`
```objc
	// 把参数中的数据包装成元组
    RACTuple *tuple = RACTuplePack(@"xmg",@20);

    // 解包元组，会把元组的值，按顺序给参数里面的变量赋值
    // name = @"xmg" age = @20
    RACTupleUnpack(NSString *name,NSNumber *age) = tuple;
```

