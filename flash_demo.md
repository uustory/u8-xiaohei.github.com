Flash 平台
=========

NOTE: Flash平台中接入U8SDK，和原生平台的区别，仅仅就是多了AIR和原生平台的通信机制，我们采用官方的ANE实现。所以，Android中我们采用一个中间工程来完成U8SDK的接入，在中间工程中完成U8SDK和AIR的通信。

Android
---------

Flash中接入U8SDK，需要使用Air ANE的方式 ，按照air官方ANE文档，我们需要实现以下几个部分。 以下仅仅简单说明下接入的步骤。完整工程和实现代码请参考自己购买的U8SDK中U8SDKDemo目录下的AIR相关的几个工程。

1、Android接入部分(U8SDKANELib)：负责U8SDK2抽象层接口的调用，同时将回调通知到AIR中。是U8SDK和游戏的中间工程
2、AS中接入部分(U8SDKExtension): 负责在as层中调用Android层的接口，同时接收Android层的事件回调
3、ANE打包，将上面两部分，通过ane打包，融合成一个ANE，被游戏工程引用
4、游戏工程中引用ANE，完成接入。


下载地址：[U8SDK Flash Demo](http://pan.baidu.com/s/1eQsJVay)

关于Flash中接入U8SDK，你还可以看我们官方博客中对应的文章,了解详细的接入步骤和实现原理：

[U8SDK——Flash游戏快速接入U8SDK](http://www.uustory.com/?p=2036)


iOS
---------

待续
