---
title: Cocos2d-x 2.x CCSprite 灰白图的生成（利用shader设置）
tags:
  - CCSprite
  - 灰白图
id: 325
categories:
  - Cocos2D-X
date: 2012-11-02 06:38:12
---

[![](http://www.cocos2dev.com/wp-content/uploads/2012/11/1.jpg "1")](http://www.cocos2dev.com/wp-content/uploads/2012/11/1.jpg)

游戏中人物死掉后要把人物头像变成灰白图，很多游戏也是这样处理，问题来了，怎么将CCsprite生成的图变成灰白呢？

看了下实现，基本有了办法。CCSprite是在initWithTexture的时候渲染的贴图，如果在这里面设置一个一个灰度Shader，也许可以将图片改成灰白色。

GL Shader脚本代码：

[cpp]

#ifdef GL_ES
precision mediump float;
#endif
uniform sampler2D u_texture;
varying vec2 v_texCoord;
varying vec4 v_fragmentColor;
void main(void)
{
// Convert to greyscale using NTSC weightings
float alpha = texture2D(u_texture, v_texCoord).a;
float grey = dot(texture2D(u_texture, v_texCoord).rgb, vec3(0.299, 0.587, 0.114));
gl_FragColor = vec4(grey, grey, grey, alpha);
}
[/cpp]

这个主要目的是将RGB值转换为YUV值，Y就是灰度，所以我们取出里面的Y值来实现我们的灰白图。

Google了下，vec3(0.299, 0.587, 0.114) 是RGB转YUV的参数值。

计算完了之后根据原来的纹理alpha值，输出我们处理后的颜色到gl_FragColor，就可以让shader去渲染黑白图。

里面主要是在光栅化后的片段进行颜色处理，最后输出的是像素，它不会产生额外纹理，所以不会卡。

<span style="color: #ff0000;">注意：</span>GLSL1.0的可能有些函数不支持，会crash。不过目前基本可以不考虑。

下面就是我写好的类，加入自己的工程就可以了。

头文件

[cpp]

//
// BYGraySprite.h
// Demo
//
// Created by Yanghui Liu on 12-11-2.
// Copyright (c) 2012年 BoyoJoy. All rights reserved.
//

#ifndef Demo_BYGraySprite_h
#define Demo_BYGraySprite_h

#include &quot;cocoa/CCGeometry.h&quot;
#include &quot;cocos2d.h&quot;
USING_NS_CC;

class BYGraySprite : public CCSprite{

public:
 BYGraySprite();
 virtual ~BYGraySprite();
 static BYGraySprite* create(const char* pszFileName);
 bool initWithTexture(CCTexture2D* pTexture, const CCRect&amp; tRect);
 virtual void draw();

};

#endif

[/cpp]

cpp文件

[cpp]

//
// BYGraySprite.cpp
// Demo
//
// Created by Yanghui Liu on 12-11-2.
// Copyright (c) 2012年 BoyoJoy. All rights reserved.
//

#include &quot;BYGraySprite.h&quot;

BYGraySprite::BYGraySprite(){

}

BYGraySprite::~BYGraySprite(){

}

BYGraySprite* BYGraySprite::create( const char* pszFileName ){
 BYGraySprite* graySprite = new BYGraySprite;
 if (graySprite &amp;&amp; graySprite-&gt;initWithFile(pszFileName)){
 graySprite-&gt;autorelease();
 return graySprite;
 }else{
 CC_SAFE_RELEASE(graySprite);
 return NULL;
 }
}

bool BYGraySprite::initWithTexture(CCTexture2D* pTexture, const CCRect&amp; tRect ){
 do{
 CC_BREAK_IF(!CCSprite::initWithTexture(pTexture, tRect));

 GLchar* pszFragSource =
 &quot;#ifdef GL_ES \n \
 precision mediump float; \n \
 #endif \n \
 uniform sampler2D u_texture; \n \
 varying vec2 v_texCoord; \n \
 varying vec4 v_fragmentColor; \n \
 void main(void) \n \
 { \n \
 // Convert to greyscale using NTSC weightings \n \
 float grey = dot(texture2D(u_texture, v_texCoord).rgb, vec3(0.299, 0.587, 0.114)); \n \
 gl_FragColor = vec4(grey, grey, grey, 1.0); \n \
 }&quot;;

 CCGLProgram* pProgram = new CCGLProgram();
 pProgram-&gt;initWithVertexShaderByteArray(ccPositionTextureColor_vert, pszFragSource);
 this-&gt;setShaderProgram(pProgram);
 pProgram-&gt;release();
 CHECK_GL_ERROR_DEBUG();

 this-&gt;getShaderProgram()-&gt;addAttribute(kCCAttributeNamePosition, kCCVertexAttrib_Position);
 this-&gt;getShaderProgram()-&gt;addAttribute(kCCAttributeNameColor, kCCVertexAttrib_Color);
 this-&gt;getShaderProgram()-&gt;addAttribute(kCCAttributeNameTexCoord, kCCVertexAttrib_TexCoords);
 CHECK_GL_ERROR_DEBUG();

 this-&gt;getShaderProgram()-&gt;link();
 CHECK_GL_ERROR_DEBUG();

 this-&gt;getShaderProgram()-&gt;updateUniforms();
 CHECK_GL_ERROR_DEBUG();

 return true;
 } while (0);
 return false;
}

void BYGraySprite::draw(){
 ccGLEnableVertexAttribs(kCCVertexAttribFlag_PosColorTex );
 ccGLBlendFunc( m_sBlendFunc.src, m_sBlendFunc.dst );

 this-&gt;getShaderProgram()-&gt;use();
 this-&gt;getShaderProgram()-&gt;setUniformForModelViewProjectionMatrix();

 ccGLBindTexture2D( this-&gt;getTexture()-&gt;getName() );

#define kQuadSize sizeof(m_sQuad.bl)
 long offset = (long)&amp;m_sQuad;

 // vertex
 int diff = offsetof( ccV3F_C4B_T2F, vertices);
 glVertexAttribPointer(kCCVertexAttrib_Position, 3, GL_FLOAT, GL_FALSE, kQuadSize, (void*) (offset + diff));

 // texCoods
 diff = offsetof( ccV3F_C4B_T2F, texCoords);
 glVertexAttribPointer(kCCVertexAttrib_TexCoords, 2, GL_FLOAT, GL_FALSE, kQuadSize, (void*)(offset + diff));

 // color
 diff = offsetof( ccV3F_C4B_T2F, colors);
 glVertexAttribPointer(kCCVertexAttrib_Color, 4, GL_UNSIGNED_BYTE, GL_TRUE, kQuadSize, (void*)(offset + diff));
 glDrawArrays(GL_TRIANGLE_STRIP, 0, 4);
 CC_INCREMENT_GL_DRAWS(1);
}

[/cpp]

使用方法：

[cpp]

BYGraySprite* graySprite = BYGraySprite::create(&quot;Icon.png&quot;);
 graySprite-&gt;setPosition( ccp(size.width/2, size.height/2) );
 this-&gt;addChild(graySprite);

[/cpp]

<span style="color: #ff0000;">2012.11.14更新</span>
<span style="color: #ff0000;">二、之前alpha值是直接用的1，所以透明图会渲染成黑色。把原来纹理的alpha取出来并使用。就可以避免该问题。</span></pre>
也就是将GL脚本代码中的

float grey = dot(texture2D(u_texture, v_texCoord).rgb, vec3(0.299, 0.587, 0.114)); \n \

gl_FragColor = vec4(grey, grey, grey, 1.0); \n \

改成:

vec4 col = texture2D(u_texture, v_texCoord); \n \

float grey = dot(col.rgb, vec3(0.299, 0.587, 0.114)); \n \

gl_FragColor = vec4(grey, grey, grey, col.a); \n \