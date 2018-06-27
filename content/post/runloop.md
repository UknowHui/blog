---
title: RunLoop
slug: RunLoop
date: 2018-06-04
categories:
- iOS
- home
tags:
- iOS
---

RunLoop
<!--more-->

一个线程一次职能执行一个任务，执行完成后线程就会退出，如果我们需要一个机制，让线程能随时处理时间但并不退出，如何让线程在没有处理消息时休眠以避免资源占用，在有消息到来时立刻被唤醒，RunLoop实际上就是一个对象，这个对象管理了其需要处理的事件和消息，并提供了一个函数来执行event loop的逻辑，线程执行了这个函数后，就会一直处于这个函数内部“接受消息->等待->处理”的循环中，知道这个循环结束，函数返回。

CFRunLoopRef:在CoreFoundation框架内的，它提供了纯c函数的api，都是线程安全的
NSRunLoop:基于CFRunLoopRef的封装，提供了面向对象的api，不是线程安全的
CFRunLoop是基于pthread来管理的，apple不允许创建RunLoop，它只提供了两个自动获取的函数，CFRunLoopGetMain()和CFRunLoopGetCurrent()
线程和RunLoop之间是一一对应的，其关系是保存在一个全局的Dictionary里，线程刚创建时并没有RunLoop，如果你不主动获取，那它一直不会有，RunLoop的创建是发生在第一次获取时，RunLoop的销毁是发生在线程结束时，职能在一个线程的内部获取其RunLoop(主线程除外)

一个RunLoop包含若干个Mode，每个Mode又包含若干个Source/Timer/Observer，每次调用RunLoop的主函数时，只能指定其中一个Mode，这个Mode被称作CurrentMode，如果需要切换Mode，职能退出RunLoop，再重新指定一个Mode进入
CFRunLoopSourceRef是时间产生的地方
CFRunLoopTimerRef是基于时间的触发器
CFRunLoopObserverRef是观察者，每个Observer都包含一个回调(函数指针)，当RunLoop的状态发生变化时，观察者就能通过回调接收到这个变化
{{< codeblock >}}
typedef CF_OPTIONS(CFOptionFlags, CFRunLoopActivity) {
    kCFRunLoopEntry         = (1UL << 0), // 即将进入Loop
    kCFRunLoopBeforeTimers  = (1UL << 1), // 即将处理 Timer
    kCFRunLoopBeforeSources = (1UL << 2), // 即将处理 Source
    kCFRunLoopBeforeWaiting = (1UL << 5), // 即将进入休眠
    kCFRunLoopAfterWaiting  = (1UL << 6), // 刚从休眠中唤醒
    kCFRunLoopExit          = (1UL << 7), // 即将退出Loop
};
{{< /codeblock >}}

这里有个概念叫 “CommonModes”：一个 Mode 可以将自己标记为”Common”属性（通过将其 ModeName 添加到 RunLoop 的 “commonModes” 中）。每当 RunLoop 的内容发生变化时，RunLoop 都会自动将 _commonModeItems 里的 Source/Observer/Timer 同步到具有 “Common” 标记的所有Mode里。

应用场景举例：主线程的 RunLoop 里有两个预置的 Mode：kCFRunLoopDefaultMode 和 UITrackingRunLoopMode。这两个 Mode 都已经被标记为”Common”属性。DefaultMode 是 App 平时所处的状态，TrackingRunLoopMode 是追踪 ScrollView 滑动时的状态。当你创建一个 Timer 并加到 DefaultMode 时，Timer 会得到重复回调，但此时滑动一个TableView时，RunLoop 会将 mode 切换为 TrackingRunLoopMode，这时 Timer 就不会被回调，并且也不会影响到滑动操作。
有时你需要一个 Timer，在两个 Mode 中都能得到回调，一种办法就是将这个 Timer 分别加入这两个 Mode。还有一种方式，就是将 Timer 加入到顶层的 RunLoop 的 “commonModeItems” 中。”commonModeItems” 被 RunLoop 自动更新到所有具有”Common”属性的 Mode 里去。