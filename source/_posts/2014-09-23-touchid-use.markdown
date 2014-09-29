---
layout: post
title: "ios 8 Touch ID 使用"
date: 2014-09-23 13:14:06 +0800
comments: true
categories: IOS开发
---
##Touch ID 介绍
参考：<u>http://www.imore.com/touch-id-ios-8-explained </u>

##Touch ID API

前提：只有在装有iOS8的真机设备才能编译通过。

**Step1).** 使用Touch ID API,首先需要导入:`LocalAuthentication.framework`

```objective-c
	#import <LocalAuthentication/LAContext.h> 
```
<!--more-->
**Step2).** 使用API，我们需要创建一个Authentication Context

```objective-c
	LAContext *myContext = [[LAContext alloc] init];
```

**Step3).** 检查当前Touch ID 是否可用,设备没有TouchID或者TouchID未开启返回false，有TouchID并开启返回true.

```objective-c

	- (BOOL)canEvaluatePolicy:(LAPolicy)policy error:(NSError * __autoreleasing *)error;
```
	
**Step4).** 调用显示验证界面
```objective-c

	- (void)evaluatePolicy:(LAPolicy)policy 
		   localizedReason:(NSString *)localizedReason 
                     reply:(void(^)(BOOL success, NSError *error))reply;
```
localizedReason：根据官方文档必须提供.

reply:验证成功 success == YES, 否则返回error,根据**error.code**可以得到具体的原因.

在`<LocalAuthentication/LAError.h>`头文件中可以看到如下定义：

```objective-c

	typedef NS_ENUM(NSInteger, LAError)
	{
    	/// Authentication was not successful, because user failed to provide valid credentials.
    	LAErrorAuthenticationFailed = kLAErrorAuthenticationFailed,
     
    	/// Authentication was canceled by user (e.g. tapped Cancel button).
    	LAErrorUserCancel           = kLAErrorUserCancel,
     
    	/// Authentication was canceled, because the user tapped the fallback button (Enter Password).
    	LAErrorUserFallback         = kLAErrorUserFallback,
     
    	/// Authentication was canceled by system (e.g. another application went to foreground).
    	LAErrorSystemCancel         = kLAErrorSystemCancel,
     
    	/// Authentication could not start, because passcode is not set on the device.
    	LAErrorPasscodeNotSet       = kLAErrorPasscodeNotSet,
 
    	/// Authentication could not start, because Touch ID is not available on the device.
    	LAErrorTouchIDNotAvailable  = kLAErrorTouchIDNotAvailable,
     
    	/// Authentication could not start, because Touch ID has no enrolled fingers.
    	LAErrorTouchIDNotEnrolled   = kLAErrorTouchIDNotEnrolled,
	} NS_ENUM_AVAILABLE(10_10, 8_0);
```

##Touch ID API 简单封装Demo

**TouchIdUtil.h**

```objective-c

    typedef NS_ENUM(NSInteger, TouchIdEvaluateResult)
    {
        kTouchIdEvaluateResultSuccess,   // 验证成功
        kTouchIdEvaluateResultFailed,    // 验证失败
        kTouchIdEvaluateResultCancel,    // 点击取消按钮
        kTouchIdEvaluateResultFallback,  // 点击回退按钮
        kTouchIdEvaluateResultOther      // 未知结果
    };
    
    typedef void(^TouchIdEvaluateCallback)(TouchIdEvaluateResult result);
    
    @interface TouchIdUtil : NSObject
    
    + (instancetype)sharedInstance;

    // Touch Id 是否开启或设置
    - (BOOL)canEvaluatePolicy;

    // Touch Id 验证 callback回调已经抛到了主线程
    - (void)evaluatePolicy:(NSString *)localizedReasion
             fallbackTitle:(NSString *)title
                  callback:(TouchIdEvaluateCallback)cb;

    @end
```
**TouchIdUtil.m**

```objective-c
	
	#import "TouchIdUtil.h"
	#import <LocalAuthentication/LocalAuthentication.h>
	
	@implementation TouchIdUtil
	
	+ (instancetype)sharedInstance
    {
        static TouchIdUtil* instance = nil;
        static dispatch_once_t onceToken;
        dispatch_once(&onceToken, ^{
            instance = [[TouchIdUtil alloc] init];
        });
        return instance;
    }

    - (BOOL)canEvaluatePolicy
    {
        LAContext *context = [[LAContext alloc] init];
        NSError *error;
        return [context canEvaluatePolicy: LAPolicyDeviceOwnerAuthenticationWithBiometrics error:&error];
    }

    - (void)evaluatePolicy:(NSString *)localizedReasion
             fallbackTitle:(NSString *)title
                  callback:(TouchIdEvaluateCallback)cb
    {
        LAContext *context = [[LAContext alloc] init];
        if (title) {
            context.localizedFallbackTitle = title;
        }
        
        NSString *myLocalizedReasonString = localizedReasion;
        __weak typeof (self) weakSelf = self;
        [context evaluatePolicy:LAPolicyDeviceOwnerAuthenticationWithBiometrics
                 localizedReason:myLocalizedReasonString
                           reply:
         ^(BOOL succes, NSError *error) {
             if (succes) {
                 [[weakSelf class] reportResultOnUI:kTouchIdEvaluateResultSuccess callback:cb];
             } else {
                 switch (error.code) {
                     case LAErrorAuthenticationFailed:
                         [[weakSelf class] reportResultOnUI:kTouchIdEvaluateResultFailed callback:cb];
                         break;
                     case LAErrorUserCancel:
                         [[weakSelf class] reportResultOnUI:kTouchIdEvaluateResultCancel callback:cb];
                         break;
                     case LAErrorUserFallback:
                         [[weakSelf class] reportResultOnUI:kTouchIdEvaluateResultFallback callback:cb];
                         break;
                     default:
                         [[weakSelf class] reportResultOnUI:kTouchIdEvaluateResultOther callback:cb];
                         break;
                 }
             }
         }];
    }
    
    + (void)reportResultOnUI:(TouchIdEvaluateResult)result callback:(TouchIdEvaluateCallback)cb
    {
        dispatch_async(dispatch_get_main_queue(), ^{
            cb(result);
        });
    }
	
    @end
```  
** 问题总结 ** 
    
1.指纹识别3次错误会弹出系统“输入密码”数字键盘，而且这3次错误机会是系统所有应用共享

2.不要在 `evaluatePolicy:`方法中调用 `canEvaluatePolicy` `<LocalAuthentication/LAContext.h>`中有说明

** 参考链接 ** 

- <u>https://developer.apple.com/library/ios/documentation/LocalAuthentication/Reference/LAContext_Class/index.html#//apple_ref/occ/cl/LAContext </u>

- <u>http://hayageek.com/ios-touch-id-authentication-api/ </u>



