
怎样指定打包证书
=========

打包证书可以在config.json中指定, 有三种方式

1.指定开发者TeamID
---------

采用这种方式, xcode会自动匹配打包证书, 配置关键字:DevelopmentTeam 
示例:
``` json
{
    "desc":"07073（楚游/数游）",
    "CFBundleIdentifier": "com.u8sdk.sample.cy",
    "DevelopmentTeam": "A1B2C3D4",
    "U8SDK": {
        "Channel": 48
    },
    "plugins": [
        {
            "name": "07073",
            "pid": "226",
            "gameid": "212"
        }
    ]
}
```
(注意: 这种方式需要xcode添加好开发者账号, 如果本地没有匹配的证书的话打包工具会报错, 这时需要用xcode打开工程确认一下是否有匹配的证书)
2. 指定证书(provision profile)名称
---------
配置关键字:provision, 证书的名称可以通过苹果开发者后台或者xcode查看
示例:
``` json
{
    "desc":"07073（楚游/数游）",
    "CFBundleIdentifier": "com.u8sdk.sample.cy",
    "provision": "XXXDev",
    "U8SDK": {
        "Channel": 48
    },
    "plugins": [
        {
            "name": "07073",
            "pid": "226",
            "gameid": "212"
        }
    ]
}
```
3. 指定证书文件名
---------
配置关键字还是provision, 文件名的扩展名必须是.mobileprovision, 文件要与config.json放在同一目录
这种方式适于给第三方合作者打包, 合作者提供打包证书p12和mobileprovision文件
示例:
``` json
{
    "desc":"07073（楚游/数游）",
    "CFBundleIdentifier": "com.u8sdk.sample.cy",
    "provision": "dev.mobileprovision",
    "U8SDK": {
        "Channel": 48
    },
    "plugins": [
        {
            "name": "07073",
            "pid": "226",
            "gameid": "212"
        }
    ]
}
```
采用指定provision的方式打包都需要保证: 系统中已经导入开发者证书(就是包含公开证书和密钥的p12文件),
provision还可以指定为多个证书
示例:

``` json
{
    "desc":"07073（楚游/数游）",
    "CFBundleIdentifier": "com.u8sdk.sample.cy",
    "provision": { "develop": "dev.mobileprovision", "distribute": "dis.mobileprovision"},
    "U8SDK": {
        "Channel": 48
    },
    "plugins": [
        {
            "name": "07073",
            "pid": "226",
            "gameid": "212"
        }
    ]
}
```
