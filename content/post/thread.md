---
title: 多线程
slug: thread
date: 2018-06-03
categories:
- iOS
- home
tags:
- iOS
---

多线程
<!--more-->

* NSThread是一个控制线程执行的对象，通过它我们可以方便的得到一个线程并控制它。NSThread的线程之间的并发控制，是需要我们自己来控制的，可以通过NSCondition实现。它的缺点是需要自己维护线程的声明周期和线程的同步和互斥等，优点是清亮，灵活。
* NSOperation是一个抽象类，它封装了线程的细节实现，不需要自己管理线程的声明周期和线程的同步和互斥等。只是需要关注自己的业务逻辑处理，需要和NSOperationQueue一起使用，使用NSOperation时，你可以很方便的设置线程之间的依赖关系。
*  GCD是apple开发的一个多核编程的解决方法。提供了很简洁的面向“任务”的编程接口，让程序员可以专注于代码的便携。GCD底层实现仍然依赖于线程，但是使用GCD时完全不需要考虑下层线程的有关细节(创建任务比创建线程简单的多)，GCD会自动对任务进行调度，以尽可能的利用处理器资源。
GCD：
* Dispatch Queue：Dispatch Queue 顾名思义，是一个用于维护任务的队列，它可以接受任务（即可以将一个任务加入某个队列）然后在适当的时候执行队列中的任务。
* Dispatch Sources：Dispatch Source 允许我们把任务注册到系统事件上，例如 socket 和文件描述符，类似于 Linux 中 epoll 的作用
* Dispatch Groups：Dispatch Groups 可以让我们把一系列任务加到一个组里，组中的每一个任务都要等待整个组的所有任务都结束之后才结束，类似 pthread_join 的功能
* Dispatch Semaphores：这个更加顾名思义，就是大家都知道的信号量了，可以让我们实现更加复杂的并发控制，防止资源竞争

NSOperation:
NSOperation是对GCD中的block进行的封装，它也表示一个要被执行的任务，
NSOperationQueue是一个专门用于执行NSOperation的队列。
NSOperation可以通过addDependency来依赖于其他的operation完成。
GCD与NSOperation的对比
* NSOperationQueue是基于GCD的更高层的封装
* GCD由于采用c风格的api,在调用上比使用面向对象风格的NSOperation要简单一些
* 从对任务的控制性来说，NSOperation显著好于GCD，支持任务之间的依赖关系，支持同一个队列中任务的优先级设置，同时还可以通过KVO来监控任务的执行情况，这些通过GCD也可以实现，不过需要很多代码。