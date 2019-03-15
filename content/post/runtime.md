---
title: Runtime你好
slug: Runtime
date: 2018-06-02
categories:
- iOS
- home
tags:
- iOS
---

Runtime
<!--more-->

Objective-C的Runtime是一个运行时库，它是一个主要使用c和汇编写的库，为c添加了面向对象的能力并创造了Objective-C,这就是说他在类信息(Class infomation)中被加载，完成所有的方法分发，方法转发等等。

Objective-C runtime 创建了所有需要的结构体，让Objective-C的面相对象编程变为可能。

Runtime库主要做下面几件事：
1. 封装：在这个库中，对象可以用c语言中的结构体表示，而方法可以用c函数来实现，另外再加上一些额外的特性。这些结构体和函数被runtime函数封装后，我们就可以在程序运行时创建，检查，修改类、对象和他们的方法了。
2. 找出方法的最终执行代码：当程序执行`[object doSomething]`时，会向消息接受者(object)发送一条消息(doSomething)，runtime会根据消息接受者是否能够响应该消息而作出不同的反应。
oc类是由Class类型类表示的，它实际上是一个指向objc_class结构体的指针：
`typedef struct objc_class *Class;`

{{< codeblock >}}
struct objc_class {
    Class _Nonnull isa  OBJC_ISA_AVAILABILITY;

#if !__OBJC2__
    Class _Nullable super_class                              OBJC2_UNAVAILABLE;
    const char * _Nonnull name                               OBJC2_UNAVAILABLE;
    long version                                             OBJC2_UNAVAILABLE;
    long info                                                OBJC2_UNAVAILABLE;
    long instance_size                                       OBJC2_UNAVAILABLE;
    struct objc_ivar_list * _Nullable ivars                  OBJC2_UNAVAILABLE;
    struct objc_method_list * _Nullable * _Nullable methodLists                    OBJC2_UNAVAILABLE;
    struct objc_cache * _Nonnull cache                       OBJC2_UNAVAILABLE;
    struct objc_protocol_list * _Nullable protocols          OBJC2_UNAVAILABLE;
#endif

} OBJC2_UNAVAILABLE;
{{< /codeblock >}}

`isa`:需要注意的是在Objective-C中，所有的类自身也是一个对象，这个对象的Class里面也有一个isa指针，它指向metaClass(元类)

`super_class`:指向该类的父类，如果该类已经是最顶层的根类(如NSObject或NSProxy)，则super_class为NULL

`cache`: 先从cache里表中查找执行过的方法，再去methodLists中查找方法。

当我们向一个对象发送消息时，runtime会在这个对象所属的这个类的方法列表中查找方法，而向一个类发送消息时，会在这个类的meta-class的方法列表中查找，meta-class存储着一个类的所有类方法。每个类都会有一个单独的meta-class，因为每个类的类方法基本不可能完全相同。任何NSObject继承体系下的meta-class都使用NSObject的meta-class作为自己的所属类，而基类的meta-class的isa指针指向它自己。