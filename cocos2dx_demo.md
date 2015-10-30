Cocos2dx 平台
=========

NOTE: Cocos2dx平台中接入U8SDK，和原生平台的区别，仅仅就是多了C++和原生平台的通信机制。所以，Android中我们采用一个中间工程来完成U8SDK的接入，在中间工程中完成U8SDK和Cocos2dx的通信。iOS中，我们直接在Cocos2dx中调用U8SDK抽象层的接口

Android
---------

对于Android平台， 我们创建一个中间工程来接入U8SDK，同时负责U8SDK和Cocos2dx的通信。然后我们也封装了一套Cocos2dx中的C++接口。游戏中只需要将这些类放到游戏工程中，并配置，即可使用C++中对应的api。这套api同时抽象了android和iOS平台的接口，游戏中采用统一的接口调用。


下载地址：[U8SDK Cocos2dx Demo](http://pan.baidu.com/s/1jGfInh8)

关于Cocos2dx中接入U8SDK，你还可以看我们官方博客中对应的文章,了解详细的接入步骤和api：

1、[U8SDK——Cocos2dx游戏快速接入U8SDK（接入篇）](http://www.uustory.com/?p=1886)


iOS
---------

待续
