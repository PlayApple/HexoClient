---
title: MAC下搭建Android Eclipse开发环境
id: 128
categories:
  - Android
date: 2012-04-12 00:46:51
tags:
---

**1.****下载****jdk**

 **[http://support.apple.com/downloads/](http://support.apple.com/downloads/)**

![](http://houziadcocos2d.w112.mc-test.com/wp-content/uploads/2012/04/2-300x190.png "java for mac")

根据mac 版本 下载相应的Java开发工具

**2****下载****mac****版本的****android sdk**　[http://developer.android.com/sdk/index.html](http://developer.android.com/sdk/index.html)

并解压

**3****安装Fastboot for Mac OS插件，**

[http://wiki.smartphonefrance.info/(X(1)S(sosnpkvottciqs45qsiadm55))/GetFile.aspx?File=android/fastboot-osx-10_5.zip](file://localhost/%0D%0Ahttp/:wiki.smartphonefrance.info:(X(1)S(sosnpkvottciqs45qsiadm55)):GetFile.aspx%3FFile=android:fastboot-osx-10_5.zip)

**4 ****下载完成后将　****Fastboot ****复制到**** /androidsdk/tools ****目录下（如果****SDK****包中包含**** fastboot****，可跳过此步骤）**

**5.****配置环境变量**

打开终端

输入下面的命令来创建一个新的 .bash_profile 文件,输入

pico .bash_profile

在新出现的修改提示中输入

export PATH=$PATH:/AndroidSDK/tools

按“control+X”保存（确认按“Y”），然后退出并重启终端，至此，Mac OS X 下就可以使用 Adb 和 Fastboot 工具了．

**6****下载****Eclipse**

在[http://www.eclipse.org/downloads/](http://www.eclipse.org/downloads/)里找到适合的版本

选择Mac Carbon 32bit 然后进入下载页面

下载，并解压安装

**7.****打开eclipse 选择一个 workspace 用来存放 代码**

**8.****安装android ADT （若无法安装，请看文章结尾的注意事项）**

 点击菜单栏的Help -&gt; Install new software...

add一个地址源。

Name: 用来标识  可以随便写

Location: [https://dl-ssl.google.com/android/eclipse/](https://dl-ssl.google.com/android/eclipse/)， 点击OK

等下载列表出现后，

选择Developer Tools， 点击下一步

点击下一步

此时开始下载安装android ADT 插件， 保持网络通畅

安装完成后， 出现以下对话框， 点击YES 重启 eclipse

**9\. ****设置****Android SDK****位置**

点击菜单栏Eclipse -&gt; 偏好设置  -&gt; Android

在SDK Location 处 指向下载的 android SDK 文件夹

**10\. ****打开window -&gt; Android SDK and AVD manager**

**更新SDK ：**

 点击Available Packages

选中全部   点击Install Selected （可以选择部分， 建议一定要选择SDK Platform Android 1.5, API 3, revision 3）

选择Accept All  然后点击 Install

开始更新 SDK

**更新完SDK后， 创建 AVD**

点击右边的 New...

输入AVD Name 选择 Target  以及添加相应的 Hardware （添加上所有的硬件）

点击Create AVD 创建成功

**11.****安装 SVN 插件**

点击菜单栏的Help -&gt; Install new software...

Location: [http://subclipse.tigris.org/update_1.4.x](http://subclipse.tigris.org/update_1.4.x)， 点击OK

和 安装 ADT 一样

选择上所有 点击 Next

选择 accept  点击Next

开始 下载安装  请保持网络畅通

安装完以后 重启 eclipse

**12.****配置****SVN:**

点击 Eclipse的右上角Open perspective 项 -&gt;  other

选择 SVN 资源库研究

在左侧空白处点击右键 -&gt; 新建 -&gt;资源库位置

点击finish

打开链接 出现以上两个文件夹 选择 **EAPhone** 点击右键 选择 **检出为****...**

点击Finish 开始 检出 项目

**13.****运行项目：（若项目中存在错误， 请看后边备注进行调试）**

选中项目名 点击右键 -&gt; Run As -&gt; Android Application

运行成功

**备注：项目调试**

如果检出的项目有错误出现 执行以下操作

1.重新导入android.jar 包

在EAPhone 项目名上点右键 -&gt; Properties

选择 java Build Path -&gt; Libraries

若存在Libraries -&gt; Remove掉

选择AddLibrary

选择 User Library

点击 User Libraries...

点击 右侧 New...

输入 Library Name 比如 android 1.5

新建以后 列表中出现 android 1.5

选中  点击右侧 Add JARs...

出现选择文件选择对话框

选择 android SDK -&gt; platforms -&gt; android-3 -&gt; android.jar

打开 (建议选择android-3)

完成后  选中 新建的Library 点击finish

2.修改eclipse jdk 版本

Window -&gt; Properties -&gt; Java -&gt; Compiler

将右侧 jdk 版本 改为 1.6  点击OK

<span style="color: #ff0000;">注意事项：</span>

1、如果ADT下载不下来，请手工下载ADT的压缩包，

下载地址：http://dl.google.com/android/ADT-18.0.0.zip

解压后，将里面的features文件夹里面的jar包和plugins里面的jar包对应的复制到eclipse的features文件夹和plugins文件夹。之后你的eclipse就会出现ADT