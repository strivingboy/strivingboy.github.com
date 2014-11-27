---
layout: post
title: "iOS8 Day-by-Day :: Day 1 :: swift学习指南"
description: "iOS8 Day-by-Day Day 1 swfit 学习指南"
date: 2014-11-27 16:21:13 +0800
comments: true
categories: iOS8,Day-by-Day,翻译
---

这篇文章是iOS8 Day-by-Day系列的一部分，你可以查看完整的系列目录：[iOS8 Day-by-Day 系列文章](/blog/2014/11/27/ios8daybyday/)

**介绍**

今年的WWDC大会真让人难以置信,除了宣布iOS8之外,他们还引入了一个新的编程语言Swfit。这跟objective-c这种强类型的语言有很大的不同,它包括了现代语言一些常见特性。

怀着拥抱一切新东西的心情,这系列的博客将使用swfit。有大量的信息关于如何学习swfit语言,以及如何与cocoa交互——事实上下面这两本官方的书籍不能错过:

+ [The Swift Programming Language](https://itunes.apple.com/cn/book/swift-programming-language/id881256329?mt=11) 
+ [Using Swift with Cocoa and Objective-C](https://itunes.apple.com/us/book/using-swift-cocoa-objective/id888894773?mt=11&ls=1) 

这两本书的中文版见 github: [Welcome-to-Swift](https://github.com/CocoaChina-editors/Welcome-to-Swift)

<!--more-->

同时可以参考苹果的[Swfit官方博客](https://developer.apple.com/swift/blog/) 以及其它[swfit 资源](https://developer.apple.com/swift/resources/)

正因为有如此多关于Swfit的资源，本篇文章不会去介绍所有swfit的知识，而是介绍首次使用Swfit容易遇到的一些重要的陷阱以及潜在的问题，特别是和系统框架相关部分。

本章的实例程序github访问地址:[github.com/ShinobiControls/iOS8-day-by-day/](https://github.com/ShinobiControls/iOS8-day-by-day/)

**初始化**

关于初始化，swfit 和 objective-c 有很大的不同如下：

objective-c代码:

```objective-c
- (instancetype)init {
  self = [super init];
  if (self) {
    // Do some stuff
  }
  return self;
}
```

对应的swfit代码如下：

```objective-c
init {
  variableA = 10
  ...
  super.init()
}
```

objective-c 的初始化方法负责 创建和返回*self* 而swfit没有*return* 语句，这就意味着swfit没有任何方式去返回*nil*对象。
*nil* 对象在objective-c中通常表示一个失败的初始化，这显然在接下来发布的swfit更新中有可能改掉。

**可变对象与不可变对象**

可变或不可变对象的概念对Cocoa开发者已经非常熟悉如：*NSArray* 和 *NSMutableArray* 在swfit中，*let*关键字来定义不可变对象，如：

```objective-c
let a = MyClass()
a = MySecondClass() // Not allowed
```

这就意味这不能重新赋值给*let*关键字指定的实例。实例的引用能不能重新赋值取决于对象的类型，如果一个实例是**value type**（如结构体）引用了*let*关键字指定的实例，改实例依然是不可变的，如果引用是一个**class**实例, 则改引用是可变的，具体看下面的例子：

```objective-c
struct MyStruct {
  let t = 12
  var u: String
}
```

如果使用*var*关键字定义一个变量*struct1* ,你将得到如下行为：

```objective-c
var struct1 = MyStruct(t: 15, u: "Hello")
struct1.t = 13 // Error: t is an immutable property
struct1.u = "GoodBye"
struct1 = MyStruct(t: 10, u: "You")
```

你可以改变属性*u*，因为它是通过*var*关键字定义的，同时也可以变量*struct1*本身，同样是因为通过*var*关键字定义的，而不能改变属性*t*,因为它是通过*let*关键字定义的，让我们看下使用*let*关键字定义的一个*struct*：

```objective-c
let struct2 = MyStruct(t: 12, u: "World")
struct2.u = "Planet" // Error: struct2 is immutable
struct2 = MyStruct(t: 10, u: "Defeat") // Error: struct2 is an immutable ref
```
现在，不仅是*struct2*不可变，而去属性*u*也是不可变的，这是因为结构体是一个 **value type**

对于一个**class**上述行为则有点不同，如下：

```objective-c
class MyClass {
  let t = 12
  var u: String

  init(t: Int, u: String) {
    self.t = t
    self.u = u
  }
}
```
通过*var*关键字定义一个*class*实例，行为和使用objective-c类似：

```objective-c
var class1 = MyClass(t: 15, u: "Hello")
class1.t = 13 // Error: t is an immutable property
class1.u = "GoodBye"
class1 = MyClass(t: 10, u: "You")
```
现在不仅引用自己是可变的，而且所有通过*var*关键字定义的属性也是可变的，只是通过*let*关键字定义的属性是不可变的。作为比较我们来看下使用*let*关键字定义的实例：

```objective-c
let class2 = MyClass(t: 12, u: "World")
class2.u = "Planet" // No error
class2 = MyClass(t: 11, u: "Geoid") Error: class2 is an immutable reference
```
现在自己的引用就是不可变的了，但是*var*关键字定义的属性是可变的，这是因为一个*class*是 **reference type**

上述行为时比较容易理解的，因为大部分有引用类型的语言书籍都有详细的解释，当使用swfit 集合类型是上述行为就有点困惑，一个 *NSArray* 是一个**reference type**,也就是说当你创建一个*NSArray*实例时，就是创建了一指针指向数组所在的一块内存区域（根据objective-c 的定义），如果你根据上面介绍的"reference type"不难推理出*NSArray*的实例也是可变的，实际上，如果要创建一个可变数组，再objective-c中完我们要使用*NSMutableArray*
Swfit 数组与此不同，它使用**value types** 来代替 **reference types**, 也就意味着在swfit中的行为类似一*struct*而不是一个*class*,因此关键字 *let* 或者*var* 不仅可以指定变量是否可以重定义，而且还可以指定一个数组实例是否可变。
通过 *var*定义的数组既可以重新赋值也是可变的：

```objective-c
var array1 = [1,2,3,4]
array1.append(5)  // [1,2,3,4,5]array1[0] = 27    // [27,2,3,4,5]
array1 = [3,2]    // [3,2]
```

通过 *var*定义的数组两者都不可以：

```objective-c
let array2 = [4,3,2,1]
array2.append(0) // Error: array2 is immutablearray2[2] = 36   // Error: array2 is immutable
array2 = [5,6]   // Error: cannot reassign an immutable reference
```

哦，my god,都晕了，这不仅完全改变了我们对集合可变的认识，而且混谣了上面说的两个概念的区别，这个有个可能在后面swfit语言的发展中会修改，因此保持关注swfit语言的定义。

结论：由于arrays 是 值类型，所以它们通过拷贝来传递，*NSArray*实例是通过引用来传递，如果在一个方法中传递一个swfit array ，它将对这个数组进行拷贝，至于是深拷贝还是潜拷贝取决于数组中存储的对象。

**强类型和任意对象(AnyObject)**

强类型是swfit一个很大的特性，使用它可以写出更安全的代码，这使得objective-c在运行时的异常可以在编译时捕获。

这是很强大，但是在objective-c 中我们经常用到 *id*类型，swfit中对应的就是*AnyObject*，*AnyObject* 感觉不像swfit的风格(un-swfit-like)，它允许你调用任何它能找到的方法，但是这将在运行时导致异常。实际上，它的行为和objective-c的*id*非常相似，区别在于，如果*AnyObject*对象上没有相应的属性或方法，它将返回*nil*:

```objective-c
let myString: AnyObject = "hello"
myString.cornerRadius // Returns nil
```

为了更像Cocoa APIs中的 swfit 风格（swfit-like），下面是很常见的代码模式：

```objective-c
func someFunc(parameter: AnyObject!) -> AnyObject! {
  if let castedParameter = parameter as? NSString {
    // Now I know I have a string :)
    ...
  }
}
```

如果你完全确定传递一个字符串，你没有必要做如上的代码保护，可以直接这样写：

```objective-c
let castedParameter = parameter as NSString
```

**Swift 协议（protocol）**

Protocol 在swift中很容易理解如下：

```objective-c
protocol MyProtocol {
  func myProtocolMethod() -> Bool
}
```

一个常见的情况就是检测一个对象是否符合指定的协议，你可能写下：

```objective-c
if let class1AsMyProtocol = class1 as? MyProtocol {
    // We're in
}
```
然而它会导致错误，因为为了检查是否符合某个协议，改协议必须是objective-c 协议，即通过 *@objc*来指定的协议：

```objective-c
@objc protocol MyNewProtocol {
  func myProtocolMethod() -> Bool
}

if let class1AsMyNewProtocol = class1 as? MyNewProtocol {
  // We're in
}
```
如果Swift类继承自Objective-C的类，则它里面的方法和属性都能够作为Objective-C的选择器使用。而如果不是Objective-C的子类，需要使用@objc属性修饰。

**枚举**

枚举在swfit中变化很大，不仅可以枚举有关联的值（可以使不同类型），而且还方法也可以枚举。

```objective-c
enum MyEnum {
  case FirstType
  case IntType (Int)
  case StringType (String)
  case TupleType (Int, String)

  func prettyFormat() -> String {
    switch self {
    case .FirstType:
      return "No params"
    case .IntType(let value):      return "One param: \(value)"    case .StringType(let value):
      return "One param: \(value)"
    case .TupleType(let v1, let v2):
      return "Some params: \(v1), \(v2)"
    default:
      return "Nothing to see here"
    }
  }
}
```

真是强大，可以如下使用：

```objective-c

var enum1 = MyEnum.FirstType
enum1.prettyFormat() // "No params"
enum1 = .TupleType(12, "Hello")
enum1.prettyFormat() // "Some params: 12, Hello"
```

**总结**

Swfit 是一门功能强大的语言，掌握它的最佳实践、惯用方法以及模式需要一段时间，这篇文章列出了一些从objective-c到swfit常见的困惑.

本文翻译自：[ios8-day-by-day-day-1-blaggers-guide-to-swift](http://www.shinobicontrols.com/blog/posts/2014/07/17/ios8-day-by-day-day-1-blaggers-guide-to-swift)




