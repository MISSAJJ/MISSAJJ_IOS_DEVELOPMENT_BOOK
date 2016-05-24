# assign和weak的区别

 
---
```objc
Update更新：2016年5月9日 By {MISSAJJ琴瑟静听}
```


- 本质区别
    - 速度比较: __unsafe_unretained > __weak

```objc
@property (nonatomic, assign) XMGDog *dog;  
// XMGDog *__unsafe_unretained _dog;

__unsafe_unretained的特点:
1.不是强引用, 不能保住OC对象的命
2.如果引用的OC对象销毁了, 指针并不会被自动清空, 
  依然指向销毁的对象(很容易产生野指针错误: EXC_BAD_ACCESS)

@property (nonatomic, weak) XMGDog *dog;  
// XMGDog * _Nullable __weak _dog;

__weak的特点:
1.不是强引用, 不能保住OC对象的命
2.如果引用的OC对象销毁了, 指针会被自动清空(变为nil), 
  不再指向销毁的对象(永远不会产生野指针错误)
```