---
title: MacOS的菜单状态栏App添加饼型进度
tags:
  - Swift
id: 676
categories:
  - Swift
date: 2016-07-30 14:32:02
---

猴子原创，欢迎转载。转载请注明： 转载自Cocos2Der-CSDN，谢谢！ 
原文地址: http://blog.csdn.net/cocos2der/article/details/52075418

使用swift如何开发一个MacOS的状态栏App，上一篇已经讲了。里面我忘记提如何修改状态icon为饼型进度。比如App在处理什么事情的时候，可以添加进度状态提示用户。如下图所示：
![这里写图片描述](http://img.blog.csdn.net/20160730215539215)

    /// 显示状态栏菜单饼型进度
        private func showStatusItemProgress() {
            if let button = statusItem.button {
                // FIXME: it works, but obviously not good.
                button.subviews.removeAll()
            }
            if let button = statusItem.button {
                // FIXME: it works, but obviously not good.
                let frame = NSRect(x: 6, y: 2, width: 18, height: 18)
                let progressIndicator = NSProgressIndicator(frame: frame)
                progressIndicator.style = .SpinningStyle
                progressIndicator.indeterminate = false
                progressIndicator.minValue = 0
                progressIndicator.maxValue = 100
                progressIndicator.doubleValue = 0
                self.progressIndicator = progressIndicator

                // 当添加进度后，发现状态栏frame大小错误了，没找到解决办法，但是填充一个图片可以解决这个尺寸错误问题
                statusItem.image = NSImage(named: "EmptyIcon")
                statusItem.image?.template = true
                button.addSubview(progressIndicator)
            }
        }

请注意，
- 我们是把一个NSProgressIndicator添加到了NSStatusItem的button视图上，不要添加到view视图上，如果添加到view上，view会挡住状态栏点击事件，这样NSStatusItem会不能响应用户点击了。（当然，如果你app恰好不需要用户点击，可以这样做）
- 你应该留意到了，我在NSStatusItem的image里放了一张透明图片，因为这了是为了解决它frame大小错误的问题

当然，如果你知道如何解决的话，可以告诉我下。哈哈。