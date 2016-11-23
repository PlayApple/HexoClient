---
title: Cocos2dx-截屏并设置图片尺寸
id: 522
categories:
  - Code
date: 2014-03-10 03:17:33
tags:
---

前几天添加微信图片分享的时候，发现全屏截图超出了微信的数据包大小，所以截屏的时候可以考虑缩小尺寸到0.5倍。
下面的截屏代码：
<pre class="lang:default decode:true">void LHUtil::screenShoot()
{
    Size visibleSize = Director::getInstance()-&gt;getVisibleSize();

    //定义一个屏幕大小的渲染纹理
    RenderTexture* renderTexture = RenderTexture::create(visibleSize.width * .5, visibleSize.height * .5, Texture2D::PixelFormat::RGBA8888);

    Scene* curScene = Director::getInstance()-&gt;getRunningScene();
    Point ancPos = pCurScene-&gt;getAnchorPoint();

    //渲染纹理开始捕捉
    renderTexture-&gt;begin();

    // 缩小屏幕截屏区域
    curScene-&gt;setScale(.5);
    curScene-&gt;setAnchorPoint(cocos2d::Point(0, 0));

    //绘制当前场景
    curScene-&gt;visit();

    //结束
    renderTexture-&gt;end();

    //保存png
    renderTexture-&gt;saveToFile("screenshoot.png", Image::Format::PNG);

    // 恢复屏幕尺寸
    curScene-&gt;setScale(1);
    curScene-&gt;setAnchorPoint(ancPos);
    CC_SAFE_DELETE(curScene);
}</pre>

上面是cocos2dx的获取截屏的方法。

我顺便写下如何用OC的UIGraphicsBeginImageContext获取UIView转化成UIImage.
下面是ios方法：

<pre class="lang:default decode:true " >-(void)screenShot:(CGRect)rect{
    // 开始设置截屏区域
    UIGraphicsBeginImageContextWithOptions(rect.size, YES, 0);
    [[self.view layer] renderInContext:UIGraphicsGetCurrentContext()];
    UIImage *viewImage = UIGraphicsGetImageFromCurrentImageContext();
    UIGraphicsEndImageContext();

    // 获取image，可以根据需要进行尺寸修改
    CGImageRef imageRef = viewImage.CGImage;
    CGImageRef imageRefRect =CGImageCreateWithImageInRect(imageRef, rect);
    UIImage *image = [[UIImage alloc] initWithCGImage:imageRefRect];

    // 保存图片到相册, 这里会提示用户授权，不需要保存的话，可以取消
    UIImageWriteToSavedPhotosAlbum(image, nil, nil, nil);

    // 获取Documents目录
    NSArray *paths = NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES);
    NSString *documentsDirectory = [paths objectAtIndex:0];
    NSString *savedImagePath = [documentsDirectory stringByAppendingPathComponent:@"temScreenShot.png"];

    // 保存image到Documents目录
    NSData *imageData = UIImagePNGRepresentation(image);
    [imageData writeToFile:savedImagePath atomically:YES];
    CGImageRelease(imageRefRect);
}</pre> 