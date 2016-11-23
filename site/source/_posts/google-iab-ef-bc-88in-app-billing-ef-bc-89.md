---
title: Google IAB（In-App Billing）
tags:
  - IAB
id: 134
categories:
  - Android
date: 2012-04-12 06:57:48
---

今天要把我们上线的一款游戏移植到android上，由于游戏里面要用到内购，于是研究了下IAB。特整理了下资料。

Android Market IAB（In-app Billing） 是一个提供应用程序内支付处理的Android Market服务，它对于开发者在Android平台盈利有很大帮助。以下是其基本概念和工作流程的一个简要说明。

参考资料：http://developer.android.com/guide/market/billing/billing_overview.html
主要原理：
Android Market服务提供在你应用内的支付处理，服务会处理所有支付事物的细节，当支付完成后，服务会发送购买的细节给你的应用，包括订单号，订单时间，支付价格。你无需处理任何商业事物
架构：
IAB采用一种异步消息循环来在你的应用和Android Market服务器之间发送账单请求和应答。实际上，你的应用不会直接和Android Market服务器通讯，你的应用通过发送账单请求给Android Market的IPC并且从Android Market程序接收异步广播intents形式的应答。
也可以采用私有的服务器进行内容发送和事务验证，私有服务器可以保存业务历史记录，或者进行各种IAB的账单安全处理任务，比如签名验证等。
一个典型的IAB实现依赖与三个组件：
BillingService
处理应用的购买消息，发送付款请求
BillingReceiver
从Android Market 接收异步的账单处理的应答
Security
处理安全相关的任务，如签名验证，nonce生成等
其他组件：
ResponseHandler
提供应用程序特定的对购买提示，错误和其他状态消息的处理
PurchaseObserver
负责发送回调消息给应用，对界面上的购买信息和状态进行更新。
除了这些，你的应用还要提供存储用户购买信息的方法，以及给用户选择购买产品的界面。

![](http://houziadcocos2d.w112.mc-test.com/wp-content/uploads/2012/04/billing_arch1.png "billing_arch")

IAB消息：
IAB请求
MarketBillingService.sendBillingRequest(Bundle)
Bundle参数
BILLING_REQUEST（必须）
CHECK_BILLING_SUPPORTED：查看Android Market 程序是否支持IAB
REQUEST_PURCHASE：发送购买消息
GET_PURCHASE_INFORMATION：要求Android Market当购买成功、取消购买、退款等发生状态改变时，提示应用程序。
CONFIRM_NOTIFICATIONS：Android Market会发送状态改变的提示给你，除非你确认了提示
RESTORE_TRANSACTIONS：重装系统的情况下，可以通过发送这个请求来获取已购买的状态
API_VERSION
PACKAGE_NAME
ITEM_ID
NONCE
NOTIFY_IDS
DEVELOPER_PAYLOAD
IAB应答
有同步和异步的应答
同步应答是一个Bundle，包含
RESPONSE_CODE
PURCHASE_INTENT
REQUEST_ID
异步应答消息是独立的broadcast intents格式，包含：
com.android.vending.billing.RESPONSE_CODE
com.android.vending.billing.IN_APP_NOTIFY
包含一个或多个提示ID，当你接收到IN_APP_NOTIFY这个广播intent后，你要发送GET_PURCHASE_INFORMATION请求去获得消息详细内容。
com.android.vending.billing.PURCHASE_STATE_CHANGED
包含一个或多个事务的详细应答信息，用JSON格式表示
消息序列
购买请求的消息序列图

![](http://houziadcocos2d.w112.mc-test.com/wp-content/uploads/2012/04/billing_request_purchase.png "billing_request_purchase")

安全控制
Android Market为应答消息中的JSON数据签名。签名使用与Android Market账户关联的私钥进行。
Android Market返回的账单应答中包括未加密的JSON和签名，应用收到签名的应答后可以使用公钥验证其签名。
IAB同样也使用nonces（只能使用一次的随机数）来验证购买信息的完整性。当你发送GET_PURCHASE_INFORMATION和RESTORE_TRANSACTIONS请求的时候必须生成一个nonce。请求返回的时候，应用必须验证这个JSON中的nonce。
IAB的限制
必须是从Android Market中发布的应用
必须有Google Checkout账户
Android3.0，IAB必须5.0.12以上；其他版本需要IAB2.3.4以上版本。
Android1.6以上设备
只能买虚拟物品
Android Market不提供内容
必须联网