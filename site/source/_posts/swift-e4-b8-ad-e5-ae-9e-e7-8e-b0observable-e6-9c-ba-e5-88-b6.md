---
title: Swift中实现Observable机制
tags:
  - Observable
  - Swift
id: 669
categories:
  - IPhone
date: 2016-07-15 06:39:30
---

猴子原创，欢迎转载。转载请注明： 转载自Cocos2Der-CSDN，谢谢！ 
原文地址: http://blog.csdn.net/cocos2der/article/details/51917539

今天给别人讲个Observable的实现和使用场景，结合Observable-Swift github: https://github.com/slazyk/Observable-Swift 讲了半天貌似还没有特别明白，故写了个简易的实现，讲述了下Observable属性监控机制。

<pre class="lang:default decode:true " >//: Playground - noun: a place where people can play

import UIKit
import Foundation

// MARK: - Observable
class Observable&lt;T&gt; {
    // 定义block结构
    typealias Observer = T -&gt; Void

    // 申明一个block，用于数据改变的执行
    private var observer: Observer?

    // 数据发生变更，则通过observer告知
    var value: T {
        didSet {
            observer?(value)
        }
    }

    init(_ v: T) {
        value = v
    }

    func observe(observer: Observer?) {
        self.observer = observer
        observer?(value)
    }
}

// MARK: - People
struct PeopleModel {
    let firstName: Observable&lt;String&gt;
    let lastName: Observable&lt;String&gt;

    init(firstName: String, lastName: String) {
        self.firstName = Observable(firstName)
        self.lastName = Observable(lastName)
    }
}

// MARK: - Test

// test1
let people = PeopleModel(firstName: "sunny", lastName: "liu")
people.firstName.observe {
    newValue in
    print("firstName changed: \(newValue)")
}
people.lastName.observe {
    print("lastName changed: \($0)")
}
people.firstName.value = "sunny2"
people.lastName.value = "liu2"

// test2
class House {
    let lableHouseName =  UILabel()

    init() {

    }

    var people: PeopleModel? {
        didSet {
            people?.firstName.observe{
                [unowned self] in
                self.lableHouseName.text = $0
            }
        }
    }
}

</pre> 

这样貌似容易理解了，O(∩_∩)O哈哈~