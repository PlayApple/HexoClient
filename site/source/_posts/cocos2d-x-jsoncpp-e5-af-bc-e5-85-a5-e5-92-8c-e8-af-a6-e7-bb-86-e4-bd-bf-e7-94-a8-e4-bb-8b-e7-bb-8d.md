---
title: cocos2d-x JsonCpp 导入和详细使用介绍
tags:
  - JsonCpp
id: 299
categories:
  - Cocos2D-X
date: 2012-10-11 10:23:01
---

Json我就不介绍了，我主要说下怎么在cocos2dx中加入并使用json，我选的是JsonCpp这个库。

一、首先去下载JsonCpp这个库。

解压后，找到include的文件夹。将它们复制到2dx工程的libs目录，然后加入到工程中。最后效果如下：（截图中其他的不要管，看include就可以了）。

[![](http://www.cocos2dev.com/wp-content/uploads/2012/10/2dx_jsoncpp.png "2dx_jsoncpp")](http://www.cocos2dev.com/wp-content/uploads/2012/10/2dx_jsoncpp.png)

好了，就这么简单，现在你可以使用jsoncpp这个库了，在使用的地方引用头文件json.h就可以了。

二、jsoncpp的一些使用方法介绍：（网上也有很多介绍，我只是写下我工作中遇到的一些）

1、初始化

Json::Value root;

Json::Reader reader;

reader.parse("{"name":"sunny"}", root);

Reader可以用来初始化一个人json从字符串。

2、读取json文件初始化

这是我写的一个方法：

[cpp]

Json::Value BYJsonDataManager::getJsonFromFile(const char* fileName){
Json::Reader reader;
ifstream file(getFullPath(fileName));
CCAssert(file.is_open(), &quot;file is open fail!&quot;);
Json::Value root;
if  (!reader.parse(file, root, false )) {
CCAssert(false, &quot;Json::Reader Parse error!&quot;);
}
return root;
}

const char* BYJsonDataManager::getFullPath(const char* path){
return cocos2d::CCFileUtils::sharedFileUtils()-&gt;fullPathFromRelativePath(path);
}

[/cpp]

3、解析json

首先生成一个json

Json::Value myjson = getJsonFromFile("test.json");//利用上面的函数生成一个json。

int num = myJson["num"].asInt();

string str = myJson["name"].asString();

4、json数组

Json::Value myjson = getJsonFromFile("test.json");//利用上面的函数生成一个json。

int i = 0;

Json::Value arr = myjson[i];//获取arr数组的第一个元素

5、利用迭代器获取json的key。（有时候并不知道json的key，这个时候可以利用迭代器获取json的key）

Json::Value myjson = getJsonFromFile("test.json");//利用上面的函数生成一个json。

Json::Value::Members members(myjson.getMemberNames());

for (Json::Value::Members::iterator it = members.begin(); it != members.end(); ++it)  {
const std::string &amp;key = *it;
}

6、自己拼装Json数组，（有时候发往服务器的数据是一个json数据）

Json::Value arr;

for(int i = 0 ;i &lt; 5;++i){

Json::Value myjson = getJsonFromFile("test.json");//利用上面的函数生成一个json。

arr.append(protocolData);

}

如果想让这个jsonArr有key。

Json::Value arr2;

arr2["array"] = arr;

OK ,目前只记得这些，等我想起其它的再补充。一些比较简单的没有都介绍。

&nbsp;

<span style="color: #ff0000;">20130402更新：</span>

<span style="color: #ff0000;">如果导入后发现无法识别json头文件的，请下载下面的源文件，里面包含了上面提到的libs文件</span>

<span style="color: #ff0000;">http://vdisk.weibo.com/s/vvUmP/1364549033</span>