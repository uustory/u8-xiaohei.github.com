支持的渠道列表
===========

Note:Android和iOS平台支持的渠道，是指当前我们已经接好的渠道，并不是说打包框架只能接这些渠道。后续，我们会继续支持更多的渠道，在 [增值服务](http://www.u8sdk.com/plus) 页面，选择了版本之后，可以在这里渠道列表中，任意选择指定数量的渠道。


购买企业版的客户，可以选择我们暂时还不支持的渠道(仅限国内渠道)，我们会优先帮客户接入这些渠道：

企业初级版：最多可以选择2家我们还不支持的渠道
企业高级版：最多可以选择5家我们还不支持的渠道

其他方案的U8SDK客户，您也可以选择将渠道SDK外包给我们来接入(单价:1000元/个，包括客户端和服务器端部分，目前仅限国内Android渠道)


支付回调完整地址格式：{u8server根地址}{支付回调相对地址}/{渠道号}. 具体可以参考[支付回调文档](u8server_pay.md). 各个渠道对应的支付回调相对地址参考下方表格中的[支付回调相对地址]


Android平台
-------

| 序号  | 渠道名称    | 渠道工程  | 配置目录  |支付回调相对地址
|-------|:-----------|:---------|:---------|:-------|
| 1     | 和游戏(咪咕) | U8SDK_AndGame | andgame |单机暂无 |
| 2     | 安峰 | U8SDK_AnFeng | anfeng |/pay/anfeng/payCallback|
| 3     | 安智| U8SDK_AnZhi | anzhi |/pay/anzhi/payCallback|
| 4     | 应用汇 | U8SDK_AppChina  | appchina |/pay/appchina/payCallback|
| 5     | 百度 | U8SDK_Baidu  | baidu |/pay/baidu/payCallback|
| 6     | 叉叉助手 | U8SDK_ChaChaZhuShou  | chachazhushou |/pay/guopan/payCallback|
| 7     | 虫虫游戏 | U8SDK_ChongChong  | chongchong |/pay/chongchong/payCallback|
| 8     | 武汉楚游(07073) | U8SDK_ChuYou  | chuyou |/pay/chuyou/payCallback|
| 9     | 云点联动(友游) | U8SDK_CloudPoint  | cloudpoint |/pay/cloudpoint/payCallback|
| 10    | 移动MM | U8SDK_CM_PAY_IAP  | cm_pay_iap |单机暂无|
| 11    | 当乐 | U8SDK_Downjoy  | downjoy |/pay/downjoy/payCallback|
| 12    | 电信(爱游戏) | U8SDK_EGame  | egame |/pay/egame/payCallback|
| 13    | 机锋 | U8SDK_GFan  | gfan |/pay/gfan/payCallback|
| 14    | 华为 | U8SDK_Huawei  | huawei |/pay/huawei/payCallback|
| 15    | 金立 | U8SDK_JinLi  | jinli |/pay/jinli/payCallback|
| 16    | 靠谱助手 | U8SDK_KaoPu  | kaopu |/pay/kaopu/payCallback|
| 17    | 快用 | U8SDK_KuaiYong  | kuaiyong |/pay/kuaiyong/payCallback|
| 18    | 酷狗 | U8SDK_KuGou  | kugou |/pay/kugou/payCallback|
| 19    | 酷派 | U8SDK_KuPai  | kupai |/pay/kupai/payCallback|
| 20    | 乐嗨嗨 | U8SDK_LeHaiHai  | lehaihai |/pay/lehaihai/payCallback|
| 21    | 联想 | U8SDK_Lenovo  | lenovo |/pay/lenovo/payCallback|
| 22    | 乐视 | U8SDK_Letv  | letv |/pay/letv/payCallback|
| 23    | 乐玩 | U8SDK_LeWan  | lewan |/pay/lewan/payCallback|
| 24    | 乐游 | U8SDK_LeYou  | leyou |/pay/leyou/payCallback|
| 25    | 猎宝 | U8SDK_LieBao  | liebao |/pay/liebao/payCallback|
| 26    | 4399 | U8SDK_M4399  | m4399 |/pay/m4399/payCallback|
| 27    | 魅族 | U8SDK_MeiZu  | meizu |/pay/meizu/payCallback|
| 28    | 免商店(蜗牛) | U8SDK_MianShangDian  | mianshangdian |/pay/mianshangdian/payCallback|
| 29    | 木蚂蚁 | U8SDK_MuMaYi  | mumayi |/pay/mumayi/payCallback|
| 30    | 拇指玩 | U8SDK_MuZhiWan  | muzhiwan |/pay/muzhiwan/payCallback|
| 31    | OPPO | U8SDK_Oppo  | oppo |/pay/oppo/payCallback|
| 32    | 偶玩 | U8SDK_OuWan  | ouwan |/pay/ouwan/payCallback|
| 33    | 泡椒网 | U8SDK_PaoJiao  | paojiao |/pay/paojiao/payCallback|
| 34    | 啪啪游戏厅 | U8SDK_PaPa  | papa |/pay/papa/payCallback|
| 35    | 朋友玩 | U8SDK_PengYouWan  | pengyouwan |/pay/pengyouwan/payCallback|
| 36    | PPS(爱奇异) | U8SDK_PPS  | pps |/pay/iqiyi/payCallback|
| 37    | PPTV | U8SDK_PPTV  | pptv |/pay/pptv/payCallback|
| 38    | 360 | U8SDK_Qihoo360  | qihoo360 |/pay/qihoo360/payCallback|
| 39    | 全民助手 | U8SDK_QuanMingZhuShou  | quanmingzhushou |/pay/quanminzhushou/payCallback|
| 40    | 趣游 | U8SDK_QuYou  | quyou |/pay/quyou/payCallback|
| 41    | 手游村 | U8SDK_ShouYouCun  | shouyoucun |/pay/shouyoucun/payCallback|
| 42    | 搜狗 | U8SDK_SouGou  | sougou |/pay/sougou/payCallback|
| 43    | TT语音 | U8SDK_TT  | tt |/pay/tt/payCallback|
| 44    | UC(阿里) | U8SDK_Aligames  | aligames |/pay/uc/payCallback|
| 45    | 联通(沃支付) | U8SDK_UniPay  | unipay |/pay/unipay/payCallback|
| 46    | VIVO | U8SDK_Vivo  | vivo |/pay/vivo/payCallback|
| 47    | 豌豆荚 | U8SDK_WanDouJia  | wandoujia |/pay/wandoujia/payCallback|
| 48    | 小米 | U8SDK_XiaoMi  | xiaomi |/pay/xiaomi/payCallback|
| 49    | 小七手游 | U8SDK_XiaoQi  | xiaoqi |/pay/xiaoqi/payCallback|
| 50    | 喜马拉雅 | U8SDK_XiMaLaYa  | ximalaya |/pay/ximalaya/payCallback|
| 51    | 熊猫玩(游戏群) | U8SDK_XMWan  | xmwan |/pay/xmwan/payCallback|
| 52    | 迅雷 | U8SDK_XunLei  | xunlei |/pay/xunlei/payCallback|
| 53    | 宴门手游 | U8SDK_YanMen  | yanmen |/pay/yanmen/payCallback|
| 54    | 夜神模拟器 | U8SDK_YeShenUnion  | yeshenunion |/pay/yeshen/payCallback|
| 55    | 手游村 | U8SDK_ShouYouCun  | shouyoucun |/pay/shouyoucun/payCallback|
| 56    | 优酷 | U8SDK_YouKu  | youku |/pay/youku/payCallBack|
| 57    | 腾讯应用宝 | U8SDK_YSDK  | ysdk |/pay/ysdk/payCallback(道具直购模式才有)|
| 58    | 丫丫玩 | U8SDK_YYWan  | yywan |/pay/yywan/payCallback|
| 59    | 第一弹 | U8SDK_DiYiDan  | diyidan |/pay/diyidan/payCallback|
| 60    | 益玩 | U8SDK_EWan  | ewan |/pay/ewan/payCallback|
| 61    | 小皮手游 | U8SDK_XiaoPi  | xiaopi |/pay/xiaopi/payCallback|
| 62    | 星游(StarGame) | U8SDK_StarGame  | stargame |/pay/stargame/payCallback|
| 63    | 49游 | U8SDK_SiJiuYou  | 49you |/pay/sijiuyou/payCallback|
| 64    | N多市场 | U8SDK_NDuo  | nduo |/pay/nduo/payCallback|
| 65    | 葫芦侠 | U8SDK_HuLuXia  | huluxia |/pay/huluxia/payCallback|
| 66    | 逍遥模拟器 | U8SDK_XiaoYao  | xiaoyao |/pay/xiaoyao/payCallback|
| 67    | 七果游戏(七匣子) | U8SDK_QiGuo  | qiguo |/pay/qiquo/payCallback|
| 68    | 蘑菇玩 | U8SDK_MoGuWan  | moguwan |/pay/moguwan/payCallback|
| 69    | 185手游 | U8SDK_SY185  | sy185 |/pay/sy185/payCallback|
| 70    | 07073手游 | U8SDK_SY07073  | sy07073 |/pay/sy07073/payCallback|
| 71    | 海马玩 | U8SDK_HaiMa  | haima |/pay/haima/payCallback|
| 72    | 说玩 | U8SDK_ShuoWan  | shuowan |/pay/shuowan/payCallback|
| 73    | 三星 | U8SDK_Samsung  | samsung |/pay/samsung/payCallback|
| 74    | 天天游戏中心 | U8SDK_TianTian  | tiantian |/pay/tiantian/payCallback|
| 75    | play800 | U8SDK_Play800  | play800 |/pay/play800android/payCallback|
| 76    | 拇指游玩 | U8SDK_MuZhiYouWan  | muzhiyouwan |/pay/muzhiyouwan/payCallback|
| 77    | 9377 | U8SDK_M9377  | m9377 |/pay/m9377/payCallback|
| 78    | 新浪微博 | U8SDK_Sina  | sina |/pay/sina/payCallback|
| 79    | 8868 | U8SDK_M8868  | m8868 |/pay/m8868/payCallback|
| 80    | 掌阅 | U8SDK_ZhangYue  | zhangyue |/pay/zhangyue/payCallback|
| 81    | 游戏Fan | U8SDK_YouXiFan  | youxifan |/pay/youxifan/payCallback|
| 82    | iTools | U8SDK_iTools  | itools |/pay/itools/payCallback|



iOS平台
-------

| 序号  | 渠道名称    | 渠道工程  | 配置目录  |支付回调相对地址|
|-------|:-----------|:---------|:---------|:--------|
| 1     | 91助手 | U8SDK_91 | 91 |/pay/baidu91/payCallback|
| 2     | 07073 | U8SDK_07073 | 07073 |/pay/chuyou/payCallback|
| 3     | Appstore | U8SDK_AppStore | AppStore |AppStore无回调地址|
| 4     | 爱思助手 | U8SDK_as | as |/pay/i4/payCallback|
| 5     | 当乐 | U8SDK_downjoy | downjoy |/pay/downjoy/payCallback|
| 6     | 海马玩 | U8SDK_haima | haima |/pay/haima/payCallback|
| 8     | i苹果助手 | U8SDK_IIApple | IIApple |暂无
| 9     | iTools助手 | U8SDK_iTools | iTools |/pay/itools/payCallback|
| 10    | 快用助手 | U8SDK_KuaiYong | KuaiYong |/pay/kuaiyong/payCallback|
| 11    | 乐8 | U8SDK_le8 | le8 |/pay/le8/payCallback|
| 12    | 乐嗨嗨 | U8SDK_lehihi | lehihi |/pay/lehaihai/payCallback|
| 13    | 泡椒网 | U8SDK_paojiao | paojiao |/pay/paojiao/payCallback|
| 14    | PP助手 | U8SDK_PP | PP |/pay/pp/payCallback|
| 15    | 小七手游 | U8SDK_SMSDK | SMSDK |/pay/xiaoqi/payCallback|
| 16    | 同步推 | U8SDK_tongbu | tongbu |/pay/tongbu/payCallback|
| 17    | 叉叉助手 | U8SDK_xx | xx |/pay/guopan/payCallback|
| 18    | XY助手 | U8SDK_xy | xy |/pay/xy/payCallback|
| 19    | 友游(云点联动) | U8SDK_youyou | youyou |/pay/cloudpoint/payCallback|
| 20    | 拇指游玩 | U8SDK_MZYW | mzyw |/pay/muzhiyouwan/payCallback|
| 21    | Play800 | U8SDK_Play800 | play800 |/pay/play800/payCallback|
| 22    | 手盟 | U8SDK_SM910 | sm910 |/pay/shoumeng/payCallback|
| 23    | YY | U8SDK_YY | yy |/pay/yyunion/payCallback|
