---
title: Block
slug: block
date: 2018-05-10
categories:
- iOS
- home
tags:
- iOS
---

关于Block的相关知识
<!--more-->

在block内修改外部变量，外部变量需要加__block修饰，变量是static/static global则不添加__block也可以修改，为什么使用block修饰了之后就可以修改？

在执行block语法的时候，block语法表达式所使用的自动变量的值被保存进了block的结构体实例中，也就是block自身中，如果block外面还有很多自动变量、静态变量等等，block只会捕获block闭包里面会用到的值，

block仅仅捕获了自动变量，并没有捕获自动变量的内存地址，所以在内部修改无效，oc基于这一点防止开发者犯错，所以在编译过程就报错。

静态全局变量、全局变量由于作用域的原因可以直接在block里面修改，他们也都存储在全局区。静态变量传递给block的是内存地址值，所以在block内部直接修改值。

在block中改变变量值有2种方式，一是传递内存地址指针到block中，二是改变存储方式(__block)
Block_copy(_NSConcreteStackBlock -> _NSConcreteMallocBlock 9步)
Block_release (6步)
ARC环境下，一旦Block赋值就会触发copy，__block就会copy到堆上。Block捕获外部对象变量，是都会copy一份的，地址都不同，只不过带有__block修饰符的变量会被捕获到Block内部持有。
在MRC环境下，__block根本不会对指针所指向的对象执行copy操作，而只是把指针进行的复制。
而在ARC环境下，对于声明为__block的外部对象，在block内部会进行retain，以至于在block环境内能安全的引用外部对象，所以才会产生循环引用的问题！

Block可以认为是一个带有自动变量的匿名函数，return_type (^block_name)(parameters)
Block也是一种OC对象，可以用于赋值，当作参数传递，也可以放入NSArray和NSDictionary中。
__weak以及纯匿名Block是放在栈上，赋值给__strong就是默认的会在堆上创建。

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