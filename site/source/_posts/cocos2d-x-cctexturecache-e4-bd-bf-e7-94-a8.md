---
title: cocos2d-x CCTextureCache使用
id: 114
categories:
  - Cocos2D-X
date: 2012-04-10 05:25:25
tags:
---

CCTextureCache,读取图片当文件名一样时，直接返回内存而非在读取文件；所有图像有关的实现统一调用CCTextureCache类的单例对象，保证最少的系统IO操作，提高程序运行效率。先记录一笔，后续再详细介绍。