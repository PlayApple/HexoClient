---
title: cocos2dx-lua中如何使用自定义类以及tolua++的使用
tags:
  - cocos2dx
  - lua
id: 405
categories:
  - Cocos2D-X
date: 2013-04-09 02:36:51
---

[![1](http://www.cocos2dev.com/wp-content/uploads/2013/04/1.jpg)](http://www.cocos2dev.com/wp-content/uploads/2013/04/1.jpg)

使用cocos2dx-lua开发，免不了自己定义类，但是如何使用自定义的类的？

先了解下lua如何调用c++的:

lua脚本代码-&gt;通过coocs2dx中间解析层代码-&gt;将其转换并调用cocos2dx c++的前端代码

coocs2dx中间解析层代码都在libs/lua/cocos2dx_support/LuaCocos2d.cpp 这个文件中，想了解的可以自己去看下这个文件。

也就是说，你自己定义了一个类，lua能够调用你自己定义的类，你的自定义类就必须在LuaCocos2d.cpp这个中间解析文件中申明。

看了LuaCocos2d.cpp这个文件，可能有的同学都晕了，不知道怎么在LuaCocos2d.cpp中申明自己的定义的类。不过，不用担心，cocos2dx已经提供了tolua++这个工具自动编译生成新的LuaCocos2d.cpp文件。

下面开始进入正题。

<span style="color: #ff0000;">一、创建一个coocs2dx-lua 的Demo工程，然后在class中自己定义个类。</span>

SNSprite.h

[/cpp]

//
// SNSprite.h
// LuaDemo
//
// Created by LiuYanghui on 13-4-8.
//
//

#ifndef __LuaDemo__SNSprite__
#define __LuaDemo__SNSprite__

#include "cocos2d.h"
USING_NS_CC;

class SNSprite : public CCSprite{
public:
static SNSprite* create(const char* name);

private:
void initData();
};

#endif /* defined(__LuaDemo__SNSprite__) */

[/cpp]

SNSprite.cpp

[cpp]

//
// SNSprite.cpp
// LuaDemo
//
// Created by LiuYanghui on 13-4-8.
//
//

#include &quot;SNSprite.h&quot;

SNSprite* SNSprite::create(const char* name){
 SNSprite* sprite = new SNSprite();
 if(sprite &amp;&amp; sprite-&gt;initWithFile(name)){
 sprite-&gt;initData();
 sprite-&gt;autorelease();
 return sprite;
 }
 CC_SAFE_DELETE(sprite);
 return NULL;
}

void SNSprite::initData(){
 CCActionInterval* action = CCSequence::createWithTwoActions(CCMoveTo::create(1.0, ccp(300,300)),
 CCMoveTo::create(1.0, ccp(100,100))
 );
 this-&gt;runAction(CCRepeatForever::create(action));
}

[/cpp]

<span style="color: #000000;">上面是我定义个一个类，很简单的功能。</span>

<span style="color: #ff0000;">二、使用tolua++将自定义的类添加的LuaCocos2d.cpp文件中。</span>

tolua++工具在/tools/tolua++/目录下。

1、编写pkg文件

_<em id="__mceDel"><em id="__mceDel">_</em></em>_<em id="__mceDel"><em id="__mceDel"><em id="__mceDel">首先我们看下里面会发现有很多pkg文件，所以先为自定义的类编写pkg文件，文件的编写规则在README中，我简单说下编写规则：_</em></em></em>

_<em id="__mceDel"><em id="__mceDel"><em id="__mceDel"><em id="__mceDel"><em id="__mceDel">Writing .pkg files_</em></em></em></em></em>

_<em id="__mceDel"><em id="__mceDel"><em id="__mceDel"><em id="__mceDel"><em id="__mceDel">_</em></em></em></em></em>_<em id="__mceDel"><em id="__mceDel"><em id="__mceDel"><em id="__mceDel"><em id="__mceDel">1) enum keeps the same //枚举类型保留不变_</em></em></em></em></em>

_<em id="__mceDel"><em id="__mceDel"><em id="__mceDel"><em id="__mceDel"><em id="__mceDel">_</em></em></em></em></em>_<em id="__mceDel"><em id="__mceDel"><em id="__mceDel"><em id="__mceDel"><em id="__mceDel"><em id="__mceDel">2) remove CC_DLL for the class defines, pay attention to multi inherites //删除cc_dll的类定义，改用多继承_</em></em></em></em></em></em>

_<em id="__mceDel"><em id="__mceDel"><em id="__mceDel"><em id="__mceDel"><em id="__mceDel"><em id="__mceDel">3) remove inline keyword for declaration and implementation //删除内联关键字声明和实现
<em id="__mceDel"><em id="__mceDel"><em id="__mceDel"><em id="__mceDel"><em id="__mceDel"><em id="__mceDel"><em id="__mceDel">_</em></em></em></em></em></em></em></em></em></em></em></em></em>

_<em id="__mceDel"><em id="__mceDel"><em id="__mceDel"><em id="__mceDel"><em id="__mceDel"><em id="__mceDel"><em id="__mceDel"><em id="__mceDel"><em id="__mceDel"><em id="__mceDel"><em id="__mceDel"><em id="__mceDel"><em id="__mceDel">4) remove public protect and private //删除访问限定词_</em></em></em></em></em></em>
</em></em></em></em></em></em></em>

_<em id="__mceDel"><em id="__mceDel"><em id="__mceDel"><em id="__mceDel"><em id="__mceDel"><em id="__mceDel">5) remove the decalration of class member variable //删除成员变量
_</em></em></em></em></em></em>

_<em id="__mceDel"><em id="__mceDel"><em id="__mceDel"><em id="__mceDel"><em id="__mceDel"><em id="__mceDel">6) keep static keyword //保留静态关键词
_</em></em></em></em></em></em>

_<em id="__mceDel"><em id="__mceDel"><em id="__mceDel"><em id="__mceDel"><em id="__mceDel"><em id="__mceDel">7) remove memeber functions that declared as private or protected //非public的函数都删除
_</em></em></em></em></em></em>

_<em id="__mceDel"><em id="__mceDel"><em id="__mceDel"><em id="__mceDel"><em id="__mceDel"><em id="__mceDel">以上是pkg文件的编写规则。
_</em></em></em></em></em></em>

_<em id="__mceDel"><em id="__mceDel"><em id="__mceDel"><em id="__mceDel"><em id="__mceDel"><em id="__mceDel">下面编写自定义文件的pkg文件。纯文本，后缀改成pkg
_</em></em></em></em></em></em>

_<em id="__mceDel"><em id="__mceDel"><em id="__mceDel"><em id="__mceDel"><em id="__mceDel"><em id="__mceDel">SNSprite.pkg_</em></em></em></em></em></em>

[cpp]

class SNSprite : public CCSprite{
 static SNSprite* create(const char* name);
};

[/cpp]

注意对比之前的头文件声明，可以进一步了解pkg文件的编写规则。将编写好的pkg文件放在tolua++中，和所有的pkg文件放在一起。

2、在Cocos2d.pkg文件中注册添加的pkg文件
用文本编辑器打开Cocos2d.pkg文件，在最后一行加入我们的注册声明：
$pfile "SNSprite.pkg"

3、配置build.sh编译脚本
在tolua++文件夹里面有个tolua++.Mac.zip，解压得到mac版的tolua++
打开build.sh文件，修改成下面的：

[cpp]

#!/bin/bash
#
# Invoked build.xml, overriding the lolua++ property
SCRIPT_DIR=$(cd &quot;$(dirname &quot;${BASH_SOURCE[0]}&quot;)&quot; &amp;&amp; pwd)
TOLUA=/APP/cocos2d-2.1rc0-x-2.1.2/tools/tolua++/tolua++
if [ -z &quot;${TOLUA}&quot; ]; then
 TOLUA=`which tolua++5.1`
fi
if [ -z &quot;${TOLUA}&quot; ]; then
 echo &quot;Unable to find tolua++ (or tolua++5.1) in your PATH.&quot;
 exit 1
fi
cd ${SCRIPT_DIR}
${TOLUA} -L basic.lua -o /Users/liuyanghui/Desktop/LuaDemo/LuaDemo/libs/lua/cocos2dx_support/LuaCocos2d.cpp Cocos2d.pkg

[/cpp]

_<span style="color: #ff0000;">两个地方注意修改：</span>_

_<em id="__mceDel">a、TOLUA=/APP/cocos2d-2.1rc0-x-2.1.2/tools/tolua++/tolua++ 这个是你的mac版tolua++的地址，请修改成你自己的。_</em>

_<em id="__mceDel">_</em>_<em id="__mceDel"><em id="__mceDel">b、${TOLUA} -L basic.lua -o /Users/liuyanghui/Desktop/LuaDemo/LuaDemo/libs/lua/cocos2dx_support/LuaCocos2d.cpp Cocos2d.pkg _</em></em>

_<em id="__mceDel"><em id="__mceDel">_</em></em>_<em id="__mceDel"><em id="__mceDel"><em id="__mceDel"><em id="__mceDel">参数-o 后面的 /Users/liuyanghui/Desktop/LuaDemo/LuaDemo/libs/lua/cocos2dx_support/LuaCocos2d.cpp 是生成新的LuaCocos2d.cpp的存放路径，请修改成你自己的。_</em></em></em></em>

_<em id="__mceDel"><em id="__mceDel"><em id="__mceDel"><em id="__mceDel">_</em></em></em></em>_4、执行编译脚本，生成LuaCocos2d.cpp文件_

_<em id="__mceDel">终端cd到build.sh目录。执行：_</em>

_<em id="__mceDel">_</em>_<em id="__mceDel"><em id="__mceDel">./build.sh_</em></em>

_<em id="__mceDel"><em id="__mceDel">执行后之后，在你定义的输出目录就生成了新的LuaCocos2d.cpp文件。我们自己定义的类也添加进去了。_</em></em>

_<em id="__mceDel"><em id="__mceDel">_</em></em>_三、在lua中调用自定义类_

_在刚才新的cocos2dx-lua的Demo工程中，在hello.lua文件中添加调用自定义类的函数：_

[cpp]

local function createSunnyLayer()
 local layerSunny = CCLayer:create()

 local labTips = CCLabelTTF:create(&quot;这个icon图标就是使用的自定义类&quot;, &quot;Arial&quot;, 18)
 labTips:setPosition(ccp(240,280))
 layerSunny:addChild(labTips)

 local sp = SNSprite:create(&quot;Icon.png&quot;)
 sp:setPosition(ccp(100,100))
 layerSunny:addChild(sp)

 return layerSunny
 end
 -- play background music, preload effec t

[/cpp]

添加到scene中：

[cpp]

-- run
 local sceneGame = CCScene:create() -- 创建场景
 --sceneGame:addChild(createLayerFarm()) -- 将农场层加入场景
 --sceneGame:addChild(createLayerMenu()) -- 将菜单界面层加入场景
 sceneGame:addChild(createSunnyLayer())
 CCDirector:sharedDirector():runWithScene(sceneGame)

[/cpp]

OK，xcode编译运行，就看到效果了。