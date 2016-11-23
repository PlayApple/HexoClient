---
title: Swift下多个Storyboard的项目结构
tags:
  - Storyboard
id: 681
categories:
  - IPhone
  - Swift
date: 2016-11-23 10:07:39
---

我是个比较喜欢用storyboard和xib的人。我个人的习惯就是，能用storyboard的一定不用代码手工撸。当然自己业余个人写的项目，基本上一个storyboard就搞定了。但涉及到多人合作下时候，一个storyboard还是挺蛋疼的，冲突难解决，打开storyboard极容易出现修改。结构大的时候打开还卡（也可能是我电脑太屌丝啦。。。）

下面介绍下我使用多个storyboard的习惯，仅供参看，不一定是最好的方案。

### 一、storyboard结构

![这里写图片描述](http://img.blog.csdn.net/20161114112720003)

1.  默认的Main.storyboard下我只有一个UITabBarController，如果你建立的是TabItem的模板项目，这个应该是默认的。注意，没有tabItem。
2.  给对应的每个TabBarItem建立对应的storyboard，比如我建立的MyTabItemOne.storyboard和MyTabItemTwo.storyboard。
3.  MyTabItemOne.storyboard下我放的是一个UINavigationController作为初始Controller。
4.  MyTabItemTwo.storyboard类似

### 二、代码层面

自定义UITabBarController和UINavigationController。将Main.storyboard下的UITabBarController改为自定义的SPTabBarController.swift。将MyTabItemOne（Two）.storyboard下的UINavigationController改为自定义的SPNavigationController.swift

SPTabBarController.swift

    //
    //  SPTabBarController.swift
    //  SampleProject
    //
    //  Created by LiuYanghui on 2016/11/14.
    //  Copyright © 2016年 LiuYanghui. All rights reserved.
    //

    import UIKit

    class SPTabBarController: UITabBarController {

        override func viewDidLoad() {
            super.viewDidLoad()

            // Do any additional setup after loading the view.
            // 添加所有子控制器
            addChildViewControllers()
        }

        override func didReceiveMemoryWarning() {
            super.didReceiveMemoryWarning()
            // Dispose of any resources that can be recreated.
        }

        // MARK: - private method

        /// 添加所有子控制器
        private func addChildViewControllers() {
            setChildrenController(title: "One", image: UIImage(), selectedImage: UIImage(), storyboard: UIStoryboard(name: "MyTabItemOne", bundle: nil))

            setChildrenController(title: "Two", image: UIImage(), selectedImage: UIImage(), storyboard: UIStoryboard(name: "MyTabItemTwo", bundle: nil))

        }

        /// 添加一个子控制器
        private func setChildrenController(title:String, image:UIImage,selectedImage:UIImage, storyboard:UIStoryboard) {
            let controller = storyboard.instantiateInitialViewController()!
            controller.tabBarItem.title = title
            controller.tabBarItem.image = image
            controller.tabBarItem.selectedImage = selectedImage
            addChildViewController(controller)
        }
    }
    `</pre>

    SPNavigationController.swift

    <pre>`//
    //  SPNavigationController.swift
    //  SampleProject
    //
    //  Created by LiuYanghui on 2016/11/14.
    //  Copyright © 2016年 LiuYanghui. All rights reserved.
    //

    import UIKit

    class SPNavigationController: UINavigationController {

        override func viewDidLoad() {
            super.viewDidLoad()

            // Do any additional setup after loading the view.
            self.interactivePopGestureRecognizer?.delegate = nil
        }

        override func pushViewController(_ viewController: UIViewController, animated: Bool) {
            if viewControllers.count &gt; 0 {
                //隐藏tabbar
                viewController.hidesBottomBarWhenPushed = true

                viewController.navigationItem.leftBarButtonItem = setBackBarButtonItem()
            }
            super.pushViewController(viewController, animated: animated)
        }

        func back(){
            self.popViewController(animated: true)
        }

        // MARK: - Private method
        private func setBackBarButtonItem() -&gt; UIBarButtonItem{
            let button = UIButton(type: .custom)
            button.setImage(UIImage(named: "navigationButtonReturn"), for: .normal)
            button.setImage(UIImage(named: "navigationButtonReturnClick"), for: .highlighted)
            button.setTitle("返回", for: .normal)
            button.setTitleColor(UIColor(red: 81/255, green: 81/255, blue: 81/255, alpha: 1), for: .normal)
            button.addTarget(self, action: #selector(SPNavigationController.back), for: .touchUpInside)
            button.frame.size = CGSize(width: 100, height: 30)
            // button 里的内容左对齐
            button.contentHorizontalAlignment = .left
            button.contentEdgeInsets = UIEdgeInsets(top: 0, left: -10, bottom: 0, right: 0)

            return UIBarButtonItem(customView: button)
        }

        override func didReceiveMemoryWarning() {
            super.didReceiveMemoryWarning()
            // Dispose of any resources that can be recreated.
        }
    }

Xcode工程
![这里写图片描述](http://img.blog.csdn.net/20161114114013691)

[Sample Project Github](https://github.com/PlayApple/SampleProject)