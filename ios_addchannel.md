接入新SDK
===========

NOTE:在U8SDK框架基础上接入一个新渠道SDK，首先，心里请明确一点。接入过程和游戏工程没有半点联系，也就是说，游戏开发和SDK接入是完全解耦的。所以，接入的时候，不要想着游戏中怎样怎样，严格按照下面的步骤进行接入即可。
所有的接入工程都放在一个统一的目录，默认是放在打包工具根目录/Plugins目录下。所有接入工程的命名格式必须固定，统一为U8SDK_***


创建新的SDK插件
-------

**更新: 为了方便接入新的SDK, 我们提供了插件创建脚本, 以前手动创建接入工程的方式不再推荐 **

Plugins目录下的create_plugin.py就是我们提供的接入工程创建工具, 它以SDK接入工程U8SDK_Template做为模板生成新的接入工程

创建一个工程:U8SDK_AAA
```
cd Plugins
python create_plugin.py U8SDK_AAA
```

实现渠道SDK必须接入的方法
-------

NOTE:所有渠道SDK需要接入的方法，都大同小异。我们将其归为三大类：
1、初始化：	 大部分渠道都有初始化方法
2、用户相关：包含[登录]、[切换帐号]、[登出]、[个人中心]、[提交玩家数据]、[退出确认框]
3、支付：仅仅包含一个充值的接口

**1、引入SDK库和资源**

一般渠道SDK，都会提供一些库文件和资源文件，接入的时候，需要将这些文件拷贝到该工程下面SDK目录下，同时，添加SDK目录中的framework和头文件到接入工程,
通常可以直接把整个SDK目录加到接入工程中, 但是要注意:  
**SDK目录下的静态库不能加入接入工程, 否则会导致最终编译渠道工程时连接失败**


**2、实现需要的方法**

我们将原来的U8SDK_iTools.h和U8SDK_iTools.m 重命名为 U8SDK_AAA.h和U8SDK_AAA.m，类文件中的对应的名称，也需要修改。

然后，选中U8SDK_AAA的target，在Build Phases 中，Compile Sources下面，如果还是U8SDK_iTools.m，那么删除这个，添加上U8SDK_AAA.m

U8SDK_AAA.h文件如下：

```
#import <Foundation/Foundation.h>
#import "U8Plugin.h"

#import "HXAppPlatformKitPro.h"

@interface U8SDK_AAA : U8Plugin<U8User, U8Pay>

@end

1、继承U8Plugin接口，
2、同时，实现U8User协议和U8Pay协议。
3、在U8SDK_AAA.m文件中实现U8PluginProtocol中的接口，进行渠道SDK的初始化，和AppDelegate中的一些系统方法接口。
3、在U8SDK_AAA.m文件中实现U8User和U8Pay中的接口。

这三个需要实现的Protocol如下：

// U8Plugin 插件接口
@protocol U8PluginProtocol

-(instancetype) initWithParams:(NSDictionary*)params;

@optional

-(void) setupWithParams:(NSDictionary*)params;

// UIApplicationDelegate事件
- (void)didFinishLaunchingWithOptions:(NSDictionary *)launchOptions;
- (void)applicationWillResignActive:(UIApplication *)application;
- (void)applicationDidEnterBackground:(UIApplication *)application;
- (void)applicationWillEnterForeground:(UIApplication *)application;
- (void)applicationDidBecomeActive:(UIApplication *)application;
- (void)applicationWillTerminate:(UIApplication *)application;

- (void)didRegisterForRemoteNotificationsWithDeviceToken:(NSData*)deviceToken;
- (void)didFailToRegisterForRemoteNotificationsWithError:(NSError*)error;
- (void)didReceiveLocalNotification:(UILocalNotification*)notification;
- (void)didReceiveRemoteNotification:(NSDictionary*)userInfo;

- (void)openURL:(NSURL*)url sourceApplication:(NSString*)sourceApplication annotation:(id)annotation;

@end

//U8User 账号登录相关接口
@protocol U8User

- (void) login;				//SDK登录
- (void) logout;
- (void) switchAccount;

- (BOOL) hasAccountCenter;
@optional
- (void) showAccountCenter;

//- (BOOL) isLogined;
//- (NSString*) getSessionID;

@end

//U8Pay 应用内购接口
@protocol U8Pay

-(void) pay:(NSDictionary*) profuctInfo;

@optional
-(void) openIAP;

@optional
-(void) closeIAP;

@optional
-(void) finishTransactionId:(NSString*)transactionId;

@end

```

然后，我们在U8SDK_AAA.m中根据需要实现上面三个协议中的对应的接口，我们这里的内容还是iTools的实现，我们看一下：

```

#import "U8SDK_AAA.h"
#import <AlipaySDK/AlipaySDK.h>

typedef enum
{
    StateDefault,
    StateIniting,
    StateInited,
    StateLogin,
    StateLogined
} SDKState;

@implementation U8SDK_AAA
{
    SDKState sdkState;
}

//////////////////////////////////////////////////////////////////////
#pragma mark - 处理 SDK 通知
//注册通知处理
- (void)registerNotification:(NSNotification *)notification
{
    NSLog(@"userId: %@", [notification.object objectForKey:@"userId"]);
    NSLog(@"userName: %@", [notification.object objectForKey:@"userName"]);
    NSLog(@"sessionId: %@", [notification.object objectForKey:@"sessionId"]);
}

//登录通知处理
- (void)loginNotification:(NSNotification *)notification
{
    NSLog(@"userId: %@", [notification.object objectForKey:@"userId"]);
    NSLog(@"userName: %@", [notification.object objectForKey:@"userName"]);
    NSLog(@"sessionId: %@", [notification.object objectForKey:@"sessionId"]);
    
    sdkState = StateLogined;
    
    NSDictionary* extension = [NSDictionary dictionaryWithObjectsAndKeys:
                                  [notification.object objectForKey:@"userName"], @"username",
                                  [notification.object objectForKey:@"userId"], @"uid",
                                  [notification.object objectForKey:@"sessionId"], @"sessionid",
                                  nil];
    
    [self eventUserLoginExt:extension];
}

//关闭窗口通知处理
- (void)closeViewNotification:(NSNotification *)notification
{
    NSLog(@"SDK View Closed");
}

//注销通知处理
- (void)logoutNotification:(NSNotification *)notification
{
    NSLog(@"Logout");
    [self eventUserLogout:nil];
}

- (void)closePayViewNotification:(NSNotification *)notification
{
    NSLog(@"SDK PayView Closed");
    NSLog(@"userId: %@", [notification.object objectForKey:@"userId"]);
    NSLog(@"userName: %@", [notification.object objectForKey:@"userName"]);
    NSLog(@"sessionId: %@", [notification.object objectForKey:@"sessionId"]);
    NSLog(@"orderIdCom: %@", [notification.object objectForKey:@"orderIdCom"]);
}

//监听服务器有可更新App的通知处理
- (void)updateApp:(NSNotification *)notification
{
    NSLog(@"server has app update");
    NSLog(@"userId: %@", [notification.object objectForKey:@"userId"]);
    NSLog(@"userName: %@", [notification.object objectForKey:@"userName"]);
    NSLog(@"sessionId: %@", [notification.object objectForKey:@"sessionId"]);
}

//监听钱包支付不成功的通知处理
- (void)payFailed:(NSNotification *)notification
{
    NSLog(@"wallet pay failed");
    NSLog(@"orderIdCom: %@", [notification.object objectForKey:@"orderIdCom"]);
    NSLog(@"userId: %@", [notification.object objectForKey:@"userId"]);
    NSLog(@"userName: %@", [notification.object objectForKey:@"userName"]);
    NSLog(@"sessionId: %@", [notification.object objectForKey:@"sessionId"]);
}

//////////////////////////////////////////////////////////////////////

//这里进行SDK的初始化
-(instancetype)initWithParams:(NSDictionary *)params
{
    [U8SDK sharedInstance].defaultUser = self;			//这个是必须的
    [U8SDK sharedInstance].defaultPay  = self;			//这个是必须的
    
    sdkState = StateDefault;
    
    NSString* appid = [params objectForKey:@"appid"];	//这里的key和渠道SDK的配置文件中定义的key需要一致
    NSString* appkey = [params objectForKey:@"appkey"];
    
    //设置充值平台分配的appid和appkey
    [HXAppPlatformKitPro setAppId:[appid intValue] appKey:appkey];
    
    return self;
}

-(void) didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
{
    //设置支付宝回调所需的URL Scheme
    [HXAppPlatformKitPro setAlipayURLScheme:[[[NSBundle mainBundle] infoDictionary] objectForKey:(NSString *)kCFBundleIdentifierKey]];
    //设置支持的旋转方向
    [HXAppPlatformKitPro setSupportOrientationPortrait:NO portraitUpsideDown:NO landscapeLeft:YES landscapeRight:YES];
    
    //监听注册通知
    [[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(registerNotification:) name:HX_NOTIFICATION_REGISTER object:nil];

    //监听登录通知
    [[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(loginNotification:) name:HX_NOTIFICATION_LOGIN object:nil];
    
    //视图关闭通知
    [[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(closeViewNotification:) name:HX_NOTIFICATION_CLOSEVIEW object:nil];
    
    //注销通知
    [[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(logoutNotification:) name:HX_NOTIFICATION_LOGOUT object:nil];
    
    //处理支付视图时关闭的附加通知
    [[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(closePayViewNotification:) name:HX_NOTIFICATION_CLOSE_PAYVIEW object:nil];
    
    //监听服务器有可更新App的通知
    [[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(updateApp:) name:HX_NOTIFICATION_UPDATE_APP object:nil];
    
    //监听钱包支付不成功的通知
    [[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(payFailed:) name:HX_NOTIFICATION_WALLET_PAY_FAILED object:nil];
}

-(void) openURL:(NSURL *)url sourceApplication:(NSString *)sourceApplication annotation:(id)annotation
{
    //跳转支付宝钱包进行支付，需要将支付宝钱包的支付结果回传给SDK
    if ([url.host isEqualToString:@"safepay"]) {
        [[AlipaySDK defaultService]
         processOrderWithPaymentResult:url
         standbyCallback:^(NSDictionary *resultDic) {
             NSLog(@"result = %@", resultDic);
             [HXAppPlatformKitPro alipayCallBack:resultDic];
         }];
    }
}

//实现SDK的登录
-(void) login
{
    [HXAppPlatformKitPro logout];
    [HXAppPlatformKitPro showLoginView];
}

//实现SDK的登出
-(void) logout
{
    [HXAppPlatformKitPro logout];
}

//实现SDK的切换账号
-(void) switchAccount
{
    [HXAppPlatformKitPro logout];
    [HXAppPlatformKitPro showLoginView];
}

//SDK是否要求游戏中提供［个人中心］按钮，点击可以进入SDK的个人中心界面
-(BOOL) hasAccountCenter
{
    return YES;
}

//显示SDK的个人中心界面
-(void) showAccountCenter
{
    [HXAppPlatformKitPro showPlatformView];
}

//实现渠道SDK的支付方法，打开渠道SDK的支付界面
-(void) pay:(NSDictionary*)productInfo
{
    int price = [[productInfo valueForKey:@"price"] intValue];
    NSString* productName = [productInfo valueForKey:@"productName"];
    NSString* orderId = [productInfo valueForKey:@"orderID"];

    //NSString* extension = [productInfo valueForKey:@"extension"];
    //NSString* serverName = [productInfo valueForKey:@"serverName"];
    //int buyNum = [[productInfo valueForKey:@"buyNum"] intValue];
    
    [HXAppPlatformKitPro payProductWithProductName:productName Amount:price OrderIdCom:orderId];
}

@end



```

触发U8SDK事件
-------

NOTE:接入工程中，在初始化成功;登录成功;登出成功;支付成功;等时刻，都需要往抽象层抛出对应的事件，以便抽象层中能够分发这些事件，这样游戏接入工程监听这些事件，就能做对应的逻辑处理。

对应的事件触发方法在U8Plugin中，因为上面U8SDK_AAA直接继承了U8Plugin，所以在U8SDK_AAA.m中，可以直接调用。我们看看这些事件：

```
#import "U8SDK.h"

@interface U8Plugin : NSObject<U8PluginProtocol>

-(UIView*) view;										//获取当前的UIView
-(UIViewController*) viewController;					//获取当前的UIViewController


-(void) eventPlatformInit:(NSDictionary*) params;		//初始化成功的时候，调用
-(void) eventUserLogin:(NSDictionary*) params;			//直接使用eventUserLoginExt即可
-(void) eventUserLoginExt:(id) extension;				//登录成功的时候，调用
-(void) eventUserLogout:(NSDictionary*) params;			//登出成功的时候，调用
-(void) eventPayPaid:(NSDictionary*) params;			//支付成功的时候，调用

比如上面在SDK通知的登录成功的回调方法loginNotification中，我们触发了eventUserLoginExt事件，同时将登录验证需要的参数，放在了一个Dictionary中，这些参数在去U8Server做二次登录认证的时候，会转换成json放到extension字段中。

所以，这里的名称是什么，U8Server那边也按照对应的key，按照json格式进行解析即可。

@end

```

配置编译参数
-------

NOTE: 我们在每个渠道SDK的根目录下，都有一个xcode_process.py文件，用来配置当前渠道SDK的编译参数信息，同时也定义了一个自定义脚本的处理接口，用来实现特殊操作。

```
文件内容如下：

# -*- coding: utf-8 -*-

mods = {
    "group": "U8SDK_AAA",
    "files": [
        "libU8SDK_AAA.a",
    ],
    "folders": [
        "SDK"
    ],
    "frameworks": [
        "MobileCoreServices.framework",
        "AdSupport.framework",
        "UIKit.framework"
    ],
    "libs": [
        "libsqlite3.dylib"
    ]
}

def post_process(self, project, infoPlist, sdkparams):

    self.addBundleURLType(CFBundleTypeRole="Editor", CFBundleURLSchemes=[self.getBundleId()])
    
    infoPlist['UIViewControllerBasedStatusBarAppearance'] = False

1、mods：配置添加到xcode工程中的库和文件等。
	group：添加到工程中的group
	files：添加到工程中的文件
	folders：添加到工程中的文件夹，SDK下面是当前渠道SDK提供的各种库文件和资源文件
	frameworks：添加到工程中的系统的frameworks，注意：这里添加的都是当前渠道SDK需要依赖的系统的framework
	libs：添加到工程中的系统的lib文件，注意：这里添加的都是当前渠道SDk需要依赖的系统的lib

2、post_process：当前渠道SDK的自定义脚本
	self:当前类空间，可以用self调用XcodeProcess类中所有方法。这个类在buildscript/xcode_gen.py文件中
	project:当前工程对象
	infoPlist:info.plist对象，是一个字典结构
	sdkparams:当前渠道SDK的配置参数，是一个字典结构

```

到这里，我们上面接入成功之后，我们可以运行之前我们重命名之后的名为U8SDK_AAADeploy的Scheme，编译成功之后，会在根目录下生成一个名为libU8SDK_AAA.a的静态库文件。

