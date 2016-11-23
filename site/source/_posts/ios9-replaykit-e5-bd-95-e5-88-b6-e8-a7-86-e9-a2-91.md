---
title: iOS9 ReplayKit录制视频
id: 651
categories:
  - IPhone
date: 2015-12-11 02:54:36
tags:
---

猴子原创，欢迎转载。转载请注明： 转载自Cocos2Der-CSDN，谢谢！ 
原文地址: http://blog.csdn.net/cocos2der/article/details/50260873

iOS9的ReplayKit 视频录制，api非常简单，下面是代码，Objective-C的类似，就不写了。

<pre class="lang:default decode:true " >//
//  ReplayKitProxy.swift
//  ReplayKitDemo
//
//  Created by LiuYanghui on 15/12/11.
//  Copyright © 2015年 LiuYanghui. All rights reserved.
//

import UIKit
import ReplayKit

class ReplayKitProxy: NSObject, RPPreviewViewControllerDelegate {

    private var rootViewController: UIViewController?
    private var previewViewController: RPPreviewViewController?

    class var sharedInstance : ReplayKitProxy {
        struct Static {
            static var onceToken : dispatch_once_t = 0
            static var instance : ReplayKitProxy? = nil
        }
        dispatch_once(&amp;Static.onceToken) {
            Static.instance = ReplayKitProxy()
            Static.instance?.initData()
        }
        return Static.instance!
    }

    private func initData() {

    }

    /// 开始录制视频
    func startRecording() {
        let recorder = RPScreenRecorder.sharedRecorder()

        if recorder.available == false {
            print("Replaykit is not available")
            return
        }

        if recorder.recording == true {
            print("Replaykit is recording")
            return
        }

        recorder.startRecordingWithMicrophoneEnabled(true) { (error) -&gt; Void in
            if error != nil {
                print(error?.localizedDescription)
            }
        }
    }

    /// 停止录制视频
    func stopRecording() {
        let recorder = RPScreenRecorder.sharedRecorder()
        if recorder.recording == false {
            return
        }

        recorder.stopRecordingWithHandler { (previewViewController, error) -&gt; Void in
            if error != nil {
                print(error?.localizedDescription)
            }else if let viewController = previewViewController{
                viewController.previewControllerDelegate = self
                self.previewViewController = viewController
            }
        }
    }

    /// 删除已录制视频，必须在stopRecording之后调用
    func discardRecording() {
        let recorder = RPScreenRecorder.sharedRecorder()
        if recorder.recording == true {
            return
        }

        recorder.discardRecordingWithHandler { () -&gt; Void in
            print("discardRecording complete")
        }
    }

    /// 显示视频
    func displayRecordingContent() {
        // ipad需要修改显示方式，否则可能会导致sorceView为nil的异常
        if UIDevice.currentDevice().userInterfaceIdiom == UIUserInterfaceIdiom.Pad {
            self.previewViewController?.modalPresentationStyle = UIModalPresentationStyle.CurrentContext
            self.previewViewController?.popoverPresentationController?.sourceView = self.rootViewController?.view
        }
        rootViewController?.presentViewController(self.previewViewController!, animated: true, completion: { () -&gt; Void in
            print("display complete")
        })
    }

    // MARK: - delegate
    func previewControllerDidFinish(previewController: RPPreviewViewController) {
        previewController.dismissViewControllerAnimated(true) { () -&gt; Void in

        }
    }
}
</pre> 