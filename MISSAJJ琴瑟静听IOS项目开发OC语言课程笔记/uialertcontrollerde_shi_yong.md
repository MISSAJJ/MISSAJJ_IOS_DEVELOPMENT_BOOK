# UIAlertController的使用

 
---
```objc
Update更新：2016年5月9日 By {MISSAJJ琴瑟静听}
```


```objc
UIAlertController *controller = [UIAlertController alertControllerWithTitle:nil message:nil preferredStyle:UIAlertControllerStyleActionSheet];

[controller addAction:[UIAlertAction actionWithTitle:@"收藏" style:UIAlertActionStyleDefault handler:^(UIAlertAction * _Nonnull action) {
    NSLog(@"点击了[收藏]按钮");
}]];

[controller addAction:[UIAlertAction actionWithTitle:@"举报" style:UIAlertActionStyleDestructive handler:^(UIAlertAction * _Nonnull action) {
    NSLog(@"点击了[举报]按钮");
}]];

[controller addAction:[UIAlertAction actionWithTitle:@"取消" style:UIAlertActionStyleCancel handler:^(UIAlertAction * _Nonnull action) {
    NSLog(@"点击了[取消]按钮");
}]];

//    [controller addTextFieldWithConfigurationHandler:^(UITextField * _Nonnull textField) {
//        textField.textColor = [UIColor redColor];
//    }];

[self.window.rootViewController presentViewController:controller animated:YES completion:nil];
```