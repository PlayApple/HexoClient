---
title: xcode 如何设置第三方库不使用ARC
tags:
  - ARC
id: 283
categories:
  - IPhone
date: 2012-07-24 01:06:58
---

xcode支持ARC技术，对于不熟悉内存管理的是个不错的办法，但是有时自己项目使用了ARC，但是自己在项目中使用第三方库继续引用ARC，可能导致一些莫名的错误，这是最好对第三方库不要使用ARC。

&nbsp;

如何设置第三方库不使用ARC的方法：

&nbsp;

对于某些我们不希望使用 ARC 的文件,例如第三方库源文件,可以<span style="color: #ff0000;">在 Project Settings -&gt; Build Phases 中,对这些文件选中 -fno-objc-arc 标志。这样 Xcode 编译项目时,这些文件就不会使用 ARC。</span>