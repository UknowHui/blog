---
title: KVC
slug: KVC
date: 2018-06-05
categories:
- iOS
- home
tags:
- iOS
---

KVC
<!--more-->

全称是Key-value coding,键值编码，它提供了一种使用字符串而不是访问器方法去访问一个对象实例变量的机制。
KVC主要对三种类型进行操作，基础数据类型及常量、对象类型、集合类型。
在使用KVC时，直接将属性名当作key，并设置value，即可对属性进行赋值。
keyPath：对其更“深层”的对象赋值
[myAccount setValue:@"长安街" forKeyPath:@"address.street"];
NSArray *names = [array valueForKeyPath:@"name"];
用valueForKeyPath方法，@“@avg,@count,@sum,@max,@min” 对集合进行计算，集合中只有一个属性，类型为NSNumber的时候，可以用self代表值本身。
基础Setter搜索模式
1.查找set<Key>:或_set<Key>命名的setter，如果找到，调用这个方法并将值传进去（根据需要进行对象转换）
2.如果没有发现简单的setter，但是accessInstanceVariablesDirectlyl类属性返回yes，则查找一个命名规则为_<key>,_is<Key>,<key>,is<Key>的实例变量，如果发现则将value赋值给实例变量
3.如果没有发现setter或实例变量，则调用setValue:forUndefineKey方法，并默认提出一个异常，但是一个NSObject的子类可以提出合适的行为。
基础Getter搜索模式
1.通过getter方法搜索实例，get<Key>,<key>,is<Key>,_<key>,如果发现符合的方法，跳转第五步
2.搜索其匹配模式的方法countof<Key>,objectIn<Key>AtIndex:,<key>AtIndexs:。找到其中的第一个和其他两个中的一个，则创建一个集合代理对象，该对象响应所有NSArray的方法并返回该对象。
3.查找countOf<Key>,enumeratorOf<Key>,memberOf<Key>:，如果找到三个方法，则创建一个集合代理对象，该对象响应所有NSSet方法并返回
4.如果没有发现简单getter方法，或集合存取方法组，以及接受类方法accessInstanceVariablesDirectly返回yes，搜索一个名为_<key>,_is<Key>,<key>,is<Key>，如果发现对应的实例，则立刻获得实例可用的值并跳转到第五步，否则，跳转到第六步
5.如果取回的是一个对象指针，则直接返回这个结果，如果取回的是一个基础数据类型，但是这个基础数据类型是被NSNumber支持的，则存储为NSNumber并返回。如果取回的是一个不支持NSNumber的基础数据类型，则通过NSValue进行存储并返回。
6.如果所有情况都失败，则调用valueForUndefinedKey:方法并抛出异常，这是默认行为，但是子类可以重写此方法。
安全性检查

KVC存在一个问题在于，因为传入的key或keyPath是一个字符串，这样很容易写错或者属性自身修改后字符串忘记修改，这样会导致Crash。
可以利用iOS的反射机制来规避这个问题，通过@selector()获取到方法的SEL，然后通过NSStringFromSelector()将SEL反射为字符串。这样在@selector()中传入方法名的过程中，编译器会有合法性检查，如果方法不存在或未实现会报黄色警告。
[self valueForKey:NSStringFromSelector(@selector(object))]