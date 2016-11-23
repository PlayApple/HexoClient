---
title: svn is already under version controll 的解决办法
tags:
  - SVN
id: 267
categories:
  - IPhone
date: 2012-07-04 01:26:14
---

出现svn is already under version controll 这个问题的原因是你所提交的文件或目录已经在其他SVN的管理下了，也就是说你提交的文件夹里面已经含有了.svn的目录。需要先把它们删除才能提交。

删除的方法可以直接删或者用命令行处理：

<span style="color: #ff0000;">命令行：</span>
terminal，cd到你要提交的目录，输入命令：

#find . -mindepth 2 -name '.svn' -exec rm -rf '{}' \;         （这个命令我没有成功，所以加了个下面的命令。）

或者

terminal，cd到你要提交的目录，输入命令：

find ./ -name .svn        (显示该目录下所有的.svn)文件

find ./ -name .svn | xargs rm -rf       (删除该目录下所有的.svn)文件

<span style="color: #ff0000;">手工删：</span>
首先要显示mac的隐藏文件，注意有大小写。

<span style="color: #ff0000;">显示Mac隐藏文件的命令：defaults write com.apple.finder AppleShowAllFiles -bool true</span>
<span style="color: #ff0000;">隐藏Mac隐藏文件的命令：defaults write com.apple.finder AppleShowAllFiles -bool false</span>

或者

<span style="color: #ff0000;">显示Mac隐藏文件的命令：defaults write com.apple.finder AppleShowAllFiles YES</span>
<span style="color: #ff0000;">隐藏Mac隐藏文件的命令：defaults write com.apple.finder AppleShowAllFiles NO</span>

输完单击Enter键，退出终端，重新启动Finder就可以了
重启Finder：窗口左上角的苹果标志 &gt; 强制退出 &gt; Finder &gt; 重新启动