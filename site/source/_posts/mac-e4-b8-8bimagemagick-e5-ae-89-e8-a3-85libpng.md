---
title: ' Mac下ImageMagick安装(libpng)'
tags:
  - ImageMagick
  - libpng
id: 602
categories:
  - IPhone
date: 2015-03-07 10:57:45
---

猴子原创，欢迎转载。转载请注明： 转载自Cocos2Der-CSDN，谢谢！

原文地址: http://blog.csdn.net/cocos2der/article/details/42562705

今天由于项目需求，需要使用ImageMagick，安装完成后，处理png遇到

convert: no decode delegate for this image format错误

这个主要是libpng没有安装。下面记录下安装的过程。假设你之前没有安装libpng。

一、下载ImageMagick，libpng

ImageMagick下载地址：http://www.imagemagick.org/download/ImageMagick.tar.gz

libpng下载地址：libpng-1.6.16.tar.gz

ImageMagick官网安装介绍：http://www.imagemagick.org/script/install-source.php#unix

ImageMagick delegates list：http://www.imagemagick.org/download/delegates/

二、安装libpng

$ tar xvzf libpng-1.6.16.tar.gz
$ cd libpng-1.6.16
$ ./configure
$ make
$ sudo make install
三、添加环境变量，添加完毕后重启终端生效

export CPPFLAGS='-I/usr/local/include'
export LDFLAGS="-L/usr/local/lib"

四、安装ImageMagick

$ tar xvzf ImageMagick.tar.gz
$ cd ImageMagick-6.9.0-3
$ ./configure
$ make
$ sudo make install

注意：安装ImageMagick，执行./configure查看安装配置的时候，注意查看是否支持png，注意下图，no表示目前安装不支持。如果是no请检查是否步骤有误。

[![20150109224408484](http://www.cocos2dev.com/wp-content/uploads/2015/03/20150109224408484.png)](http://www.cocos2dev.com/wp-content/uploads/2015/03/20150109224408484.png)