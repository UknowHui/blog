---
title: category
slug: category
date: 2018-06-01
categories:
- iOS
- home
tags:
- iOS
---

分类
<!--more-->

category可以动态的为已有类添加新行为。category结构体，包含类的名字，类，实例方法列表，类方法列表，协议列表，属性。编译器在DATA段下保存一个大小为1的`category_t`的数组，在运行时把category的实例方法，协议以及属性添加到类上。把category的类方法和协议添加到类的metaclass上。

category的主要作用是为已经存在的类添加方法。除此之外，apple还推荐category的另外两个使用场景：
- 可以把类的实现分开在几个不同的文件里面，这样做有几个显而易见的好处：
 1. 可以减少单个文件的体积
 2. 可以把不同的功能组织到不同的category里面
 3. 可以由多个开发者共同完成一个类
 4. 可以按需加载想要的category等等
- 声明私有方法
- 模拟多继承
- 把framework的私有方法公开

extention在编译期决议，它就是类的一部分，extension一般用来隐藏类的私有信息，你必须有一个类的源码才能为一个类添加extention，所以你无法为系统的类，比如NSString添加extention.
category是在运行期决议，extention可以添加实例变量，而category是无法添加实例变量的，因为在运行期，对象的内存布局已经确定。

category的方法没有“完全替换掉”原来类已经有的方法，也就是说如果category和原来类都有`methodA`，那么category附加完成之后，类的方法列表里面会有两个methodA，category的方法被放到了新方法列表的前面，而原来类的方法被放到了新方法列表的后面，这也就是我们平常所说的category的方法会“覆盖”掉原来类的同名方法，这是因为运行时在查找方法的时候是顺着方法列表的顺序查找的，它只要一找到对应名字的方法，就会罢休，殊不知后面可能还有一样名字的方法。

+load的执行顺序是先类，后category，而category的_load执行顺序是根据编译顺序决定的。
category其实并不是完全替换掉原来类的同名方法，只是category在方法列表的前面而已，所以我们只要顺着方法列表找到最后一个对应名字的方法，就可以调用原来类的方法。

在category里面可以添加实例变量，只是没有set和get方法，所有在使用点语法调用变量的时候会崩溃，这个时候可以使用关联对象来实现。
	`set: objc_setAssociatedObject(self, "name", name, OBJC_ASSOCIATION_COPY);`
	`get: return objc_getAssociatedObject(self, "name");`

所有得关联对象都是由`AssociationsManager`管理,`AssociationsManager`里面是由一个静态`AssociationsHashMap`来存储所有的关联对象的。这相当于把所有对象的关联对象都存在一个全局map里面。而map的的key是这个对象的指针地址（任意两个不同对象的指针地址一定是不同的），而这个map的value又是另外一个`AssociationsHashMap`，里面保存了关联对象的kv对。
而在对象的销毁逻辑里面，`runtime`的销毁对象函数`objc_destructInstance`里面会判断这个对象有没有关联对象，如果有，会调用_`object_remove_assocations`做关联对象的清理工作。