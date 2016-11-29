---
title: MacOS获取辅助功能权限控制鼠标点击事件
date: 2016-11-29 15:22:26
categories:
  - Swift
tags:
	- Swift
	- 鼠标
---

昨晚玩一个模拟经营的游戏，由于升级太慢我就不停的种树卖树来换取经验值。不过重复点击10几分钟后，实在受不了。网上本来准备找个鼠标自动点击的软件用用。结果没找到趁手的。如是自己写了个。

自己设置需要点击的一组动作，长按（100，200），点击（576，789），点击（750，550）。类似在对应坐标点操作鼠标。

原理非常简单，我就不贴代码了，主要说下如何控制鼠标点击事件。

#### 注册系统辅助权限，这里会触发用户授权

```
let opts = NSDictionary(object: kCFBooleanTrue,
                        forKey: kAXTrustedCheckOptionPrompt.takeUnretainedValue() as NSString
            ) as CFDictionary
        
guard AXIsProcessTrustedWithOptions(opts) == true else { return }
```

#### 鼠标事件
```
// 鼠标左键按下
let mouseDown = CGEvent(mouseEventSource: nil,
						mouseType: .leftMouseDown,
						mouseCursorPosition: CGPoint(x: 200, y: 300),
						mouseButton: .left
)
mouseDown?.post(tap: .cghidEventTap)

// 鼠标左键抬起
let mouseUp = CGEvent(mouseEventSource: nil,
					  mouseType: .leftMouseUp,
                      mouseCursorPosition: CGPoint(x: 200, y: 300),
                      mouseButton: .left
)
mouseUp?.post(tap: .cghidEventTap)
```

以上两个事件，组成了一个鼠标左键在坐标（200，300）点击事件

#### MouseType支持类型
```
/* Constants that specify the different types of input events. */
public enum CGEventType : UInt32 {

    
    /* The null event. */
    case null

    
    /* Mouse events. */
    case leftMouseDown

    case leftMouseUp

    case rightMouseDown

    case rightMouseUp

    case mouseMoved

    case leftMouseDragged

    case rightMouseDragged

    
    /* Keyboard events. */
    case keyDown

    case keyUp

    case flagsChanged

    
    /* Specialized control devices. */
    case scrollWheel

    case tabletPointer

    case tabletProximity

    case otherMouseDown

    case otherMouseUp

    case otherMouseDragged

    
    /* Out of band event types. These are delivered to the event tap callback
       to notify it of unusual conditions that disable the event tap. */
    case tapDisabledByTimeout

    case tapDisabledByUserInput
}
```

#### 长按事件
有人可能会留意到上述没有长按事件

长按事件就是 .leftMouseDown, 达到你需要长按的时间后，再触发.leftMouseUp

#### 扩展
一般情况你做好一系列点击组合后，是用快捷键触发开始的。那么MacOS中如何监听系统快捷键呢或者键盘事件？

添加系统键盘监听
```
NSEvent.addGlobalMonitorForEvents(matching: .keyDown, handler: {
	[unowned self] event in
	// num1:18, num2:19
	if event.keyCode == 18 {
		// self.startAction()
	}
})
```

