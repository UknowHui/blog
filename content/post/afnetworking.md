---
title: AFNetworking源码了解
slug: AFNetworking
date: 2018-05-26
categories:
- iOS
- home
tags:
- iOS
---

<!--more-->

### AFNetworking主要分为5个模块：
1. 网络通信模块(`AFURLSessionManager`、`AFHTTPSessionManger`)
2. 网络状态监听模块(`Reachability`)
3. 网络通信信息序列化/发序列化模块(`Serialization`)
4. 对于`iOS UIKit`库的扩展(`UIKit`)

 AFNetworking3.x是基于`NSURLSession`封装的，所以这个类围绕着`NSURLSession`做了一系列的上层封装，而其余的四个模块，均是为了配合`AFURLSessionManager`类的网络通信做一些必要的处理工作。其中`AFHTTPSessionManager`是继承于`AFURLSessionManager`的，我们一般做网络请求都是用这个类，但是它本身是没有做实事的，只是做了一些简单的封装，把请求逻辑分发给父类`AFURLSessionMananger`去做。