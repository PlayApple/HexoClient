---
title: 监听手机截屏事件
tags:
  - Swift
  - 截屏
id: 665
categories:
  - IPhone
date: 2016-06-29 02:09:19
---

猴子原创，欢迎转载。转载请注明： 转载自Cocos2Der-CSDN，谢谢！
原文地址: http://blog.csdn.net/cocos2der/article/details/51780954

今天无意中在百度地图中截屏路线的时候，顶部出现提示我的截屏信息。这细节挺好的，省去我后面需要使用该截屏的繁琐步骤。刚好手头空闲会，我也写个玩玩。哈哈哈~~

截屏在iOS7以前是需要使用小技巧来获取用户截屏事件的，iOS7之后，apple开放了用户截屏通知事件，所以现在做起来还是挺方便的。
<pre class="lang:default decode:true ">// This notification is posted after the user takes a screenshot (for example by pressing both the home and lock screen buttons)
@available(iOS 7.0, *)
public let UIApplicationUserDidTakeScreenshotNotification: String</pre>

注册通知
<pre class="lang:default decode:true ">NSNotificationCenter.defaultCenter().addObserver(self, selector: #selector(ViewController.userDidTakeScreenshot), name: UIApplicationUserDidTakeScreenshotNotification, object: nil)</pre>

代码实现（swift）

<pre class="lang:default decode:true " >/// 用户截屏完毕
    func userDidTakeScreenshot() {
        // 当前屏幕的image
        // 注意：为什么不直接从相册读取截屏图像
        //（万一用户直接拒绝可权限你不跪了，何况截屏之后，用户可不知道你会提示，第一反应肯定拒绝读取相册的权限）
        let image = imageWithScreenshot()

        let imageView = UIImageView(frame: CGRect(x: 50, y: 50, width: 320, height: 640))
        imageView.image = image
        self.view.addSubview(imageView)
    }

    /// 获取当前屏幕图片
    func imageWithScreenshot() -&gt; UIImage? {
        let imageData = dataWithScreenshotInPNGFormat()
        return UIImage(data: imageData)
    }

    /// 截取当前屏幕
    func dataWithScreenshotInPNGFormat() -&gt; NSData {
        var imageSize = CGSizeZero
        let screenSize = UIScreen.mainScreen().bounds.size
        let orientation = UIApplication.sharedApplication().statusBarOrientation
        if UIInterfaceOrientationIsPortrait(orientation) {
            imageSize = screenSize
        } else {
            imageSize = CGSizeMake(screenSize.height, screenSize.width)
        }

        UIGraphicsBeginImageContextWithOptions(imageSize, false, 0)
        let context = UIGraphicsGetCurrentContext()

        for window in UIApplication.sharedApplication().windows {
            CGContextSaveGState(context)
            CGContextTranslateCTM(context, window.center.x, window.center.y)
            CGContextConcatCTM(context, window.transform)
            CGContextTranslateCTM(context, -window.bounds.size.width * window.layer.anchorPoint.x, -window.bounds.size.height * window.layer.anchorPoint.y)

            if orientation == UIInterfaceOrientation.LandscapeLeft {
                CGContextRotateCTM(context, CGFloat(M_PI_2))
                CGContextTranslateCTM(context, 0, -imageSize.width)
            } else if orientation == UIInterfaceOrientation.LandscapeRight {
                CGContextRotateCTM(context, -CGFloat(M_PI_2))
                CGContextTranslateCTM(context, -imageSize.height, 0)
            } else if (orientation == UIInterfaceOrientation.PortraitUpsideDown) {
                CGContextRotateCTM(context, CGFloat(M_PI))
                CGContextTranslateCTM(context, -imageSize.width, -imageSize.height)
            }
            if window.respondsToSelector(#selector(UIView.drawViewHierarchyInRect(_:afterScreenUpdates:))) {
                window.drawViewHierarchyInRect(window.bounds, afterScreenUpdates: true)
            } else {
                window.layer.renderInContext(context!)
            }
            CGContextRestoreGState(context);
        }
        let image = UIGraphicsGetImageFromCurrentImageContext()
        UIGraphicsEndImageContext()

        return UIImagePNGRepresentation(image)!
    }</pre> 

注意：为什么不直接从相册读取截屏图像？ 
万一用户直接拒绝可权限你不跪了，何况截屏之后，用户可不知道你会提示，第一反应肯定拒绝读取相册的权限。