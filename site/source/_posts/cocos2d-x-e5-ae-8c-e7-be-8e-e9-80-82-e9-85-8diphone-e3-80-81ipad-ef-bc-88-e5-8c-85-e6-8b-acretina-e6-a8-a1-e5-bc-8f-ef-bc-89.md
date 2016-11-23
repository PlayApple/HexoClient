---
title: cocos2d-x 完美适配iphone、ipad（包括retina模式）
tags:
  - 适配iphone-ipad
id: 245
categories:
  - Cocos2D-X
date: 2012-06-07 02:32:49
---

项目中需要同时适配iphone和ipad。美术提供的iphone的图是640x960的，ipad的图是768x1024的。发布的时候，项目肯定会分开发布iphone版和ipad版，但是开发过程中，我需要看到ipad上的效果，所以开发过程中不可能分开。

联想到高低清可以用-hd后缀区分，我就想到将所有ipad的768x1024图后面全部加-ipad后缀，程序中在ipad上就让它加载-ipad后缀的图。

现在来看看我的实现：（主要模仿的-hd的实现方式）

<span style="color: #ff0000;">1、首先宏定义 -ipad</span>

找到libs/cocos2dx/include/ccConfig.h，加入宏定义代码：
<pre>#ifndef CC_IPAD_DISPLAY_FILENAME_SUFFIX

#define CC_IPAD_DISPLAY_FILENAME_SUFFIX  "-ipad"

#endif</pre>
2、修改底层读取文件的地方，如果是ipad设备就添加后缀-ipad

打开libs/cocos2dx/platform/ios/CCFileUtils_ios.mm文件

找到下面函数，并做修改

static const char *static_ccRemoveHDSuffixFromFile( const char *pszPath){

#if CC_IS_RETINA_DISPLAY_SUPPORTED

if(cocos2d::CC_CONTENT_SCALE_FACTOR() == 2 ) {

//........

}

<span style="color: #ff0000;">//这里添加下面的代码：支持ipad图片 ------开始--------</span>

if( UI_USER_INTERFACE_IDIOM() ==UIUserInterfaceIdiomPad){

NSString *path = [NSString stringWithUTF8String: pszPath];

NSString *name = [path lastPathComponent];

NSString *suffix = [NSString stringWithUTF8String: CC_IPAD_DISPLAY_FILENAME_SUFFIX];

// check if path already has the suffix.

if( [name rangeOfString: suffix].location != NSNotFound ) {

CCLOG("cocos2d: Filename(%@) contains %@ suffix. Removing it. See cocos2d issue #1040", path, CC_RETINA_DISPLAY_FILENAME_SUFFIX);

NSString *newLastname = [name stringByReplacingOccurrencesOfString: suffix withString:@""];

NSString *pathWithoutLastname = [path stringByDeletingLastPathComponent];

return [[pathWithoutLastname stringByAppendingPathComponent:newLastname] UTF8String];

}

}

<span style="color: #ff0000;">//支持ipad图片 ------结束--------</span>

#endif // CC_IS_RETINA_DISPLAY_SUPPORTED

};

&nbsp;

找到下面函数，并修改：

static NSString* getDoubleResolutionImage(NSString* path)

{

#if CC_IS_RETINA_DISPLAY_SUPPORTED

&nbsp;

if( cocos2d::CC_CONTENT_SCALE_FACTOR() == 2 )

{

//.......

}

<span style="color: #ff0000;"> //这里添加支持ipad图片。--------开始----------</span>

if( UI_USER_INTERFACE_IDIOM() ==UIUserInterfaceIdiomPad )

{

NSString *pathWithoutExtension = [path stringByDeletingPathExtension];

NSString *name = [pathWithoutExtension lastPathComponent];

NSString *suffix = [NSString stringWithUTF8String: CC_IPAD_DISPLAY_FILENAME_SUFFIX];

// check if path already has the suffix.

if( [name rangeOfString: suffix].location != NSNotFound ) {

CCLOG("cocos2d: WARNING Filename(%@) already has the suffix %@. Using it.", name, CC_RETINA_DISPLAY_FILENAME_SUFFIX);

return path;

}

NSString *extension = [path pathExtension];

if( [extension isEqualToString:@"ccz"] || [extension isEqualToString:@"gz"] )

{

// All ccz / gz files should be in the format filename.xxx.ccz

// so we need to pull off the .xxx part of the extension as well

extension = [NSString stringWithFormat:@"%@.%@", [pathWithoutExtension pathExtension], extension];

pathWithoutExtension = [pathWithoutExtension stringByDeletingPathExtension];

}

NSString *retinaName = [pathWithoutExtension stringByAppendingString: suffix];

retinaName = [retinaName stringByAppendingPathExtension:extension];

NSFileManager *fileManager = [[[NSFileManager alloc] init] autorelease];

if( [fileManager fileExistsAtPath:retinaName] )

return retinaName;

CCLOG("cocos2d: CCFileUtils: Warning HD file not found: %@", [retinaName lastPathComponent] );

}

<span style="color: #ff0000;">//这里添加支持ipad图片。--------结束----------</span>

#endif // CC_IS_RETINA_DISPLAY_SUPPORTED

return path;

}
<div></div>
<div>可能你注意到了，我只是修改了里面的宏定义，所以并不需要写两个函数，跋宏定义拿出来，修改下就可以。我为了不动引擎自己的代码，就在下面写了一份。</div>
<div></div>
<div><span style="color: #ff0000;">3、将iOS Application Target 中的Devices改成Universal。</span></div>
<div></div>
<div><span style="color: #ff0000;">4、运行看看，如果你修改的是helloworld工程，那么请将HelloWorld.png复制一份，取名HelloWorld-ipad.png，并将其尺寸改成1024x768。这样iphone和ipad都可以运行了。都是全屏幕的哦。</span></div>
<div></div>
<div><span style="color: #ff0000;">5、如果你开启了retina。</span></div>
<div>ipad 的图不用修改。还是1024x768的-ipad图。但是iphone的图请用960x640的-hd图。（其实只有一套iphone的图的话，加不加-hd都可以的。但尺寸要是960x640的）</div>
<div></div>
<div><span style="color: #ff0000;">注：</span>ipad用的是ipad2，没有ipad3，所以ipad3可能尺寸有问题。大家可以试试看。有结果记得留言哦。</div>
&nbsp;