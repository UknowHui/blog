---
title: 基础-开发阶段-启动流程
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

### 冷启动
#### 1. main()函数执行前，pre-main阶段

链接：iOS启动时间优化 http://www.zoomfeng.com/blog/launch-time.html
<!--more-->