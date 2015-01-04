---
layout: post
title: "ios7 在自定义leftBarButtonItem情况下的右滑返回问题"
description: "iOS7 右滑返回 自定义返回按钮"
date: 2014-12-07 20:35:54 +0800
comments: true
categories: iOS 技术点
---

最近在做项目时发现一个问题：在自定义navigation 的 leftBarButtonItem后，右滑pop 手势失效了，google 了一把，解决过程如下，在此记录下：

问题：

```objective-c

- (void)viewDidLoad
{
  self.navigationItem.leftBarButtonItem = [self backButton];
}

- (UIBarButtonItem *)backButton
{
  UIImage *image = [UIImage imageNamed:@"back_button"];
  CGRect buttonFrame = CGRectMake(0, 0, image.size.width, image.size.height);

  UIButton *button = [[UIButton alloc] initWithFrame:buttonFrame];
  [button addTarget:self action:@selector(backButtonPressed) forControlEvents:UIControlEventTouchUpInside];
  [button setImage:[UIImage imageNamed:normalImage] forState:UIControlStateNormal];

  UIBarButtonItem *item; = [[UIBarButtonItem alloc] initWithCustomView:button];

  return item;
}
```
<!--more-->

当写完上述代码后，就会发现，iOS7提供的右滑返回手势不起作用了，这篇[ios 7 Tips](http://stuartkhall.com/posts/ios-7-development-tips-tricks-hacks)文章中给出了一个快速的解决方法：

```objective-c

self.navigationItem.leftBarButtonItem = [[UIBarButtonItem alloc]
                                             initWithImage:img
                                             style:UIBarButtonItemStylePlain
                                             target:self
                                             action:@selector(onBack:)];
self.navigationController.interactivePopGestureRecognizer.delegate = (id<UIGestureRecognizerDelegate>)self;

```

经过测试发现当 push 一个 viewController后快速 右滑返回会导致崩溃， 也就是说在当push动画还没完成时去滑动返回， navigation controller 还在引用 viewController， 调试模式下回看到如下log:

*nested pop animation can result in corrupted navigation bar*

下来就是想办法在动画过程中禁止滑动手势，于是就有了下面的解决方法：

```objective-c
@interface BaseNavigationController : UINavigationController <UINavigationControllerDelegate, UIGestureRecognizerDelegate>
@end

@implementation BaseNavigationController

- (void)viewDidLoad
{
  __weak BaseNavigationController *weakSelf = self;

  if ([self respondsToSelector:@selector(interactivePopGestureRecognizer)])
  {
    self.interactivePopGestureRecognizer.delegate = weakSelf;
    self.delegate = weakSelf;
  }
}

// Hijack the push method to disable the gesture

- (void)pushViewController:(UIViewController *)viewController animated:(BOOL)animated
{
  if ([self respondsToSelector:@selector(interactivePopGestureRecognizer)])
    self.interactivePopGestureRecognizer.enabled = NO;

  [super pushViewController:viewController animated:animated];
}

#pragma mark UINavigationControllerDelegate

- (void)navigationController:(UINavigationController *)navigationController
       didShowViewController:(UIViewController *)viewController
                    animated:(BOOL)animate
{
  // Enable the gesture again once the new controller is shown

  if ([self respondsToSelector:@selector(interactivePopGestureRecognizer)])
    self.interactivePopGestureRecognizer.enabled = YES;
}
@end

```

** 参考链接 ** 

- <u>http://stuartkhall.com/posts/ios-7-development-tips-tricks-hacks </u>
- <u>http://keighl.com/post/ios7-interactive-pop-gesture-custom-back-button/ </u>

