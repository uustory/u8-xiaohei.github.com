U8Server
==========

NOTE:U8Server是统一的渠道SDK登录认证中心和支付中心，是U8SDK统一接入框架中，不可缺少的一部分。目前，U8Server已经支持了50多家渠道SDK，并且提供开源版本，开源地址：[github](https://github.com/u8-xiaohei/U8Server)

U8Server后台管理系统，在线演示地址：[U8Server演示地址](u8server_manager.md)

U8Server企业版后台管理系统，在线演示地址：[U8Server演示地址](http://121.42.144.254/admin/index)
用户名和密码：admin/admin


开源地址
----------

U8Server开源地址：[github](https://github.com/u8-xiaohei/U8Server)
视频教程(U8Server):[U8Server视频教程](http://www.chuanke.com/2869716-164072.html)
企业版和开源版本的主要区别，企业版后台管理系统中，增加了权限管理，完善了数据查询，增加了数据统计分析功能。 SDK登录认证和支付回调，开源版本和企业版完全相同

简介
----------

```
U8Server主要负责的功能，一个是用于渠道SDK的登录认证；另一个是作为各个渠道SDK的支付回调中心。之所以称他为“统一”登录认证中心和支付中心，是因为他的设计，支持多款游戏。同时，U8Server还提供了一个简单的后台管理界面，可以在此基础上，进行完善，增加用户统计和支付统计等功能。

U8Server采用成熟的J2EE框架[Struts2+Spring+Hibernate]进行研发，可以采用主流的Web容器进行部署，比如Tomcat、Weblogic、websphere等。

U8Server数据存储采用数据库，所以，你需要选择一个数据库，可以采用Mysql，或者其他数据库。在db目录下，我们有提供sql文件，用于直接导入测试数据。
```

主要功能
----------

简单罗列下，U8Server的主要功能如下：

```
1、渠道SDK登录认证
2、获取订单号
3、支付回调
4、后台管理
	4.1 权限管理：后台权限的管理，多种不同角色登录可以操作的功能不同
	4.2 游戏管理：增加，修改，删除，查询
	4.3 渠道商管理：渠道商指的是诸如九游、当乐、360、百度等渠道的通用数据。增加，修改，删除，查询
	4.4 渠道管理：每个游戏下面都对应多个渠道，每个渠道所属一个渠道商。增加，修改，删除，查询，统计
	4.5 用户管理：每个渠道SDK的用户第一次登录认证的时候，会在U8Server本地生成一个对应的用户，和当前SDK的用户进行绑定。增加，修改，删除，查询，统计
	4.6 订单管理：每个用户进行支付的时候，会生成一个订单数据。增加，修改，删除，查询，统计
	......
```


支持渠道
---------

U8Server目前已经接好的渠道如下，后续我们还会不断地继续支持更多的渠道SDK，敬请期待......

Android：

序号 | 名称 | 开放平台 | 地址
-----|------|----------|------
1|UC| 九游开发平台|http://game.open.uc.cn/
2|当乐|当乐网开发平台|http://open.d.cn/
3|百度、91、多酷|百度移动游戏|http://dev.91.com/
4|360| 360手机开发平台|http://dev.360.cn/
5|小米|小米开发者中心|http://dev.xiaomi.com/
6|4399|4399手机开发平台|http://open.4399.cn/
7|OPPO(可可游戏中心)|OPPO开发者社区|http://open.oppomobile.com/
8|VIVO(步步高)|VIVO开发者平台|https://developer.vivo.com.cn/
9|拇指玩|拇指玩开发者中心|http://open.muzhiwan.com
10|联想|联想开放平台|http://open.lenovo.com/developer/
11|华为|华为开发者联盟|http://developer.huawei.com/
12|应用汇|应用汇开发者中心|http://dev.appchina.com
13|友游(云点联动)|友游(云点联动)|http://www.gameoao.com/
14|安智|安智开发者联盟|http://dev.anzhi.com/
15|木蚂蚁|木蚂蚁开发者平台|http://dev.mumayi.com/
16|叉叉助手(果盘)|叉叉助手|http://www.xxzhushou.cn/
17|泡椒网|泡椒网|http://www.paojiao.cn/
18|魅族|魅族开发者社区|http://developer.meizu.com/
19|豌豆荚|豌豆荚开发者中心|http://developer.wandoujia.com/
20|酷派|酷派移动计费平台|http://pay.coolpadtone.com/
21|金立|金立游戏开发者中心|http://dev.game.4wei.cn/
22|偶玩|偶玩安全支付|http://www.ouwan.com/
23|应用宝(MSDK)|腾讯应用宝|http://open.qq.com/
24|爱游戏(网游和单机)|电信爱游戏|http://open.play.cn/dev/
25|联通UniPay|联通沃支付|http://dev.wostore.cn/
26|移动MM|移动MM|http://dev.10086.cn/
27|和游戏(移动咪咕)|和游戏|http://g.10086.cn/open/
28|优酷|优酷游戏|http://wan.youku.com/
29|酷狗|酷狗游戏|http://youxi.kugou.com/
30|逗逗SDK|盛大游戏|http://dsdk.sdo.com/
31|乐嗨嗨|乐嗨嗨游戏|http://www.lehihi.com/
32|游龙(19196)|游龙|http://www.19196.com/
33|乐玩|乐玩游戏|http://www.lewanduo.com
34|猎宝游戏|猎宝网(7881)|http://www.7881.com
35|乐视|乐视应用开放平台|http://open.letv.com
36|免商店|蜗牛开发者中心|http://dev.snail.com/
37|趣游|趣游（趣联）|http://www.funity.com/
38|楚游|武汉楚游（07073）|http://www.07073.com/
39|PPS|PPS|http://g.pps.tv/
40|PPTV|PPTV|http://g.pptv.com/

iOS：

序号 | 名称 | 开放平台 | 地址
-----|------|----------|------
1|AppStore| 苹果开发者中心 |https://developer.apple.com/
2|iTools|iTools开发者平台|http://open.itools.cn/
3|PP助手|PP助手移动开放平台|http://dev.pp.cn/
4|同步推| 同步开发者中心|http://dev.tongbu.com/
5|i4（爱思助手）|爱思助手开发者平台|https://dev.i4.cn/
6|XX助手(和Android同样的处理类)|叉叉助手开发者平台|http://dev.xxzhushou.cn/
7|XY助手|XY苹果助手|http://dev.xyzs.com/dev/Help/detail/17308.html
8|快用(7659)|7659开放平台|http://open.7659.com/
9|海马玩|海马玩游戏中心|http://pay.haima.me/index.php
10|乐8|乐8游戏中心|http://www.le890.com/


特征问题
---------

**使用U8SDK的同学，请放心，U8SDK不存在被第三方渠道SDK拒绝的情况。**

因为U8SDK是一整套框架，而不是第三方渠道SDK接入产品。

我们提供给用户的都是源码和工具(也包含工具源码)，所以，和用户自己研发，没有任何差别。

U8Server部署在游戏公司自己的服务器，由游戏公司自己维护。

U8SDK客户端接入和打包部分，游戏公司也都是直接拥有源码，各个游戏公司拿去就是各自公司自己的统一SDK接入解决方案了，你可能叫他ASDK,BSDK...等等。

正是因为这个原因，像UC等渠道，拒绝AnySDK，棱镜SDK等第三方接入平台；但是不会拒绝U8SDK，因为，你用的不是U8SDK,是你们公司自己的统一SDK接入框架。

目前已经有多家使用U8SDK的游戏，在UC渠道上线了，你可以戳 [这里](http://www.9game.cn/mhsg/)、[这里](http://www.9game.cn/xsanguo/)、[这里](http://www.9game.cn/fatemdzz/)

至少到目前为止，我们还没有听说，哪家游戏因为使用U8SDK，而被渠道拒绝的案例。

