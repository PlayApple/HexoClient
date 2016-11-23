---
title: xcode 4.2 自己添加 MainWindow.xib
id: 104
categories:
  - IPhone
date: 2012-04-10 05:23:17
tags:
---

xcode 4.2 不再支持 Window-Based Application 的解决办法：
1.创建空项目 Empty Application。（在Xcode4.2下创建的这个空项目不再有MainWindow.xib文件了。）
2.Ctrl+N，创建User Interface下面的Window（选择“iOS-&gt;User Interface-&gt;Window）,命名成MainWindow.xib(这名称可随意，只是以MainWindow我们更熟悉)。
3.File Owner修改成UIApplication。（即调出show identity inspector面板，将file's Owner的属性Custom Class中将class改为UIApplication。）

[![](http://www.cocos2dev.com/wp-content/uploads/2012/04/12.png "1")](http://www.cocos2dev.com/wp-content/uploads/2012/04/12.png)
4.从Library库中，拖个Object出来添加，并修改类为Delegate的那个类，最开始自动生成的那个。（即将新添加的Object的Class改为AppDelegate）

![](http://hi.csdn.net/attachment/201202/10/0_1328867343Tz2T.gif)

![](http://hi.csdn.net/attachment/201202/10/0_13288673594d9z.gif)

5.把Delegate类的属性window声明加上IBOutlet，这样才好在IB里面连接。（即将AppDelegate中UIWindow属性标记为IBOutlet）

即@property (strong,nonatomic) UIWindow *window;修改为：@property (strong, nonatomic) IBOutlet UIWindow *window;

然后，把MainWindow.xib的FileOwner的delegate设置为你的AppDelegate。FileOwner的delegate和AppDelegate(Object)连接：(蓝色连线哦)

![](http://hi.csdn.net/attachment/201202/10/0_1328867374877m.gif)
把Window和你AppDelegate中的Outlet关联起来：

![](http://hi.csdn.net/attachment/201202/10/0_13288673839OQt.gif)

上图即AppDelegate的outlets和Window连接起来。
6.(可以看到，已经有一个Window对象，此window对象就是iphone的屏幕。)把window属性连接到IB中默认出现的Window。
7.给刚才的代理再加个@property (strong,nonatomic)IBOutletUITabBarController* rootController;属性。
8.在IB中拖个TabBarController出来，并连接到前面的Object的rootController。
9.在代理的didFinishLaunchingWithOptions函数中修改下

- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions

{

// Override point for customization after application launch.

// self.window = [[UIWindow alloc] initWithFrame:[[UIScreen mainScreen] bounds]];

[self.windowaddSubview:rootController.view];

[self.windowmakeKeyAndVisible];

return YES;

}

不需要再创建window，然后把rootcontroller的view添加到当前window下面就可以了。注释掉 -(BOOL)application:didFinishLaunchingWithOptions:方法中与我们从XIB加载相冲突的方法调用(self.window = [[UIWindow alloc] initWithFrame:[[UIScreen mainScreen] bounds]];)。

10.关键一个步骤，打开项目属性，在Summary下面的Main Interface里面选择MainWindow完事。