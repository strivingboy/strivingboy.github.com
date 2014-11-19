---
layout: post
title: "ios 手势（UIGestureRecognizer）操作"
description: "ios UIGestureRecognizer 手势操作 "
date: 2014-11-15 14:14:19 +0800
comments: true
categories: IOS开发
---

本文摘自：[AppCoda](http://www.appcoda.com/ios-gesture-recognizers/)

UIKit中包含了UIGestureRecognizer类来处理手势识别，UIGestureRecognizer是一个抽象类，用于检测发生在UIView上预定义的手势。UIGestureRecognizer 提供了一些子类来处理具体的手势行为，如下：

+ UITapGestureRecognizer: 该类处理View上的点击*Tap*操作（任意手指、任意次数的点击）该操作很常见

+ UISwipeGestureRecognizer: 该类处理滑动*Swipe*操作（上、下、左、右）如：照片应用中滑动查看下一张照片

+ UIPanGestureRecognizer: 该类处理拖拽*Pan*操作，如：将一个View从一个点拖拽到另一个点

<!--more-->

+ UIPinchGestureRecognizer: 该类处理向里或向外捏和*Pinch*操作 (任意方向)如：放大或缩小某张图片

+ UIRotationGestureRecognizer: 该类处理旋转*Rotation*操作，如：双指旋转某个view

+ UILongPressGestureRecognizer: 该类处理长按*LongPress*操作

+ UIScreenEdgePanGestureRecognizer:该类是ios7上添加的，看起来像*Pan*手势,它是检测屏幕边缘的pan手势的，系统在某些controller转场的时候会使用这个手势。

所有的手势对象都会执行*perform*一个响应事件*action*,手势对象会带有相关属性设置，如：手指数目，点击次数等，我们将手势处理事件定义如下：
	
		-(void)handleMyTapGestureWithGestureRecognizer:(UITapGestureRecognizer *)gestureRecognizer;

下面简单说下各个手势的例子，详细代码请查看原文,在原文demo上我添加了UIScreenEdgePanGestureRecognizer的例子,见github：[GestureDemo](https://github.com/strivingboy/GestureDemo.git)。

+ UITapGestureRecognizer 代码片段

初始化手势：

```objective-c
	
	// 创建 单个手指单次点击 手势对象
	UITapGestureRecognizer *singleTapGestureRecognizer = [[UITapGestureRecognizer alloc] initWithTarget:self action:@selector(handleSingleTapGesture:)];
    [self.testView addGestureRecognizer:singleTapGestureRecognizer];
    
    // 创建 双手指两次点击 手势对象
    UITapGestureRecognizer *doubleTapGestureRecognizer = [[UITapGestureRecognizer alloc] initWithTarget:self action:@selector(handleDoubleTapGesture:)];
    doubleTapGestureRecognizer.numberOfTapsRequired = 2;
    doubleTapGestureRecognizer.numberOfTouchesRequired = 2;
    [self.testView addGestureRecognizer:doubleTapGestureRecognizer];
	
```

手势响应事件

```objective-c
	
    -(void)handleSingleTapGesture:(UITapGestureRecognizer *)tapGestureRecognizer
    {
	    // 处理单次点击事件
	    ...
	}
	-(void)handleDoubleTapGesture:(UITapGestureRecognizer *)tapGestureRecognizer
    {
		// 处理双手指两次点击事件
		...
	}
	
```

+ UISwipeGestureRecognizer 代码片段

初始化手势：

```objective-c
	
	// 创建 滑动 手势对象
	UISwipeGestureRecognizer *swipeRightOrange = [[UISwipeGestureRecognizer alloc] initWithTarget:self action:@selector(slideToRightWithGestureRecognizer:)];
	// 滑动方向
    swipeRightOrange.direction = UISwipeGestureRecognizerDirectionRight;
        
    [self.viewOrange addGestureRecognizer:swipeRightOrange];
	
```

手势响应事件

```objective-c

	-(void)slideToRightWithGestureRecognizer:(UISwipeGestureRecognizer *)gestureRecognizer
    {
	    // 处理向右滑动事件
	    ...
	}
	
```

+ UIPanGestureRecognizer 代码片段

初始化手势：

```objective-c
	
	// 创建 拖拽 手势对象
	UIPanGestureRecognizer *panGestureRecognizer = [[UIPanGestureRecognizer alloc] initWithTarget:self action:@selector(moveViewWithGestureRecognizer:)];
    [self.testView addGestureRecognizer:panGestureRecognizer];
	
```

手势响应事件

```objective-c

	-(void)moveViewWithGestureRecognizer:(UIPanGestureRecognizer *)panGestureRecognizer
    {
	    // 处理拖拽事件
	    ...
	}
	
```

+ UIPinchGestureRecognizer 代码片段

初始化手势：

```objective-c
	
	// 创建 捏合 手势对象
	UIPinchGestureRecognizer *pinchGestureRecognizer = [[UIPinchGestureRecognizer alloc] initWithTarget:self action:@selector(handlePinchWithGestureRecognizer:)];
    [self.testView addGestureRecognizer:pinchGestureRecognizer];
	
```

手势响应事件

```objective-c
		
	-(void)handlePinchWithGestureRecognizer:(UIPinchGestureRecognizer *)pinchGestureRecognizer
    {
	    // 处理捏合事件
	    ...
	}
	
```

+ UIRotationGestureRecognizer 代码片段

初始化手势：

```objective-c
	
	// 创建 旋转 手势对象
	UIRotationGestureRecognizer *rotationGestureRecognizer = [[UIRotationGestureRecognizer alloc] initWithTarget:self action:@selector(handleRotationWithGestureRecognizer:)];
    [self.testView addGestureRecognizer:rotationGestureRecognizer];
```

手势响应事件

```objective-c
		
	-(void)handleRotationWithGestureRecognizer:(UIRotationGestureRecognizer *)rotationGestureRecognizer
    {
	    // 处理旋转事件
	    ...
	}
	
```

+ UILongPressGestureRecognizer 代码片段

初始化手势：

```objective-c
	
	// 创建 长按 手势对象
	UILongPressGestureRecognizer *longPressGestureRecognizer = [[UILongPressGestureRecognizer alloc] initWithTarget:self action:@selector(handleLongPressWithGestureRecognizer:)];
    [self.testView addGestureRecognizer:longPressGestureRecognizer];

```

手势响应事件

```objective-c
		
	-(void)handleLongPressWithGestureRecognizer:(UILongPressGestureRecognizer *)longPressGestureRecognizer
    {
	    // 处理长按事件
	    ...
	}
	
```

+ UIScreenEdgePanGestureRecognizer 代码片段

初始化手势：

```objective-c
    
     // 创建屏幕边缘手势(优先级高于其他手势)
    UIScreenEdgePanGestureRecognizer *edgePanGestureRecognizer = [[UIScreenEdgePanGestureRecognizer alloc]
                                                                  initWithTarget:self
                                                                  action:@selector(handleEdgePanWithGestureRecognizer:)];
    edgePanGestureRecognizer.edges = UIRectEdgeLeft;           // 左侧边缘响应
    [self.view addGestureRecognizer:edgePanGestureRecognizer]; // view添到self.view上
```

手势响应事件

```objective-c
        
    -(void)handleEdgePanWithGestureRecognizer:(UIScreenEdgePanGestureRecognizer *)gesture
    {
        if(UIGestureRecognizerStateBegan == gesture.state ||
           UIGestureRecognizerStateChanged == gesture.state)
        {
            // 根据被触摸手势的view计算得出坐标值
            CGPoint translation = [gesture translationInView:gesture.view];
            _showView.center = CGPointMake(self.view.bounds.size.width / 2 + translation.x, self.view.bounds.size.height);
        }
    }
    
```



