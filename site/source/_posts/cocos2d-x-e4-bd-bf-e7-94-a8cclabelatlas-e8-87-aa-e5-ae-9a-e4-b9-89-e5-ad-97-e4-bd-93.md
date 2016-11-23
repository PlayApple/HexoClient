---
title: cocos2d-x 使用CCLabelAtlas自定义字体
tags:
  - CCLabelAtlas
id: 201
categories:
  - Cocos2D-X
date: 2012-05-05 14:26:41
---

有时候游戏中要用到一些特殊的字体效果，特别是数字。

CCLabelAtlas就可以从png图中读取文字。

[![](http://www.cocos2dev.com/wp-content/uploads/2012/05/set1_player_hud3.png "set1_player_hud3.png")](http://www.cocos2dev.com/wp-content/uploads/2012/05/set1_player_hud3.png)

CCLabelAtlas* diceCount=CCLabelAtlas::labelWithString("1:", "nums_font.png", 14, 21, '0');

第一个参数：显示的内容：1x，你也许会奇怪为什么是1x，因为使用的png图必须是连续的，因为程序内部是议连续的scall码识别的。9的后一位的”:“，所以先实现x就得用”:“代替。

第二个参数：图片的名字

第三个参数：每一个数字的宽

第四个参数：每一个数字的高

每五个数字：开始字符