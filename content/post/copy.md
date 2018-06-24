---
title: 深拷贝和浅拷贝
slug: copy
date: 2018-05-29
categories:
- iOS
- home
tags:
- iOS
---

<!--more-->

首先，只有遵守NSCopying协议的类才能发送copy消息，同理，遵守了NSMutableCopying协议的类才能发送mutableCopy消息，大部分Foundation中的类都遵守NSCopying协议，但是NSObject的子类，也就是我们自定义的类并未遵守NSCopying协议。

- 浅拷贝：又称为指针拷贝，并不会分配新的内存空间，新的指针和原指针指向同一地址。
- 深拷贝：又称为对象拷贝，会分配新的内存空间，新指针和原指针指向不同的内存地址，但是存储的内容相同。

1. 不可变对象(NSString,NSArray,NSDictionary)copy都是浅复制， 其他都是深复制。
2. 浅复制不产生新对象，深复制产生新对象。
3. copy之后的副本对象类型是不可变，mutableCopy之后的副本对象类型是可变。(不论原对象是可变的还是不可变的，copy 之后返回的总是不可变对象，mutableCopy 返回的总是可变对象)
4.NSArray和NSMutableArray的深复制和浅复制都是对array进行复制，元素还是指针拷贝，地址并没有变。
5. NSDictionary和NSMutableDictionary的深复制和浅复制都是对dictionary进行的复制，key和value还是指针拷贝，地址并没有变。

* 浅复制(shallow copy)：在浅复制操作时，对于被复制对象的每一层都是指针复制。
* 深复制(one-level-deep copy)：在深复制操作时，对于被复制对象，至少有一层是深复制。
* 完全复制(real-deep copy)：在完全复制操作时，对于被复制对象的每一层都是对象复制。
initWithArray:copyItems 是双层拷贝，是对元素进行了copy，如果元素是可变类型，此操作产生的元素因为copy会变成不可变类型，归档解档不会出现此问题。

![Alt text](/images/copy.png)