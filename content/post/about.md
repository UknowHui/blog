---
title: iOS
slug: chinese-test
date: 2018-05-10
categories:
- iOS
- home
tags:
- iOS
thumbnailImagePosition: left
thumbnailImage: //d1u9biwaxjngwg.cloudfront.net/chinese-test-post/vintage-140.jpg
---

一些关于iOS的学习
<!--more-->
# Block循环引用
{{< codeblock >}}
__weak typeof(self) weakSelf = self; 
self.aBlock = ^{ 
      __strong typeof(weakSelf) strongSelf = weakSelf;
       if (!strongSelf) return; 
        // 其它代码
        ... 
  }
{{< /codeblock >}}

##### 为什么这么写？

- 解除循环引用的问题。__weak 是弱引用，不会将 self 的引用计数器 +1。_strong 将 weakSelf 引用计数器 +1，以保持对 weakSelf 的持有，但是 strongSelf 是一个局部变量，过完这个代码块，strongSelf 就会自动释放，所以解除了循环引用的可能性。

- 防止应用奔溃。if (!strongSelf) return; 我们假设一种很常见的情况，当 self 已经释放的时候，这个 block 被调起，然后就去访问一个为 nil 的僵尸对象，比如说将 self 的某个属性插入字典什么的，这个时候往字典里插入空元素，自然会造成应用奔溃，有了这一行代码，就不会再出现类似的情况了。

