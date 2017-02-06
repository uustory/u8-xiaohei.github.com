Unity3D 平台
=========

NOTE: Unity平台中接入U8SDK，和原生平台的区别，仅仅就是多了Unity3D和原生平台的通信机制。所以，Android中我们采用一个中间工程来完成U8SDK的接入，在中间工程中完成U8SDK和Unity的通信。iOS中，我们直接在Unity中调用U8SDK抽象层的接口

Android
---------

对于Android平台， 我们创建一个中间工程来接入U8SDK，同时负责U8SDK和Unity的通信。然后我们也封装了一套Unity中的接口，游戏中只需要导入u8sdk_unity.unitypackage到工程中，即可使用Unity中对应的api。这套api同时抽象了android和iOS平台的接口，游戏中采用统一的接口调用。


下载地址：[U8SDK Unity Demo](http://pan.baidu.com/s/1gE3JC) (注意：需要依赖购买的抽象层框架和打包工具一起使用)

关于Unity中接入U8SDK，你还可以看我们官方博客中对应的文章,了解详细的接入步骤和api：

1、[U8SDK——Unity手游接入U8SDK（Android篇）](http://www.uustory.com/?p=1859)
2、[U8SDK——Unity手游接入U8SDK（Unity篇）](http://www.uustory.com/?p=1866)


iOS
---------

对于iOS平台， 我们直接调用U8SDK抽象层的接口完成接入。游戏中只需要导入u8sdk_unity.unitypackage到工程中，即可使用Unity中对应的api。这套api同时抽象了android和iOS平台的接口，游戏中采用统一的接口调用。

下载地址（和Android是同一个）：[U8SDK Unity Demo](http://pan.baidu.com/s/1gE3JC) (注意：需要依赖购买的抽象层框架和打包工具一起使用)