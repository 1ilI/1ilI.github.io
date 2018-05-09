---
layout: post
title: "3DTouch 之与 MLeaksFinder 反应"
categories: iOS
tags: iOS
---

* content
{:toc}

![leaks](https://raw.githubusercontent.com/1ilI/1ilI.github.io/master/resource/2018-05/3DTouch-iOS11-leaks.gif)
<p align="center">在 iOS11.3 下，检测到了 3DTouch Pop 时的内存泄漏。</p>






## MLeaksFinder 
[MLeaksFinder](https://github.com/Tencent/MLeaksFinder) 是腾讯系团队出的一款针对 iOS 平台的自动化内存泄漏检测工具，介绍及使用方式等详见链接。

## 背景
偶然发现在之前的项目中 MLeaksFinder 有报内存泄漏，而且只有在 3DTouch Pop 才会发生，很纳闷。刚适配 3DTouch 的时候，好像也没有发现这种情况，一度怀疑是新页面上后加的逻辑所出现的问题，找来找去无果，遂新建了工程，里面只有 3DTouch 的逻辑，看看会不会还有泄漏。<br/>
结果是 **在 iOS9.3 下无内存泄漏， iOS10.3.1 和 iOS 11.3 下均有泄漏**。（我只下了这几个版本，其他的未测）
<img alt="iPhone-simulator-os" src="https://raw.githubusercontent.com/1ilI/1ilI.github.io/master/resource/2018-05/iPhone-simulator-os.png" height="300px"/>

## UIKit Peek and Pop
3DTouch 是苹果在 iOS9 时加入的一项划时代的功能，具体就不表了，今天说的是 3DTouch 在 UIKit 内的 Peek 和 Pop 功能，介绍及使用等详见 [开发者文档](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/Adopting3DTouchOniPhone/) 。

我们这边集成主要就是 注册继承及实现 `UIViewControllerPreviewingDelegate`

<div align="center"> <img alt="show-me-code" src="https://raw.githubusercontent.com/1ilI/1ilI.github.io/master/resource/2018-04/show-me-code.png" width="200px"/> </div>

* 先检查可用后注册 3DTouch

```objc
    if ([self.traitCollection respondsToSelector:@selector(forceTouchCapability)] &&
        (self.traitCollection.forceTouchCapability == UIForceTouchCapabilityAvailable)) {
        [self registerForPreviewingWithDelegate:self sourceView:self.tableView];
    }
```

* 然后实现相关代理方法

```objc
//peek 预览
- (nullable UIViewController *)previewingContext:(id <UIViewControllerPreviewing>)previewingContext viewControllerForLocation:(CGPoint)location {
    if ([self.presentedViewController isKindOfClass:[BViewController class]]) {
        return nil;
    }
    else {
        UITableView *tableView = (UITableView *)previewingContext.sourceView;
        NSIndexPath *indexPath = [tableView indexPathForRowAtPoint:location];
        UITableViewCell *cell = [tableView cellForRowAtIndexPath:indexPath];
        if (!cell) {
            return nil;
        }
        //设置不被虚化的范围
        previewingContext.sourceRect = cell.frame;
        BViewController *previewingVC = [[BViewController alloc] init];
        previewingVC.parameter = [NSString stringWithFormat:@"BVC--->%ld",indexPath.row];
        //可调整预览视图的大小
        previewingVC.preferredContentSize = CGSizeMake([UIScreen mainScreen].bounds.size.width, [UIScreen mainScreen].bounds.size.height/(indexPath.row%2 + 1));
        return previewingVC;
    }
}
```

```objc
//Pop 进入
- (void)previewingContext:(id <UIViewControllerPreviewing>)previewingContext commitViewController:(nonnull UIViewController *)viewControllerToCommit {
    [self showViewController:viewControllerToCommit sender:self];
}
```

* 若想上滑预览图出现选项，则在 Pop 的页面实现 `previewActionItems` 方法，例：

```objc
- (NSArray<id<UIPreviewActionItem>> *)previewActionItems {
    UIPreviewAction *action1 = [UIPreviewAction actionWithTitle:@"Selected" style:UIPreviewActionStyleSelected handler:^(UIPreviewAction * _Nonnull action, UIViewController * _Nonnull previewViewController) {
        NSLog(@"点击了Selected");
    }];
    
    UIPreviewAction *action2 = [UIPreviewAction actionWithTitle:@"Destructive" style:UIPreviewActionStyleDestructive handler:^(UIPreviewAction * _Nonnull action, UIViewController * _Nonnull previewViewController) {
        NSLog(@"点击了Destructive");
    }];
    
    UIPreviewAction *group1 = [UIPreviewAction actionWithTitle:@"选项一" style:UIPreviewActionStyleDefault handler:^(UIPreviewAction * _Nonnull action, UIViewController * _Nonnull previewViewController) {
        NSLog(@"点击了选项一");
    }];
    UIPreviewAction *group2 = [UIPreviewAction actionWithTitle:@"选项二" style:UIPreviewActionStyleDefault handler:^(UIPreviewAction * _Nonnull action, UIViewController * _Nonnull previewViewController) {
        NSLog(@"点击了选项二");
    }];
    UIPreviewActionGroup *group = [UIPreviewActionGroup actionGroupWithTitle:@"更多选项" style:UIPreviewActionStyleDefault actions:@[group1,group2]];
    
    return @[action1,action2,group];
}
```

## 问题

上面的写法看起来好像都没有什么问题，而且我也是下载了 [开发者指南和样例代码](https://developer.apple.com/library/content/samplecode/ViewControllerPreviews/Introduction/Intro.html#//apple_ref/doc/uid/TP40016546) 上的 swift 工程作为参考，但是不论是我自己写的 demo 还是下载的样例工程，在加入了 MLeaksFinder 后均能发现在 iOS10.3.1 和 iOS 11.3 下有泄漏，和开头一样。

## 分析

作为分析，我在 Pop 的页面 `BViewController` 里打印了其各个生命周期函数和内存地址

在重按 Pop 后，控制台输出如下：
![clonsole-log](https://raw.githubusercontent.com/1ilI/1ilI.github.io/master/resource/2018-05/pop-console-log.png)

初步分析下 `Memory Leak` 的输出其实应该是前5行走完周期后，最后可能走的 `dealloc` 函数，前5行是 Peek 预览时走的，后面是除了异常是 Pop 跳转后输出的。

因而猜想是不是 **Pop 又引用了 Peek 的 BViewController ，同样的内存地址，因而是 Pop 持有了 Peek 的对象，Peek 要释放时，引用计数器不为空，走不了 dealloc ，然后 MLeaksFinder 就报异常了。**

也就是说，在 Pop 的代理函数里，这么写可能有问题。同样样例代码里也是这么写的

```objc
[self showViewController:viewControllerToCommit sender:self];
```

```swift
//样例代码
show(viewControllerToCommit, sender: self)
```

然后也试了用 push 或 present 的方式跳转过去，依然有问题

```objc
[self.navigationController pushViewController:viewControllerToCommit animated:YES];
//或
[self presentViewController:viewControllerToCommit animated:YES completion:nil];
```

## 解决

既然 Pop 时还持有 Peek 的类，不让它持有 Peek 的 ViewController 不就完了。在 Pop 函数里创建一个新的 BViewController ，和 Peek 里的 BViewController 内存地址不一样，最后像这样：

```objc
BViewController *pushVC = [[BViewController alloc] init];
pushVC.parameter = [(BViewController *)viewControllerToCommit parameter];
[self.navigationController pushViewController:pushVC animated:YES];
```

![no-leaks](https://raw.githubusercontent.com/1ilI/1ilI.github.io/master/resource/2018-05/3DTouch-iOS11-noleaks.gif)

