---
title: Cocos2d-x HelloLua 介绍
tags:
  - cocos2d-x
  - lua
id: 393
categories:
  - Cocos2D-X
date: 2013-03-28 10:16:04
---

[![2](http://www.cocos2dev.com/wp-content/uploads/2013/03/2.jpg)](http://www.cocos2dev.com/wp-content/uploads/2013/03/2.jpg)[![1](http://www.cocos2dev.com/wp-content/uploads/2013/03/1.jpg)](http://www.cocos2dev.com/wp-content/uploads/2013/03/1.jpg)

最近网游使用lua做更新比较火，最近也有人问我能否写下lua的文章。刚新建了一个lua工程，看了下代码，就干脆先介绍下HelloLua吧。边看代码边写的注释，应该基本都写了。

[cpp]
-- for CCLuaEngine traceback 输出绑定执行函数发生错误的信息
function __G__TRACKBACK__(msg)
 print(&quot;----------------------------------------&quot;)
 print(&quot;LUA ERROR: &quot; .. tostring(msg) .. &quot;\n&quot;)
 print(debug.traceback())
 print(&quot;----------------------------------------&quot;)
end

local function main()
 -- avoid memory leak 设置脚本内存回收参数 避免内存泄露
 collectgarbage(&quot;setpause&quot;, 100)
 collectgarbage(&quot;setstepmul&quot;, 5000)

-- 就是local function cclog(...) 定义局部Log函数
 local cclog = function(...)
 print(string.format(...))
 end

-- 类似c++的include，会检查是否重复引入
 require &quot;hello2&quot;

-- 调用外部函数，在hello2.lua中
 cclog(&quot;result is &quot; .. myadd(3, 5))

---------------

-- 获取可视区域
 local visibleSize = CCDirector:sharedDirector():getVisibleSize()
 -- 可视原点坐标 OpenGL坐标系 左下角为原点
 local origin = CCDirector:sharedDirector():getVisibleOrigin()

-- add the moving dog 创建小松鼠
 local function creatDog()

-- 每一帧尺寸设置，local表示局部变量
 local frameWidth = 105
 local frameHeight = 95

-- create dog animate 加载动画资源并创建精灵帧
 -- 加载精灵动画所在纹理
 local textureDog = CCTextureCache:sharedTextureCache():addImage(&quot;dog.png&quot;)
 -- 设置第一帧帧区域
 local rect = CCRectMake(0, 0, frameWidth, frameHeight)
 -- 创建第一帧精灵Frame
 local frame0 = CCSpriteFrame:createWithTexture(textureDog, rect)
 -- 设置第二帧帧区域
 rect = CCRectMake(frameWidth, 0, frameWidth, frameHeight)
 -- 创建第二帧精灵Frame
 local frame1 = CCSpriteFrame:createWithTexture(textureDog, rect)

-- 基于使用第一帧Frame创建Sprite对象
 local spriteDog = CCSprite:createWithSpriteFrame(frame0)
 spriteDog.isPaused = false
 spriteDog:setPosition(origin.x, origin.y + visibleSize.height / 4 * 3)

-- 将上面创建的两帧生成一个帧数组（这个松鼠一共就2帧）
 local animFrames = CCArray:create()

animFrames:addObject(frame0)
 animFrames:addObject(frame1)

-- 根据帧序列数组创建一个动画animation。帧间隔时间delay等于0.5秒
 local animation = CCAnimation:createWithSpriteFrames(animFrames, 0.5)
 -- 根据动画animation创建动作实例
 local animate = CCAnimate:create(animation);
 -- 松鼠精灵执行该动作
 spriteDog:runAction(CCRepeatForever:create(animate))

-- moving dog at every frame 用来更新松鼠的位置，后面会调用该函数
 local function tick()
 if spriteDog.isPaused then return end
 local x, y = spriteDog:getPosition()
 if x &gt; origin.x + visibleSize.width then
 x = origin.x
 else
 x = x + 1
 end

spriteDog:setPositionX(x)
 end

-- 生成一个schedule，每帧执行tick函数
 CCDirector:sharedDirector():getScheduler():scheduleScriptFunc(tick, 0, false)

return spriteDog
 end

-- create farm 创建地面的农场
 local function createLayerFarm()
 -- 创建一个新的Layer用作农场管理
 local layerFarm = CCLayer:create()

-- add in farm background 添加农场背景图
 local bg = CCSprite:create(&quot;farm.jpg&quot;)
 bg:setPosition(origin.x + visibleSize.width / 2 + 80, origin.y + visibleSize.height / 2)
 layerFarm:addChild(bg)

-- add land sprite 添加地免砖块
 for i = 0, 3 do
 for j = 0, 1 do
 local spriteLand = CCSprite:create(&quot;land.png&quot;)
 spriteLand:setPosition(200 + j * 180 - i % 2 * 90, 10 + i * 95 / 2)
 layerFarm:addChild(spriteLand)
 end
 end

-- add crop 添加庄稼，注意crop.png是多张图的合成贴图，所以只取了里面的部分贴图
 local frameCrop = CCSpriteFrame:create(&quot;crop.png&quot;, CCRectMake(0, 0, 105, 95))
 for i = 0, 3 do
 for j = 0, 1 do
 local spriteCrop = CCSprite:createWithSpriteFrame(frameCrop);
 spriteCrop:setPosition(10 + 200 + j * 180 - i % 2 * 90, 30 + 10 + i * 95 / 2)
 layerFarm:addChild(spriteCrop)
 end
 end

-- add moving dog 调用上面的creatDog()方法，创建一个移动的松鼠
 local spriteDog = creatDog()
 layerFarm:addChild(spriteDog)

-- handing touch events 手指触摸事件处理
 local touchBeginPoint = nil

-- 手指点击开始
 local function onTouchBegan(x, y)
 cclog(&quot;onTouchBegan: %0.2f, %0.2f&quot;, x, y)
 touchBeginPoint = {x = x, y = y} -- 保存点击位置
 spriteDog.isPaused = true -- 将松鼠暂停移动
 -- CCTOUCHBEGAN event must return true 这里必须返回ture，否则后续touch事件无法接收
 return true
 end

-- 手指按住移动
 local function onTouchMoved(x, y)
 cclog(&quot;onTouchMoved: %0.2f, %0.2f&quot;, x, y)
 if touchBeginPoint then
 -- 将整个农场层拖动，因为之前已经将农场里面所有对象加入在layerFarm
 local cx, cy = layerFarm:getPosition()
 layerFarm:setPosition(cx + x - touchBeginPoint.x,
 cy + y - touchBeginPoint.y)
 touchBeginPoint = {x = x, y = y}
 end
 end

-- 手指离开
 local function onTouchEnded(x, y)
 cclog(&quot;onTouchEnded: %0.2f, %0.2f&quot;, x, y)
 touchBeginPoint = nil -- 点击位置数据清空
 spriteDog.isPaused = false -- 回复松鼠移动
 end

-- touch事件的接收函数
 local function onTouch(eventType, x, y)
 if eventType == &quot;began&quot; then
 return onTouchBegan(x, y)
 elseif eventType == &quot;moved&quot; then
 return onTouchMoved(x, y)
 else
 return onTouchEnded(x, y)
 end
 end

-- 注册touch事件
 layerFarm:registerScriptTouchHandler(onTouch)
 layerFarm:setTouchEnabled(true)

return layerFarm
 end
 -- create menu 创建界面菜单
 local function createLayerMenu()
 -- 创建一个新的CCLayer管理所有菜单
 local layerMenu = CCLayer:create()

local menuPopup, menuTools, effectID

-- 点击菜单回调函数
 local function menuCallbackClosePopup()
 -- stop test sound effect 关闭音效
 SimpleAudioEngine:sharedEngine():stopEffect(effectID)
 menuPopup:setVisible(false) -- 隐藏菜单
 end

-- 点击菜单回调函数
 local function menuCallbackOpenPopup()
 -- loop test sound effect 打开音效
 local effectPath = CCFileUtils:sharedFileUtils():fullPathForFilename(&quot;effect1.wav&quot;)
 effectID = SimpleAudioEngine:sharedEngine():playEffect(effectPath)
 menuPopup:setVisible(true) -- 显示菜单
 end

-- add a popup menu 创建弹出的菜单面板
 local menuPopupItem = CCMenuItemImage:create(&quot;menu2.png&quot;, &quot;menu2.png&quot;)
 menuPopupItem:setPosition(0, 0)
 menuPopupItem:registerScriptTapHandler(menuCallbackClosePopup) -- 注册点击回调地址
 menuPopup = CCMenu:createWithItem(menuPopupItem)
 menuPopup:setPosition(origin.x + visibleSize.width / 2, origin.y + visibleSize.height / 2)
 menuPopup:setVisible(false)
 layerMenu:addChild(menuPopup)

-- add the left-bottom &quot;tools&quot; menu to invoke menuPopup 左下角的工具按钮,用来弹出菜单面板
 local menuToolsItem = CCMenuItemImage:create(&quot;menu1.png&quot;, &quot;menu1.png&quot;)
 menuToolsItem:setPosition(0, 0)
 menuToolsItem:registerScriptTapHandler(menuCallbackOpenPopup) -- 注册点击回调地址
 menuTools = CCMenu:createWithItem(menuToolsItem)
 local itemWidth = menuToolsItem:getContentSize().width
 local itemHeight = menuToolsItem:getContentSize().height
 menuTools:setPosition(origin.x + itemWidth/2, origin.y + itemHeight/2)
 layerMenu:addChild(menuTools)

return layerMenu
 end

-- play background music, preload effect

-- uncomment below for the BlackBerry version
 -- local bgMusicPath = CCFileUtils:sharedFileUtils():fullPathForFilename(&quot;background.ogg&quot;)
 local bgMusicPath = CCFileUtils:sharedFileUtils():fullPathForFilename(&quot;background.mp3&quot;)
 SimpleAudioEngine:sharedEngine():playBackgroundMusic(bgMusicPath, true) -- 播放背景音乐
 local effectPath = CCFileUtils:sharedFileUtils():fullPathForFilename(&quot;effect1.wav&quot;)
 SimpleAudioEngine:sharedEngine():preloadEffect(effectPath) -- 预加载音效

-- run
 local sceneGame = CCScene:create() -- 创建场景
 sceneGame:addChild(createLayerFarm()) -- 将农场层加入场景
 sceneGame:addChild(createLayerMenu()) -- 将菜单界面层加入场景
 CCDirector:sharedDirector():runWithScene(sceneGame)
end

--[[
xpcall( 调用函数, 错误捕获函数 );
lua提供了xpcall来捕获异常
xpcall接受两个参数:调用函数、错误处理函数。
当错误发生时,Lua会在栈释放以前调用错误处理函数,因此可以使用debug库收集错误相关信息。
两个常用的debug处理函数:debug.debug和debug.traceback
前者给出Lua的提示符,你可以自己动手察看错误发生时的情况;
后者通过traceback创建更多的错误信息,也是控制台解释器用来构建错误信息的函数。
--]]
xpcall(main, __G__TRACKBACK__)

[/cpp]

写得还算详细的，后面会慢慢介绍。