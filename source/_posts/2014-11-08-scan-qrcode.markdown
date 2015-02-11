---
layout: post
title: "iOS使用AVFoundation实现二维码扫描"
description: "ios AVFoundation 二维码扫描 "
date: 2014-11-08 14:30:57 +0800
comments: true
categories: IOS开发
---
关于二维码扫描有不少优秀第三方库如：

+ [ZBar SDK](http://zbar.sourceforge.net/iphone/sdkdoc/index.html) 里面有详细的文档，相应介绍也非常多，如：<u>http://rdcworld-iphone.blogspot.in/2013/03/how-to-use-barcode-scanner-br-and-qr-in.html</u>

+ [ZXing](https://github.com/zxing/zxing) google推出的开源项目，相应介绍如：<u>http://blog.devtang.com/blog/2012/12/23/use-zxing-library/</u>

最近项目需要，看了下使用ios7自带的 AVFoundation Framework 来实现二维码扫描，Demo 见：[scan_qrcode_demo](https://github.com/strivingboy/scan_qrcode_demo.git)

**关于AVFoundation**

<!--more-->

AVFoundation 是一个很大基础库，用来创建基于时间的视听媒体，可以使用它来检查,创建、编辑或媒体文件。也可以输入流从设备和操作视频实时捕捉和回放。详细框架介绍见官网：[About AV Foundation](https://developer.apple.com/library/mac/documentation/AudioVideo/Conceptual/AVFoundationPG/Articles/00_Introduction.html)，本文只是介绍如果使用AVFoundation获取二维码。

首先获取流媒体信息我们需要`AVCaptureSession`对象来管理输入流和输出流，`AVCaptureVideoPreviewLayer`对象来显示信息，基本流程如下图所示：

![scan_qr_code_flow](http://strivingboy.github.com/images/2014-11-08-flow.jpg)

注：

+ `AVCaptureSession` 管理输入(AVCaptureInput)和输出(AVCaptureOutput)流，包含开启和停止会话方法。
+ `AVCaptureDeviceInput` 是AVCaptureInput的子类,可以作为输入捕获会话，用AVCaptureDevice实例初始化。
+ `AVCaptureDevice` 代表了物理捕获设备如:摄像机。用于配置等底层硬件设置相机的自动对焦模式。
+ `AVCaptureMetadataOutput` 是AVCaptureOutput的子类，处理输出捕获会话。捕获的对象传递给一个委托实现AVCaptureMetadataOutputObjectsDelegate协议。协议方法在指定的派发队列（dispatch queue）上执行。
+ `AVCaptureVideoPreviewLayer`CALayer的一个子类，显示捕获到的相机输出流。

下面看下实现过程如下：

**Step1:**需要导入：AVFoundation Framework 包含头文件：

    #import <AVFoundation/AVFoundation.h>


**Step2:设置捕获会话**

设置 AVCaptureSession 和 AVCaptureVideoPreviewLayer 成员

```objective-c

    #import <AVFoundation/AVFoundation.h>

    static const char *kScanQRCodeQueueName = "ScanQRCodeQueue";

    @interface ViewController () <AVCaptureMetadataOutputObjectsDelegate>
    .....
    @property (nonatomic) AVCaptureSession *captureSession;
    @property (nonatomic) AVCaptureVideoPreviewLayer *videoPreviewLayer;
    @property (nonatomic) BOOL lastResult;
    @end

```

**Step3:创建会话，读取输入流**

```objective-c

    - (BOOL)startReading
    {
        // 获取 AVCaptureDevice 实例
        NSError * error;
        AVCaptureDevice *captureDevice = [AVCaptureDevice defaultDeviceWithMediaType:AVMediaTypeVideo];
        // 初始化输入流
        AVCaptureDeviceInput *input = [AVCaptureDeviceInput deviceInputWithDevice:captureDevice error:&error];
        if (!input) {
            NSLog(@"%@", [error localizedDescription]);
            return NO;
        }
        // 创建会话
        _captureSession = [[AVCaptureSession alloc] init];
        // 添加输入流
        [_captureSession addInput:input];
        // 初始化输出流
        AVCaptureMetadataOutput *captureMetadataOutput = [[AVCaptureMetadataOutput alloc] init];
        // 添加输出流
        [_captureSession addOutput:captureMetadataOutput];
        
        // 创建dispatch queue.
        dispatch_queue_t dispatchQueue;
        dispatchQueue = dispatch_queue_create(kScanQRCodeQueueName, NULL);
        [captureMetadataOutput setMetadataObjectsDelegate:self queue:dispatchQueue];
        // 设置元数据类型 AVMetadataObjectTypeQRCode
        [captureMetadataOutput setMetadataObjectTypes:[NSArray arrayWithObject:AVMetadataObjectTypeQRCode]];
        
        // 创建输出对象
        _videoPreviewLayer = [[AVCaptureVideoPreviewLayer alloc] initWithSession:_captureSession];
        [_videoPreviewLayer setVideoGravity:AVLayerVideoGravityResizeAspectFill];
        [_videoPreviewLayer setFrame:_sanFrameView.layer.bounds];
        [_sanFrameView.layer addSublayer:_videoPreviewLayer];
        // 开始会话
        [_captureSession startRunning];
        
        return YES;
    }

```

**Step4:停止读取**

```objective-c

    - (void)stopReading
    {
        // 停止会话
        [_captureSession stopRunning];
        _captureSession = nil;
    }
    
```

**Step5:获取捕获数据**

```objective-c
    
    -(void)captureOutput:(AVCaptureOutput *)captureOutput didOutputMetadataObjects:(NSArray *)metadataObjects
      fromConnection:(AVCaptureConnection *)connection
	{
	    if (metadataObjects != nil && [metadataObjects count] > 0) {
	        AVMetadataMachineReadableCodeObject *metadataObj = [metadataObjects objectAtIndex:0];
	        NSString *result;
	        if ([[metadataObj type] isEqualToString:AVMetadataObjectTypeQRCode]) {
	            result = metadataObj.stringValue;
	        } else {
	            NSLog(@"不是二维码");
	        }
	        [self performSelectorOnMainThread:@selector(reportScanResult:) withObject:result waitUntilDone:NO];
	    }
	}

```

**Step6:处理结果**

```objective-c

	- (void)reportScanResult:(NSString *)result
	{
	    [self stopReading];
	    if (!_lastResult) {
	        return;
	    }
	    _lastResut = NO;
	    UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"二维码扫描"
	                                                    message:result
	                                                   delegate:nil
	                                          cancelButtonTitle:@"取消"
	                                          otherButtonTitles: nil];
	    [alert show];
	    // 以下处理了结果，继续下次扫描
	    _lastResult = YES;
	}

```

以上基本就是二维码的获取流程，和扫一扫二维码伴随的就是开启系统照明，这个比较简单，也是利用 `AVCaptureDevice`,请看如下实现：

```objective-c

	- (void)systemLightSwitch:(BOOL)open
	{
	    AVCaptureDevice *device = [AVCaptureDevice defaultDeviceWithMediaType:AVMediaTypeVideo];
	    if ([device hasTorch]) {
	        [device lockForConfiguration:nil];
	        if (open) {
	            [device setTorchMode:AVCaptureTorchModeOn];
	        } else {
	            [device setTorchMode:AVCaptureTorchModeOff];
	        }
	        [device unlockForConfiguration];
	    }
	}


```

以上就是本文介绍的大部分内容，详细代码请看demo [scan_qrcode_deomo](https://github.com/strivingboy/scan_qrcode_demo.git)

实现过程中遇到一下两个问题：

1、扫描一个二维码，输出流回重复调用，代理方法头文件介绍：

```objective-c

	 /*!
	 @method captureOutput:didOutputMetadataObjects:fromConnection:
	 .....	
	 @discussion
	    Delegates receive this message whenever the output captures and emits new objects, as specified by
	    its metadataObjectTypes property. Delegates can use the provided objects in conjunction with other APIs
	    for further processing. This method will be called on the dispatch queue specified by the output's
	    metadataObjectsCallbackQueue property. **This method may be called frequently** so it must be efficient to 
	    prevent capture performance problems, including dropped metadata objects.
	
	    Clients that need to reference metadata objects outside of the scope of this method must retain them and
	    then release them when they are finished with them.
	*/

```

代理方法会频繁调用，我暂且用一个标记（@property (nonatomic) BOOL lastResult）表示是否是第一次扫描成功，来处理。

2、AVFoundation 
该库不能扫描相册中的二维码图片，不知为啥苹果没有支持，有知道实现的麻烦告诉我哈。


** 参考链接 ** 

- <u>http://useyourloaf.com/blog/2014/05/13/reading-qr-codes.html</u>

- <u>http://www.appcoda.com/qr-code-ios-programming-tutorial/ </u>