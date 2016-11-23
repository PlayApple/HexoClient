---
title: 'cocos2d-x的CCDirector::replaceScene(CCScene *pScene) 使用注意 '
id: 42
categories:
  - IPhone
date: 2012-04-09 12:36:17
tags:
---

昨晚在捕鱼切换场景时发现会出现内存不足的现象，看了下代码发现了个问题。我们先看看代码
`
[[CCDirector sharedDirector] runWithScene:[ SceneA scene]];
[[CCDirector sharedDirector] replaceScene:[ SceneB scene]];
`
假设CCDirector以SceneA为起始场景，现在要替换为SceneB。那么：

&nbsp;

&nbsp;
<table border="1" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td valign="top" width="568">将会触发一系列的方法被调用，如下：
1\. scene: <wbr><wbr> <wbr><wbr>  <wbr> <wbr> <wbr> <wbr> <wbr> <wbr> <wbr> <wbr> <wbr> <wbr> <wbr> <wbr> <wbr> <wbr> <wbr> <wbr> <wbr> SceneB
2\. init: <wbr><wbr> <wbr><wbr> <wbr><wbr>  <wbr> <wbr> <wbr> <wbr> <wbr> <wbr> <wbr> <wbr> <wbr> <wbr> <wbr> <wbr> <wbr> <wbr> <wbr> <wbr> <wbr> <wbr>SceneB
3\. onEnter:  <wbr> <wbr> <wbr> <wbr> <wbr> <wbr> <wbr> <wbr> <wbr> <wbr> <wbr> <wbr> <wbr> <wbr> <wbr> <wbr> <wbr> SceneB
4\. Transition 发生.
5\. onExit: <wbr><wbr>  <wbr> <wbr> <wbr> <wbr> <wbr> <wbr> <wbr> <wbr> <wbr> <wbr> <wbr> <wbr> <wbr> <wbr> <wbr> <wbr>  <wbr>SceneA
6\. onEnterTransitionDidFini<wbr><wbr>sh:<wbr>SceneB</wbr></wbr></wbr></wbr></wbr></wbr></wbr></wbr></wbr></wbr></wbr></wbr></wbr></wbr></wbr></wbr></wbr></wbr></wbr></wbr></wbr></wbr></wbr></wbr></wbr></wbr></wbr></wbr></wbr></wbr></wbr></wbr></wbr></wbr></wbr></wbr></wbr></wbr></wbr></wbr></wbr></wbr></wbr></wbr></wbr></wbr></wbr></wbr></wbr></wbr></wbr></wbr></wbr></wbr></wbr></wbr></wbr></wbr></wbr></wbr></wbr></wbr></wbr></wbr></wbr></wbr></wbr></wbr></wbr></wbr></wbr></wbr></wbr></wbr></wbr></wbr></wbr></wbr></wbr></wbr></wbr></wbr></wbr></wbr>7\. dealloc:  <wbr> <wbr> <wbr> <wbr> <wbr> <wbr> <wbr> <wbr> <wbr> <wbr> <wbr> <wbr> <wbr> <wbr> <wbr> <wbr>  <wbr>SceneB</wbr></wbr></wbr></wbr></wbr></wbr></wbr></wbr></wbr></wbr></wbr></wbr></wbr></wbr></wbr></wbr></wbr>

 <wbr></wbr>

当一个scene被搬上“台面”，它会收到onEnter，SceneB会收到onExit，最后交替完成在台面上的SceneA收到onEnterTransitionDidFini<wbr><wbr>sh。从中我们可以看出，只有当登台的SceneA完全加载完成，下场的SceneB才会被释放。两个scene在某一帧是同时处在内存里的，内存自然会表示压力山大。</wbr></wbr></td>
</tr>
</tbody>
</table>
&nbsp;

&nbsp;

再看看cocos2d-x的源代码
`
void CCDirector::replaceScene(CCScene *pScene)
{
       assert(pScene != NULL);

       unsigned int index = m_pobScenesStack-&gt;count();

       m_bSendCleanupToScene = true;
       m_pobScenesStack-&gt;replaceObjectAtIndex(index - 1, pScene);

       m_pNextScene = pScene;
 // replaceScene赋值m_pNextScene，以致drawScene会触发setNextScene函数。
}

void CCDirector::setNextScene(void)
｛
       ···;
       if (! (newSceneType &amp; ccTransitionScene))
       {
         if (m_pRunningScene)
         {
           m_pRunningScene-&gt;onExit(); //即 5\. onExit:    SceneA
         }   
       }
         ···;
}
`
释放一些new出来的对象，应该在onExit()里面delete掉。之前不知道原理，new出来的对象在destroy()里面delete，结果就是运行时弹窗口了…