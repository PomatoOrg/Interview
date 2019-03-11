# Objective-C 内存管理

- [堆(heap)和栈(stack)](#堆heap和栈stack)
- [引用计数](#引用计数httpszhwikipediaorgwikie5bc95e794a8e8aea1e695b0)
- [MRC 与 ARC](#mrc-与-arc)
- [管理对象](#管理对象)
  - [(ARC) CF 对象和 OC 对象的内存管理权转换](#arc-cf-对象和-oc-对象的内存管理权转换)
- [NSAutoreleasePool](#nsautoreleasepool)
  - [ARC 下的 autoreleasepool](#arc-下的-autoreleasepool)
  - [MRC 下的 autoreleasepool](#mrc-下的-autoreleasepool)
- [MRC(Mannul Reference Counting)](#mrcmannul-reference-counting)
  - [对象操作的四个类别](#对象操作的四个类别)
  - [四个法则](#四个法则)
- [ARC (Automatic Reference Counting)](#arc-automatic-reference-counting)
  - [变量标识符](#变量标识符)
  - [属性标识符](#属性标识符)
- [参考资料](#参考资料)

## 堆(heap)和栈(stack)

堆(heap): 程序员分配释放
栈(stack):操作系统自动分配释放

## [引用计数](https://zh.wikipedia.org/wiki/%E5%BC%95%E7%94%A8%E8%AE%A1%E6%95%B0)

> 引用计数是计算机编程语言中的一种内存管理技术，是指将资源（可以是对象、内存或磁盘空间等等）的被引用次数保存起来，当被引用次数变为零时就将其释放的过程。使用引用计数技术可以实现自动资源管理的目的。同时引用计数还可以指使用引用计数技术回收未使用资源的垃圾回收算法。
>
> 当创建一个对象的实例并在堆上申请内存时，对象的引用计数就为 1，在其他对象中需要持有这个对象时，就需要把该对象的引用计数加 1，需要释放一个对象时，就将该对象的引用计数减 1，直至对象的引用计数为 0，对象的内存会被立刻释放。

优势:尽快地回收不再被使用的对象，同时在回收过程中不会导致长时间的停顿，还可以清晰地标明每一个对象的生存周期。
缺陷:1. 频繁更新引用计数会降低运行效率。2. 无法解决循环引用

## MRC 与 ARC

iOS 中分为了两种内存管理机制 MRC (Mannul Reference Counting) 和 ARC (Automatic Reference Counting).

## 管理对象

| C-level API     | Objective-C | 派生出的 Objective-C 类型                                              |
| --------------- | ----------- | ---------------------------------------------------------------------- |
| Core Foundation | Foundation  | NSString,NSDictionary,NSSet,NSArray...                                 |
| Core Animation  | UIKit       | UIResponeer(UIView,UIApplication,UIViewController ),UIImage,UITouch... |

通过封装 CoreFoundation、Core Animation 等 C 语言层的 API.**调用 C 语言的 API 分配释放内存,此时的内存放在堆上**
衍生出 Foundation、UIKit 等 Objective-C 层的框架.
OC 层下的框架派生出 Objective-C 类型的对象.

MRC 和 ARC 机制管理谁?

> OC 层下的框架派生出 Objective-C 类型的对象

### (ARC) CF 对象和 OC 对象的内存管理权转换

关键字: `__bridge`、`__bridge_retained`、`__bridge_transfer`

\_\_bridge: OC 指针到 C 指针的转换

\_\_bridge_retained: 释放 ARC 的所有权,进行手动管理,并**引用计数+1**

\_\_bridge_transfer:将内存管理权交给 ARC,并**引用计数-1**

---

## NSAutoreleasePool

一个 Cocoa 框架下的引用计数管理系统.

在`{}`内、执行`[pool release]`,对象就会自动注册到 NSAutoreleasePool.
在`}`、`[pool release]`,NSAutoreleasePool 被销毁之前,统一 release 向其注册的对象.

### ARC 下的 autoreleasepool

```objective-c
@autoreleasepool {
  NSObject *ojb1 = [[NSObject alloc]init]; //自动注册
  NSObject *ojb2 = [[NSObject alloc]init]; //自动注册
}//对ojb1、obj2统一执行release
```

### MRC 下的 autoreleasepool

```objective-c
NSAutoreleasePool pool = [[NSAutoreleasePool alloc] init];

NSObject *ojb1 = [[NSObject alloc]init];
[ojb1 autorelease];//注册到pool

NSObject *ojb2 = [[NSObject alloc]init];
[ojb1 autorelease];//注册到pool

[pool drain]; //对ojb1、obj2统一执行release

[pool release];
```

## MRC(Mannul Reference Counting)

### 对象操作的四个类别

| 对象操作 | 对应的 retainCount | 变化 |
| -------- | ------------------ | ---- |
| 持有对象 | retain             | +1   |
| 释放对象 | release            | -1   |
| 废弃对象 | dealloc            | -    |

### 四个法则

- 自己生成的对象，自己持有。
- 非自己生成的对象，自己也能持有。
- 不在需要自己持有对象的时候，释放。
- 非自己持有的对象无需释放。

## ARC (Automatic Reference Counting)

ARC 是苹果引入的一种自动内存管理机制，会根据引用计数自动监视对象的生存周期，实现方式是在编译时期自动在已有代码中插入合适的内存管理代码以及在 Runtime 做一些优化。

### 变量标识符

| 变量标识符(关键字)    | 作用                                                                                                                                                                                                                  |
| --------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| \_\_strong            | 是默认使用的标识符。只有还有一个强指针指向某个对象，这个对象就会一直存活。                                                                                                                                            |
| \_\_weak              | 声明这个引用不会保持被引用对象的存活，如果对象没有强引用了，弱引用会被置为 nil                                                                                                                                        |
| \_\_unsafe_unretained | 声明这个引用不会保持被引用对象的存活，如果对象没有强引用了，它不会被置为 nil。如果它引用的对象被回收掉了，该指针就变成了野指针。如果你想部署回 iOS 4.0 和 OS X Snow Leopark，你就不得不用\_\_unsafe_unretained 标识符 |
| \_\_autoreleasing     | 用于标示使用引用传值的参数（id \*），在函数返回时会被自动释放掉。                                                                                                                                                     |

### 属性标识符

| 属性标识符(关键字) | 作用                                                                                                                                                                                       |
| ------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| assign             | 表明 setter 仅仅是一个简单的赋值操作，通常用于基本的数值类型，例如 CGFloat 和 NSInteger                                                                                                    |
| retain             | 与 strong 同义                                                                                                                                                                             |
| strong             | 表明属性定义一个拥有者关系。当给属性设定一个新值的时候，首先这个值进行 retain ，旧值进行 release ，然后进行赋值操作                                                                        |
| weak               | 表明属性定义了一个非拥有者关系。当给属性设定一个新值的时候，这个值不会进行 retain，旧值也不会进行 release， 而是进行类似 assign 的操作。不过当属性指向的对象被销毁时，该属性会被置为 nil。 |
| unsafe_unretained  | 与 weak 类似，不过当属性指向的对象被销毁时，该属性变成野指针。                                                                                                                             |
| copy               | 类似于 strong，不过在赋值时进行 copy 操作而不是 retain 操作。通常在需要保留某个不可变对象（NSString 最常见），并且防止它被意外改变时使用。                                                 |

## 参考资料

[hit-alibaba-interview](https://hit-alibaba.github.io/interview/iOS/ObjC-Basic/MM.html)

[维基百科-引用计数](https://hit-alibaba.github.io/interview/iOS/ObjC-Basic/MM.html)

[stackoverflow-Difference between Foundation Framework and Core Foundation Framework?](https://stackoverflow.com/questions/1843251/vdifference-between-foundation-framework-and-core-foundation-framework)

[简书-iOS 开发之\_\_bridge，\_\_bridge_transfer 和\_\_bridge_retained](https://www.jianshu.com/p/11c3bc21f56e)

[Apple-NSAutoreleasePool](https://developer.apple.com/documentation/foundation/nsautoreleasepool)
[stackoverflow-ARC - The meaning of \_\_unsafe_unretained?](https://stackoverflow.com/questions/8592289/arc-the-meaning-of-unsafe-unretained)

[Apple-Transitioning to ARC Release Notes](https://developer.apple.com/library/archive/releasenotes/ObjectiveC/RN-TransitioningToARC/Introduction/Introduction.html)
