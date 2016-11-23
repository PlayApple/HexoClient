---
title: 通过cell的UIButton获取UITableViewCell的行数
tags:
  - UITableViewCell
id: 265
categories:
  - IPhone
date: 2012-07-02 01:25:33
---

xib是个很好的东西，但是有时候在画cell的时候，里面的控件我会给它设置tag，用来在cellForRowAtIndexPath中获取每个cell的子控件，如果子控件有个UIButton，你给它设置targeta后，在响应的方法里没办法分是从哪一个cell的button触发的，因为所有的cell的那个UIbutton的tag是一样的。

&nbsp;

既然不许改变tag，有没有其他办法知道它的父容器Cell的行数呢？知道行数了不会知道是哪一个cell的Button触发的。有了思路就不怕没办法。

&nbsp;

我想到了利用Button的title。就是把不用的Button的状态，赋值title，title就是它所在cell的行数。然后在target中获取title，并转为int。得到的int就是行数了。

&nbsp;

设置title：

NSString* cellStr1 = [NSString stringWithFormat:@"%d", indexPath.row];

[btn_attention setTitle:cellStr1 forState:UIControlEventTouchCancel];

&nbsp;

获取title,并转为行数：

NSString* cellIndex = [sender titleForState:UIControlEventTouchCancel];

int tag =[cellIndex intValue];

&nbsp;

这样就可以实现了。

&nbsp;

不过一般来说最好是自定义cell，每个cell处理自己内部控件的响应。

&nbsp;

如何使用，看自己的需求了，毕竟代码是死的，思路是活的。