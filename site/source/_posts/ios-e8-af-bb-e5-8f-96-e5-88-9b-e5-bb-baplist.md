---
title: iOS读取/创建plist
tags:
  - plist
id: 471
categories:
  - Code
date: 2013-11-12 09:26:15
---

[objc]

//写入plist文件
NSMutableDictionary* dict1 = [ [ NSMutableDictionary alloc ] initWithContentsOfFile:@&quot;/Test.plist&quot; ];
[ dict1 setObject:@&quot;Yes&quot; forKey:@&quot;isCheck&quot; ];
[ dict1 writeToFile:@&quot;/Test.plist&quot; atomically:YES ];

//读取plist文件
NSMutableDictionary* dict2 = [ [ NSMutableDictionary alloc ] initWithContentsOfFile:@&quot;/Test.plist&quot; ];
NSString* object = [ dict2 objectForKey:@&quot;isCheck&quot; ];
NSLog(@&quot;isCheck--&gt; %@&quot;, object);

[/objc]