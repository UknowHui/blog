---
title: 1-1）基础-开发阶段-启动流程
slug: iOS体系
date: 2019-03-15
categories:
- iOSMap
- home
tags:
- iOSMap
---

- 冷启动：系统进程不存在，需要系统新创建一个进程分配给它，这是一次完整的启动过程。
- 热启动：App退至后台，进程还在系统里的情况下重新启动

冷启动
-----------
### 1. main()函数执行前，pre-main阶段
从App开始启动到系统main()函数执行
<br />

- 加载可执行文件(App的.o文件集合)

- 加载动态链接器dyld，递归加载所有的依赖动态链接库，iOS中用到的所有系统framework，加载OC runtime方法的libobjc、libdispatch(GCD)等
```
优化：减少动态库加载。使用静态库而不是动态库。合并非系统动态库为一个动态库。
```

- 进行rebase指针调整和bind符号绑定
```
优化：减少加载启动后不会去使用的类和方法(减少Objec类数量，减少selector数量)，减少C++虚函数数量
```

- 读取二进制文件的DATA段内容，Objc运行时的初始化处理；包括Objc相关类的注册，加载一个dylib时其定义的所有的类都需要被注册到一张映射类名与类的全局表中；protocal、category注册,把category的定义插入方法列表;selector唯一性检查
- 执行+load()方法、C++的构造函数attribute((constructor))修饰的函数的调用、创建非基本类型的C++静态全局变量(通常是类或结构体)
```
优化：+load()方法里面的内容放到首屏渲染完成后再执行，或者使用+initialize()方法替换
```
##### Load dylibs -> Rebase -> Bind -> Objc -> Initializers

#### 2. main()函数执行后
main()函数执行开始到didFinishLaunchingWithOptions方法里首屏渲染相关方法执行完成(或者是主UI的viewDidAppear)

- 首屏初始化所需的配置文件的读写、首屏列表数据的读取、首屏渲染
```
优化：第三方服务的初始化、监听的注册、配置文件的读取等等耗时操作，尽量使用懒加载、后台初始化、延时初始化等等优化；首屏使用纯代码
```

检测方法
--------
- 通过在工程的`scheme`中添加环境变量DYLD_PRINT_STATISTICS，设置值为1，App启动加载时Xcode的控制台就会有pre-main各个阶段的详细耗时输出。但是DYLD_PRINT_STATISTICS 变量打印时间是iOS10以后才支持的功能，所以需要用iOS10系统及以上的机器来做测试。
- Xcode 工具套件里自带的 `Time Profiler`
- `AppCode` 检查未使用的文件
- `hook objc_msgsend`方法，查看每个方法的耗时，进而优化

相关链接
-------------

iOS启动时间优化 http://www.zoomfeng.com/blog/launch-time.html
<!--more-->