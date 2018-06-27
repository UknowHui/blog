---
title: KVO
slug: KVO
date: 2018-06-06
categories:
- iOS
- home
tags:
- iOS
---

KVO
<!--more-->

Key-value observing,键值观察，时间通知机制，允许对象监听另一个对象特定属性的改变，并在改变时接收到事件，一般继承自NSObject的对象都默认支持KVO.

当一个object有观察者时，动态创建这个object的类的子类，对于每个被观察的property，重写其set方法，在重写的set方法中调用willchangevalueforkey和didchangevalueforkey通知观察者，当一个property没有观察者时，删除重写的方法，当没有observer观察任何一个property时，删除动态创建的子类。

KVO的addObserver和removerObserver需要是成对的，如果重复remove则会导致NSRangeException类型的Crash，如果忘记remove则会在观察者释放后再次接收到KVO回调时Crash，在init的时候进行addObserver，在dealloc时removeObserver。

KVO是通过isa-swizzling技术实现的，在运行时根据原类创建一个中间类，这个中间类是原类的子类，并动态修改当前对象的isa指向中间类，并且将class方法重写，返回原类的class，
这些代码都只需在观察者里进行实现，被观察者不用添加任何代码，所以谁要监听谁注册，然后对响应进行处理即可，使得观察者与被观察者完全解耦，运用很灵活很简便；但是KVO只能检测类中的属性，并且属性名都是通过NSString来查找，编译器不会帮你检错和补全，纯手敲所以比较容易出错。

NSNotification的特点呢，就是需要被观察者先主动发出通知，然后观察者注册监听后再来进行响应，比KVO多了发送通知的一步，但是其优点是监听不局限于属性的变化，还可以对多种多样的状态变化进行监听，监听范围广，使用也更灵活。