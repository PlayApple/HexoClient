---
title: 'coco2d-x中编码问题。GBK转UTF-8 '
id: 57
categories:
  - Cocos2D-X
date: 2012-04-09 13:14:04
tags:
---

由于mac太卡了，在win32下写时，突然发现中文乱码了。没办法，编一个函数转一下。可能以后用得着。

调用：GBKToUTF8(str对象, “gb2312”, “utf-8”);

[cpp]

int GBKToUTF8(std::string &amp; gbkStr, const char* toCode, const char* fromCode)
{

       iconv_t iconvH;
       iconvH = iconv_open(fromCode, toCode);
       if (iconvH == 0)
       {
              return -1;
       }

       const char* strChar = gbkStr.c_str();
       const char** pin = &amp;strChar;
       size_t strLength = gbkStr.length();
       char* outbuf = (char*) malloc(strLength*4);
       char* pBuff = outbuf;

       memset( outbuf, 0, strLength*4);
       size_t outLength = strLength*4;
       if (-1 == iconv(iconvH, pin, &amp;strLength, &amp;outbuf, &amp;outLength))
       {
              iconv_close(iconvH);
              return -1;
       }
       gbkStr = pBuff;
       iconv_close(iconvH);
       return 0;
}

[/cpp]