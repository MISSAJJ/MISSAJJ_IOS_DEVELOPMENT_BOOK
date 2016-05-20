#Chapter-03 相机相簿存图UIImagePicker SDK
---
```objc
Update更新：2016年5月9日 By {MISSAJJ琴瑟静听}
```
## 获得自定义的所有相簿
```objc
// 获得所有的自定义相簿
PHFetchResult<PHAssetCollection *> *assetCollections = [PHAssetCollection fetchAssetCollectionsWithType:PHAssetCollectionTypeAlbum subtype:PHAssetCollectionSubtypeAlbumRegular options:nil];
// 遍历所有的自定义相簿
for (PHAssetCollection *assetCollection in assetCollections) {

}
```

## 获得相机胶卷相簿
```objc
// 获得相机胶卷
PHAssetCollection *cameraRoll = [PHAssetCollection fetchAssetCollectionsWithType:PHAssetCollectionTypeSmartAlbum subtype:PHAssetCollectionSubtypeSmartAlbumUserLibrary options:nil].lastObject;
```

## 获得某个相簿的缩略图
```objc
PHImageRequestOptions *options = [[PHImageRequestOptions alloc] init];
// 同步获得图片, 只会返回1张图片
options.synchronous = YES;

// 获得某个相簿中的所有PHAsset对象
PHFetchResult<PHAsset *> *assets = [PHAsset fetchAssetsInAssetCollection:assetCollection options:nil];
for (PHAsset *asset in assets) {
    CGSize size = CGSizeZero;

    // 从asset中获得图片
    [[PHImageManager defaultManager] requestImageForAsset:asset targetSize:size contentMode:PHImageContentModeDefault options:options resultHandler:^(UIImage * _Nullable result, NSDictionary * _Nullable info) {
        NSLog(@"%@", result);
    }];
}
```

## 获得某个相簿的原图
```objc
PHImageRequestOptions *options = [[PHImageRequestOptions alloc] init];
// 同步获得图片, 只会返回1张图片
options.synchronous = YES;

// 获得某个相簿中的所有PHAsset对象
PHFetchResult<PHAsset *> *assets = [PHAsset fetchAssetsInAssetCollection:assetCollection options:nil];
for (PHAsset *asset in assets) {
    CGSize size = CGSizeMake(asset.pixelWidth, asset.pixelHeight);

    // 从asset中获得图片
    [[PHImageManager defaultManager] requestImageForAsset:asset targetSize:size contentMode:PHImageContentModeDefault options:options resultHandler:^(UIImage * _Nullable result, NSDictionary * _Nullable info) {
        NSLog(@"%@", result);
    }];
}
```

## 利用UIImagePickerController挑选图片
```objc
// UIImagePickerController : 可以从系统自带的App(照片\相机)中获得图片

// 判断相册是否可以打开
if (![UIImagePickerController isSourceTypeAvailable:UIImagePickerControllerSourceTypePhotoLibrary]) return;

UIImagePickerController *ipc = [[UIImagePickerController alloc] init];
// 打开照片应用(显示所有相簿)
ipc.sourceType = UIImagePickerControllerSourceTypePhotoLibrary;
// 打开照片应用(只显示"时刻"这个相簿)
// ipc.sourceType = UIImagePickerControllerSourceTypeSavedPhotosAlbum;
// 照相机
// ipc.sourceType = UIImagePickerControllerSourceTypeCamera;
ipc.delegate = self;
[self presentViewController:ipc animated:YES completion:nil];

#pragma mark - <UIImagePickerControllerDelegate>
- (void)imagePickerController:(UIImagePickerController *)picker didFinishPickingMediaWithInfo:(NSDictionary<NSString *,id> *)info
{
    // 销毁控制器
    [picker dismissViewControllerAnimated:YES completion:nil];

    // 设置图片
    self.imageView.image = info[UIImagePickerControllerOriginalImage];
}
```

## NaN错误
- 错误起因:0被当做除数, 比如 10 / 0

## 最简单的方法保存图片到相机胶卷
```objc
UIImageWriteToSavedPhotosAlbum(self.imageView.image, self, @selector(image:didFinishSavingWithError:contextInfo:), nil);

/**
 *  通过UIImageWriteToSavedPhotosAlbum函数写入图片完毕后就会调用这个方法
 *
 *  @param image       写入的图片
 *  @param error       错误信息
 *  @param contextInfo UIImageWriteToSavedPhotosAlbum函数的最后一个参数
 */
- (void)image:(UIImage *)image didFinishSavingWithError:(NSError *)error contextInfo:(void *)contextInfo
{
    if (error) {
        [SVProgressHUD showErrorWithStatus:@"图片保存失败!"];
    } else {
        [SVProgressHUD showSuccessWithStatus:@"图片保存成功!"];
    }
}
```

## 保存图片到自定义相册
```objc
- (IBAction)save {
    /*
     PHAuthorizationStatusNotDetermined,     用户还没有做出选择
     PHAuthorizationStatusDenied,            用户拒绝当前应用访问相册(用户当初点击了"不允许")
     PHAuthorizationStatusAuthorized         用户允许当前应用访问相册(用户当初点击了"好")
     PHAuthorizationStatusRestricted,        因为家长控制, 导致应用无法方法相册(跟用户的选择没有关系)
     */

    // 判断授权状态
    PHAuthorizationStatus status = [PHPhotoLibrary authorizationStatus];
    if (status == PHAuthorizationStatusRestricted) { // 因为家长控制, 导致应用无法方法相册(跟用户的选择没有关系)
        [SVProgressHUD showErrorWithStatus:@"因为系统原因, 无法访问相册"];
    } else if (status == PHAuthorizationStatusDenied) { // 用户拒绝当前应用访问相册(用户当初点击了"不允许")
        XMGLog(@"提醒用户去[设置-隐私-照片-xxx]打开访问开关");
    } else if (status == PHAuthorizationStatusAuthorized) { // 用户允许当前应用访问相册(用户当初点击了"好")
        [self saveImage];
    } else if (status == PHAuthorizationStatusNotDetermined) { // 用户还没有做出选择
        // 弹框请求用户授权
        [PHPhotoLibrary requestAuthorization:^(PHAuthorizationStatus status) {
            if (status == PHAuthorizationStatusAuthorized) { // 用户点击了好
                [self saveImage];
            }
        }];
    }
}

- (void)saveImage
{
    // PHAsset : 一个资源, 比如一张图片\一段视频
    // PHAssetCollection : 一个相簿

    // PHAsset的标识, 利用这个标识可以找到对应的PHAsset对象(图片对象)
    __block NSString *assetLocalIdentifier = nil;

    // 如果想对"相册"进行修改(增删改), 那么修改代码必须放在[PHPhotoLibrary sharedPhotoLibrary]的performChanges方法的block中
    [[PHPhotoLibrary sharedPhotoLibrary] performChanges:^{
        // 1.保存图片A到"相机胶卷"中
        // 创建图片的请求
        assetLocalIdentifier = [PHAssetCreationRequest creationRequestForAssetFromImage:self.imageView.image].placeholderForCreatedAsset.localIdentifier;
    } completionHandler:^(BOOL success, NSError * _Nullable error) {
        if (success == NO) {
            [self showError:@"保存图片失败!"];
            return;
        }

        // 2.获得相簿
        PHAssetCollection *createdAssetCollection = [self createdAssetCollection];
        if (createdAssetCollection == nil) {
            [self showError:@"创建相簿失败!"];
            return;
        }

        [[PHPhotoLibrary sharedPhotoLibrary] performChanges:^{
            // 3.添加"相机胶卷"中的图片A到"相簿"D中

            // 获得图片
            PHAsset *asset = [PHAsset fetchAssetsWithLocalIdentifiers:@[assetLocalIdentifier] options:nil].lastObject;

            // 添加图片到相簿中的请求
            PHAssetCollectionChangeRequest *request = [PHAssetCollectionChangeRequest changeRequestForAssetCollection:createdAssetCollection];

            // 添加图片到相簿
            [request addAssets:@[asset]];
        } completionHandler:^(BOOL success, NSError * _Nullable error) {
            if (success == NO) {
                [self showError:@"保存图片失败!"];;
            } else {
                [self showSuccess:@"保存图片成功!"];;
            }
        }];
    }];
}

/**
 *  获得相簿
 */
- (PHAssetCollection *)createdAssetCollection
{
    // 从已存在相簿中查找这个应用对应的相簿
    PHFetchResult<PHAssetCollection *> *assetCollections = [PHAssetCollection fetchAssetCollectionsWithType:PHAssetCollectionTypeAlbum subtype:PHAssetCollectionSubtypeAlbumRegular options:nil];
    for (PHAssetCollection *assetCollection in assetCollections) {
        if ([assetCollection.localizedTitle isEqualToString:XMGAssetCollectionTitle]) {
            return assetCollection;
        }
    }

    // 没有找到对应的相簿, 得创建新的相簿

    // 错误信息
    NSError *error = nil;

    // PHAssetCollection的标识, 利用这个标识可以找到对应的PHAssetCollection对象(相簿对象)
    __block NSString *assetCollectionLocalIdentifier = nil;
    [[PHPhotoLibrary sharedPhotoLibrary] performChangesAndWait:^{
        // 创建相簿的请求
        assetCollectionLocalIdentifier = [PHAssetCollectionChangeRequest creationRequestForAssetCollectionWithTitle:XMGAssetCollectionTitle].placeholderForCreatedAssetCollection.localIdentifier;
    } error:&error];

    // 如果有错误信息
    if (error) return nil;

    // 获得刚才创建的相簿
    return [PHAssetCollection fetchAssetCollectionsWithLocalIdentifiers:@[assetCollectionLocalIdentifier] options:nil].lastObject;
}

- (void)showSuccess:(NSString *)text
{
    dispatch_async(dispatch_get_main_queue(), ^{
        [SVProgressHUD showSuccessWithStatus:text];
    });
}

- (void)showError:(NSString *)text
{
    dispatch_async(dispatch_get_main_queue(), ^{
        [SVProgressHUD showErrorWithStatus:text];
    });
}
```


