---
title: Swift基础用法（Swift开发之一）
tags:
  - Iphone
  - Swift
id: 557
categories:
  - IPhone
date: 2014-06-03 03:01:56
---

昨晚苹果发布了新一代编程语言Swift，官方提供了一个iBook的说明文档，有需要的可以看下。地址：[https://itunes.apple.com/cn/book/swift-programming-language/id881256329?mt=11](https://itunes.apple.com/cn/book/swift-programming-language/id881256329?mt=11 "Swfit")

下面我先说下基本的一些东西，大家都是刚看，所以我也难免会出现理解错误的地方。欢迎指出。当然你也可以直接看官方的文档。

一、let，var基本类型

let：常量，不能二次赋值。赋值时可以不需要指定类型，编译器根据赋值自动判断。也可以手工指明类型。
var：变量

<pre class="lang:default decode:true " >let a = 12 // 申明a为常量12，类型Int
let b: Double = 12 // 申明a为常量12，手工指定类型Double
</pre> 

二、字符串

1、加号可以直接拼接字符串，不能直接拼接其他类型转化成string

<pre class="lang:default decode:true " >let str = "Hello, playground. "
let name = "Tom "
let count = 10
let say = str + name + String(count)  // "Hello, playground. Tom 10"</pre> 

2、 字符串中直接引入代码

<pre class="lang:default decode:true " >let a = 1
let b = 2
let say = "the number is \(a + b)"  // "the number is 3"</pre> 

三、数组、字典 都可以直接使用 [] 声明

1、数组

<pre class="lang:default decode:true " >var arr = ["catfish", "water", "tulips", "blue paint"]
arr[1] = "bottle of water" // 修改第二项
arr // ["catfish", "bottle of water", "tulips", "blue paint"]</pre> 

2、字典

<pre class="lang:default decode:true " >var man = [
    "like": "apple",
    "name": "Tom",
]
man["iphone"] = "5s"  // 增加字段
man["like"] = "mac"  // 修改字段
man // ["iphone": "5s", "like": "mac", "name": "Tom"]</pre> 

四、控制语句

1、if  不可以直接使用一个变量或者常量来作为条件，必须是判断语句

<pre class="lang:default decode:true " >let a = 12
if a &gt; 0 {
    // do something
} else {
   // do something
}</pre> 

如果你写成 if a {} ，就会报错，不能直接使用变量或常量判断bool

2、for

<pre class="lang:default decode:true " >for i in 0..3 {
   // i = 0, 1, 2
}

for var i = 0; i &lt; 3; ++i {
    // i = 0, 1, 2
}

// 数组
let scores = [1, 2, 3, 4, 5]
for score in scores {
    // score 
}

// 字典
let numsDic = [
    "a": [2, 3, 5, 7, 11, 13],
    "b": [1, 1, 2, 3, 5, 8],
    "c": [1, 4, 9, 16, 25],
]
for (key, numbers) in numsDic {
    for number in numbers {
        // number
    }
}</pre> 

3、while

<pre class="lang:default decode:true " >var n = 2
while n &lt; 100 {
    n = n * 2
}

var m = 2
do {
    m = m * 2
} while m &lt; 100
</pre> 