---
title: cocos2d-x-2.0 ListView使用
tags:
  - ListView
id: 256
categories:
  - Cocos2D-X
date: 2012-06-26 07:08:35
---

我在上一篇文章中介绍了[CCControlSlider、CCControlSwitch、CCControlColourPicker的用法](http://www.cocos2dev.com/?p=252)，现在把listView也讲解一下。由于cocos2d-x没有给出例子，所以代码有点长。我都写了注释，应该很容易理解。

![](http://www.cocos2dev.com/wp-content/uploads/2012/06/11.png "listView")

ListViewTestLayer.h头文件

[cpp]

//

//  ListViewTestLayer.h

//  2dxDemo

//

//  Created by Yanghui Liu on 12-6-26.

//  Copyright (c) 2012年 BoyoJoy. All rights reserved.

//

&amp;nbsp;

#ifndef _dxDemo_ListViewTestLayer_h

#define _dxDemo_ListViewTestLayer_h

&amp;nbsp;

#include &quot;cocos2d.h&quot;

#include &quot;CCListView.h&quot;

#include &lt;list.h&gt;

#include &lt;string.h&gt;

&amp;nbsp;

USING_NS_CC;

using namespace cocos2d::extension;

&amp;nbsp;

class ListViewTestLayer : public CCLayer , public CCListViewDelegate {

public:

ListViewTestLayer();

~ListViewTestLayer();

virtual bool init();

LAYER_NODE_FUNC(ListViewTestLayer);

virtual void visit();

public:

// 继承自CCListViewDelegate所需要实现的方法

virtual void CCListView_numberOfCells(CCListView *listView, CCListViewProtrolData *data);

virtual void CCListView_cellForRow(CCListView *listView, CCListViewProtrolData *data);

virtual void CCListView_didClickCellAtRow(CCListView *listView, CCListViewProtrolData *data);

virtual void CCListView_didScrollToRow(CCListView *listView, CCListViewProtrolData *data);

&amp;nbsp;

private:

//显示list的状态的一个lable

CCLabelTTF *m_InfoLabel;

private:

// 存放的List数据

std::list&lt;std::string&gt; *m_pDataList;

CCListView *m_pListView;

//是否刷新，即reload

bool m_bFresh;

void initData();

};

#endif

[/cpp]

cpp的实现：

[cpp]

//

//  ListViewTestLayer.cpp

//  2dxDemo

//

//  Created by Yanghui Liu on 12-6-26.

//  Copyright (c) 2012年 BoyoJoy. All rights reserved.

//

&amp;nbsp;

#include &quot;ListViewTestLayer.h&quot;

#include &quot;CCListViewCell.h&quot;

&amp;nbsp;

ListViewTestLayer::ListViewTestLayer(){

}

&amp;nbsp;

ListViewTestLayer::~ListViewTestLayer(){

}

&amp;nbsp;

void ListViewTestLayer::initData(){

m_bFresh = true;

CCSize winSize = CCDirector::sharedDirector()-&gt;getWinSize();

m_pDataList = new std::list&lt;std::string&gt;;

for (int i=0; i&lt;15; i++) {

char info[20];

sprintf(info, &quot;My Cell %d&quot;, i);

m_pDataList-&gt;push_back(info);

}

// 初始化控件ListView

CCListView *listView = CCListView::viewWithMode(CCListViewModeVertical);

listView-&gt;setContentSize( CCSizeMake(winSize.width * .5, winSize.height));

listView-&gt;setDelegate(this);

listView-&gt;setPosition(CCPointZero);

this-&gt;addChild(listView);

m_pListView = listView;

// 初始化控件Label，显示ListView信息

m_InfoLabel = CCLabelTTF::labelWithString(&quot;Info&quot;, &quot;&quot;, 32);

m_InfoLabel-&gt;setPosition(ccp(winSize.width * .8, winSize.height *.1));

this-&gt;addChild(m_InfoLabel);

}

&amp;nbsp;

//visit方法会在每一帧的时候调用，也可以自己注册schedule

void ListViewTestLayer::visit(){

CCLayer::visit();

if (m_bFresh) {

m_pListView-&gt;reload();

m_bFresh = false;

}

}

&amp;nbsp;

//返回行数

void ListViewTestLayer::CCListView_numberOfCells(cocos2d::extension::CCListView *listView, cocos2d::extension::CCListViewProtrolData *data){

data-&gt;nNumberOfRows = m_pDataList-&gt;size();

}

&amp;nbsp;

//构造每一个cell

void ListViewTestLayer::CCListView_cellForRow(cocos2d::extension::CCListView *listView, cocos2d::extension::CCListViewProtrolData *data){

CCSize listSize = m_pListView-&gt;getContentSize();

CCSize cellSize = CCSizeMake(listSize.width, listSize.height / 5);

CCListViewCell *cell = CCListViewCell::node();

cell-&gt;setOpacity(0);

cell-&gt;setContentSize(cellSize);

//cell选中颜色

cell-&gt;setSelectionColor(ccc4(0, 255, 0, 255));

data-&gt;cell = cell;

std::list&lt;std::string&gt;::iterator it = m_pDataList-&gt;begin();

for (int i=0; i&lt;data-&gt;nRow; ++i) {

++it;

}

CCLabelTTF *cellLabel = CCLabelTTF::labelWithString(((std::string) *it).c_str(), &quot;Arial&quot;, 32);

cellLabel-&gt;setPosition(ccp(cellSize.width / 2, cellSize.height / 2));

cell-&gt;addChild(cellLabel);

}

&amp;nbsp;

//cell被选中

void ListViewTestLayer::CCListView_didClickCellAtRow(cocos2d::extension::CCListView *listView, cocos2d::extension::CCListViewProtrolData *data){

char info[100];

sprintf(info, &quot;No. %d Row&quot;, data-&gt;nRow);

m_InfoLabel-&gt;setString(info);

}

&amp;nbsp;

//listView在滑动中

void ListViewTestLayer::CCListView_didScrollToRow(cocos2d::extension::CCListView *listView, cocos2d::extension::CCListViewProtrolData *data){

m_InfoLabel-&gt;setString(&quot;Scrolling...&quot;);

}

&amp;nbsp;

bool ListViewTestLayer::init(){

if (!CCLayer::init()) {

return false;

}

initData();

return true;

}

&amp;nbsp;

[/cpp]

调用方法：

//list view

ListViewTestLayer *listViewDemoLayer = ListViewTestLayer::node();

addChild(listViewDemoLayer);