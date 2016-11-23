---
title: IOS开发证书显示“此证书的签发者无效”解决方法
id: 656
categories:
  - IPhone
tags:
---

猴子原创，欢迎转载。转载请注明： 转载自Cocos2Der-CSDN，谢谢！ 
原文地址: http://blog.csdn.net/cocos2der/article/details/50668116

今天早上同事说咱们的证书无法使用了，显示“此证书的签发者无效”。一开始以为谁误操作了证书，查看后发现所有证书都无效了。查了会才发下原来是Apple Worldwide Developer Relations Certification Authority Intermediate Certificate证书刚好在今天过期了。

1\. 首先打开自己的Keychain，删除原来已经过期的WWDR证书。（这里我已经删除并更新了） 

2\. 在Apple PKI网页下载最新的证书：https://developer.apple.com/certificationauthority/AppleWWDRCA.cer ，双击导入即可。

现在证书无效的问题已经全部正常了。