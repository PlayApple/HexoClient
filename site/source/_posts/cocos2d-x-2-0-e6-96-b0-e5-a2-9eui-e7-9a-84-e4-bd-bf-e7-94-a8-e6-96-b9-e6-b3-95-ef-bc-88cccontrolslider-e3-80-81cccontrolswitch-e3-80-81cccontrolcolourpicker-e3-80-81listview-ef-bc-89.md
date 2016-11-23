---
title: >-
  cocos2d-x-2.0
  新增UI的使用方法（CCControlSlider、CCControlSwitch、CCControlColourPicker、ListView）
tags:
  - CCControlColourPicker
  - CCControlSlider
  - CCControlSwitch
  - ListView
id: 252
categories:
  - Cocos2D-X
date: 2012-06-26 06:57:33
---

cocos2d-x-2.0新增加了几个UI控件，这里我介绍下常用的这几个UI控件（CCControlSlider、CCControlSwitch、CCControlColourPicker、ListView）的使用方法。

[![](http://www.cocos2dev.com/wp-content/uploads/2012/06/11.png "1")](http://www.cocos2dev.com/wp-content/uploads/2012/06/11.png)

一、CCControlSlider

[cpp]

// Slider

CCControlSlider * slider = CCControlSlider::sliderWithFiles(&quot;sliderTrack2.png&quot;,&quot;sliderProgress2.png&quot; ,&quot;sliderThumb.png&quot;);

//添加回调函数

slider-&gt;addTargetWithActionForControlEvents(this, menu_selector(HelloWorld::actionSlider),CCControlEventValueChanged);

slider-&gt;setPosition(ccp(size.width*.8, size.height*.2) );

//silder的最小值和最大值

slider-&gt;setMinimumValue(0.0f);

slider-&gt;setMaximumValue(100.0f);

//slider的当前值

slider-&gt;setValue(50.0f);

addChild(slider);

[/cpp]

回调函数：

[cpp]

void HelloWorld::actionSlider(CCObject* pSender){

CCControlSlider* pSliderCtl = (CCControlSlider*)pSender;

ccTime scale;

scale = pSliderCtl-&gt;getValue();

//slider的当前值

CCLog(&quot;CCControlSlider value = %f&quot;,scale);

}

[/cpp]

二、CCControlSwitch

[cpp]

//switch button

CCControlSwitch *switchControl = CCControlSwitch::switchWithMaskSprite

(

CCSprite::spriteWithFile(&quot;switch-mask.png&quot;),

CCSprite::spriteWithFile(&quot;switch-on.png&quot;),

CCSprite::spriteWithFile(&quot;switch-off.png&quot;),

CCSprite::spriteWithFile(&quot;switch-thumb.png&quot;),

CCLabelTTF::labelWithString(&quot;On&quot;, &quot;Arial-BoldMT&quot;, 16),

CCLabelTTF::labelWithString(&quot;Off&quot;, &quot;Arial-BoldMT&quot;, 16)

);

switchControl-&gt;setPosition(ccp (size.width*.8, size.height*.35));

//回调函数

switchControl-&gt;addTargetWithActionForControlEvents(this, menu_selector(HelloWorld::callbackSwitch), CCControlEventValueChanged);

addChild(switchControl);

callbackSwitch(switchControl);

[/cpp]

回调函数：

[cpp]

void HelloWorld::callbackSwitch(CCObject* pSender){

CCControlSwitch* pSwitch = (CCControlSwitch*)pSender;

if (pSwitch-&gt;getIsOn()){

CCLog(&quot;CCControlSwitch value = ON&quot;);

} else{

CCLog(&quot;CCControlSwitch value = OFF&quot;);

}

}

[/cpp]

三、CCControlColourPicker

[cpp]

//clolor picker

CCControlColourPicker *colourPicker = CCControlColourPicker::colourPicker();

colourPicker-&gt;setColor(ccc3(37, 46, 252));

colourPicker-&gt;setPosition(ccp (size.width*.8, size.height*.7));

colourPicker-&gt;addTargetWithActionForControlEvents(this, menu_selector(HelloWorld::colourValueChanged), CCControlEventValueChanged);

addChild(colourPicker);

[/cpp]

回调函数：

[cpp]

void HelloWorld::colourValueChanged(CCObject *sender){

CCControlColourPicker* pPicker = (CCControlColourPicker*)sender;

std::string str = CCString::stringWithFormat(&quot;#%02X%02X%02X&quot;,pPicker-&gt;getColorValue().r, pPicker-&gt;getColorValue().g, pPicker-&gt;getColorValue().b)-&gt;getCString();

CCLog(&quot;CCControlColourPicker value = %s&quot;,str.c_str());

}

[/cpp]

四、ListView

有点长，单独开一个页面，[点击查看ListView使用&gt;&gt;](http://www.cocos2dev.com/?p=256)