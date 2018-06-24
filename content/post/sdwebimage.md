---
title: SDWebImage源码了解
slug: SDWebImage
date: 2018-05-27
categories:
- iOS
- home
tags:
- iOS
---

<!--more-->

显示placeholderimage->从缓存中查找图片是否下载（已下载，回调）->从硬盘中查找图片是否已下载（已下载，回调）->下载图片（下载失败，设置为无效图片）->下载成功，缓存图片，回调
缓存回收，根据时间和使用频率