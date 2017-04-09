IOS游戏接入指南
======

NOTE:这篇文档，基于最新版本的U8SDK框架，如果发现该文档和你使用的版本不匹配，则以你使用的版本对应的文档为准。

NOTE:2016年12月12号以前的版本，请看这里：[抽象层API调用](quickstart.md)

添加framework和bundle
-------

把U8SDK.framework加入xcode工程

必须调用的接口
-------

#### 初始化(必接)

初始化应该在程序启动时调用, 也就是在didFinishLaunchingWithOptions方法里

```
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {

    NSDictionary *sdkconfig = [[NSBundle mainBundle] objectForInfoDictionaryKey:@"U8SDK"];
    [[U8SDK sharedInstance] initWithParams:sdkconfig];
    
    [[U8SDK sharedInstance] application:application didFinishLaunchingWithOptions:launchOptions];
    
    [[U8Analytics sharedInstance] startLevel:@"1"];
    
    return YES;
}
```

#### AppDelegate事件处理

需要在AppDelegate以下对应的生命周期方法中调用U8SDK种对应这些方法：

```
-(void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
{
    [[U8SDK sharedInstance] application:application didRegisterForRemoteNotificationsWithDeviceToken:deviceToken];
}


-(void)application:(UIApplication *)application didReceiveRemoteNotification:(nonnull NSDictionary *)userInfo
{
    [[U8SDK sharedInstance] application:application didReceiveRemoteNotification:userInfo];
}

-(void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult))completionHandler
{
    [[U8SDK sharedInstance] application:application didReceiveRemoteNotification:userInfo];
}

-(void)application:(UIApplication *)application didReceiveLocalNotification:(UILocalNotification *)notification
{
    [[U8SDK sharedInstance] application:application didReceiveLocalNotification:notification];
}

- (void)applicationWillResignActive:(UIApplication *)application {
    [[U8SDK sharedInstance] applicationWillResignActive:application];
}

- (void)applicationDidEnterBackground:(UIApplication *)application {
    [[U8SDK sharedInstance] applicationDidEnterBackground:application];
}

- (void)applicationWillEnterForeground:(UIApplication *)application {
    [[U8SDK sharedInstance] applicationWillEnterForeground:application];
}

- (void)applicationDidBecomeActive:(UIApplication *)application {
    [[U8SDK sharedInstance] applicationDidBecomeActive:application];
}

- (void)applicationWillTerminate:(UIApplication *)application {
    [[U8SDK sharedInstance] applicationWillTerminate:application];
}

-(BOOL)application:(UIApplication *)application handleOpenURL:(NSURL *)url
{
    return [[U8SDK sharedInstance] application:application handleOpenURL:url];
}

-(BOOL)application:(UIApplication *)application openURL:(NSURL *)url sourceApplication:(NSString *)sourceApplication annotation:(id)annotation
{
    return [[U8SDK sharedInstance] application:application openURL:url sourceApplication:sourceApplication annotation:annotation];
}
```

#### 设置U8SDK回调代理

在初始化成功后, 还需要设置U8SDK的回调函数, 回调对象需要实现U8SDKDelegate的各个接口

```
    [[U8SDK sharedInstance] setDelegate:self];
```

渠道SDK中初始化成功，登录成功，登出成功，支付成功等，会回调到该接口。比如u8sdkdemo中DemoViewController.m中这些接口的实现。

```
-(UIView *)GetView
{
    return [self GetViewController].view;
}

-(UIViewController *)GetViewController
{
    return self;
}

//初始化成功会掉。 渠道SDK初始化成功，会回调该参数
//无参数
-(void) OnPlatformInit:(NSDictionary*)param
{
    [self SendCallback:"OnInitSuc" withParams:param];
}

//渠道SDK登录并去u8server登录认证成功，会回调改方法。
//param(NSDictionary类型)中的参数如下：
//userID:U8Server生成的唯一用户ID，游戏服务器需要将游戏账户ID和该userID进行绑定。
//sdkUserID:渠道SDK平台用户唯一ID，一般不需要使用
//username:U8Server生成的用户名，比如234234234.uc,4353453453.baidu,65756756756.360
//sdkUserName:渠道SDK平台用户名，可能为空，部分渠道SDK没有返回用户名
//token:U8Server生成的会话ID，游戏服务器拿该字段去U8Server做二次登录验证
//extension:U8Server返回的扩展字段，部分渠道SDK需要，游戏中无需使用该字段的值
//timestamp:U8Server生成的时间戳，游戏服务器去U8Server做二次登录验证时，传给U8Server
-(void) OnUserLogin:(NSDictionary*)param
{
    [self SendCallback:"OnLoginSuc" withParams:param];
}

//渠道SDK登出回调， 游戏收到该回调，需要返回到游戏登录界面。
//无参数
-(void) OnUserLogout:(NSDictionary*)param
{
    [self SendCallback:"OnLogout" withParams:param];
}

//客户端调用渠道SDK支付成功，一般不需要做逻辑处理，最终支付成功，以U8Server回调通知到游戏服务器为准。
//无参数
-(void) OnPayPaid:(NSDictionary*)param
{
    [self SendCallback:"OnPaySuc" withParams:param];
}

//自定义回调，一般无需处理
-(void)OnEventCustom:(NSDictionary*)param
{
    NSString* name = [param valueForKey:@"name"];
    NSDictionary* params = [param valueForKey:@"params"];
    
    [self SendCallback:[name UTF8String] withParams:params];
}
```

#### 二阶初始化接口

这个接口是在程序首个界面加载完成之后调用的，设置参数使用。
```
    [[U8SDK sharedInstance] performSelector:@selector(setupWithParams:) withObject:nil afterDelay:0];
```

#### 登录接口(必接)

```
    [[U8SDK sharedInstance] login];
```

#### 登出接口(选接)

```
    [[U8SDK sharedInstance] logout];
```

#### 提交扩展数据(必接)

```
    U8UserExtraData* extraData = [[U8UserExtraData alloc] init];
    extraData.dataType = TYPE_ENTER_GAME;
    extraData.roleID = @"testRole";
    extraData.roleName = @"角色名称";
    extraData.serverID = 1;
    extraData.serverName = @"第一区";
    extraData.roleLevel = @"1";
    extraData.moneyNum = 100;
    extraData.roleCreateTime = time(NULL);
    extraData.roleLevelUpTime = time(NULL);
    
    [[U8SDK sharedInstance] submitExtraData:extraData];
```
该方法将调用的时机分为几种类型：

1：选择服务器
2：创建角色
3：进入游戏
4：等级提升
5：退出游戏

所以在上面5个地方，都需要调用
U8User.getInstance().submitExtraData(UserExtraData extraData)

其中，UserExtraData就是游戏内玩家的数据，比如在选择服务器时，extraData中的dataType为1；创建角色的时候，dataType为2；进入游戏时，dataType为3；等级提升时，dataType为4；退出游戏时，dataType为5

NOTE:选择服务器时，因为还没有进入游戏，无法知道角色数据，extraData中只需要传入服务器信息即可。

关于U8UserExtraData 数据结构:


| 参数名称        | 参数类型          | 参数说明  |
|:------------- |:-------------|:-----|
| dataType     | int | 调用时机|
| serverID| String| 玩家所在服务器的ID|
| serverName| String| 玩家所在服务器的名称|
| roleID | String | 玩家角色ID|
| roleName| String | 玩家角色名称|
| roleLevel| String | 玩家角色等级|
| moneyNum| String | 当前角色身上拥有的游戏币数量|
| roleCreateTime| long | 角色创建时间，从1970年到现在的时间，单位秒,必须传入真实的数据，否则UC审核不过|
| roleLevelUpTime| long | 角色等级变化时间，从1970年到现在的时间，单位秒|
| vip| String | 玩家VIP等级|


#### 支付充值(必接)

```
    U8ProductInfo* productInfo = [[U8ProductInfo alloc] init];
    productInfo.productName = @"礼包1";
    productInfo.productDesc = @"礼包1";
    productInfo.productId = @"libao1";
    productInfo.price = [NSNumber numberWithInt:30];
    productInfo.buyNum = 1;
    productInfo.coinNum = 900;
    productInfo.roleId = @"12345";
    productInfo.roleName = @"角色";
    productInfo.roleLevel = @"66";
    productInfo.serverId = @"1";
    productInfo.serverName = @"桃源";
    productInfo.vip = @"1";
    productInfo.notifyUrl = @"http://110.54.33.45/game/pay/notify";
    
    [[U8SDK sharedInstance] pay:productInfo];
```

关于U8ProductInfo对象：

| 参数名称        | 参数类型          | 参数说明  |
|:------------- |:-------------|:-----|
| productId     | String | 充值商品ID，游戏内的商品ID |
| productName      | String      |   商品名称，比如100元宝，500钻石...|
| productDesc| String      |    商品描述，比如 充值100元宝，赠送20元宝|
| price| int | 充值金额(单位：元)|
| buyNum| int | 购买数量，一般都是1|
| coinNum| int | 玩家当前身上剩余的游戏币 |
| serverId| String| 玩家所在服务器的ID|
| serverName| String| 玩家所在服务器的名称|
| roleID | String | 玩家角色ID|
| roleName| String | 玩家角色名称|
| roleLevel| String | 玩家角色等级|
| vip | String| 玩家vip等级 |
| notifyUrl| String | 游戏服务器支付回调地址，渠道SDK支付成功，异步通知U8Server，U8Server根据该地址，通知游戏服务器发货|
| extension | String | 支付成功之后，U8Server通知游戏服务器时，原样返回给游戏服务器|


上面代码写好之后, 这个xcode就是以后打渠道包的母工程。 接下来，就可以通过U8SDK一键打包工具，
打出各个渠道的渠道包。
* 打包配置方法参考[IOS打包配置](ios_package.md)
* 打包方法参考[iOS平台打包](package.md)
