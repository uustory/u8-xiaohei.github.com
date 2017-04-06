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

U8SDK会把这些事件跟第三方SDK进行桥接
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

#### 二阶初始化接口

这个接口是在程序首个界面加载完成之后调用的
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

#### 支付充值(必接)

```
    U8ProductInfo* productInfo = [[U8ProductInfo alloc] init];
    productInfo.productName = @"30钻石";
    productInfo.productDesc = @"30钻石";
    productInfo.productId = @"htage_8";
    productInfo.price = [NSNumber numberWithInt:30];
    productInfo.buyNum = 30;
    productInfo.roleId = @"12345";
    productInfo.roleName = @"角色";
    productInfo.roleLevel = @"66";
    productInfo.serverId = @"1";
    productInfo.serverName = @"桃源";
    productInfo.vip = @"1";
    
    [[U8SDK sharedInstance] pay:productInfo];
```

上面代码写好之后, 这个xcode就是以后打渠道包的母工程。 接下来，就可以通过U8SDK一键打包工具，
打出各个渠道的渠道包。
* 打包配置方法参考[IOS打包配置](ios_package.md)
* 打包方法参考[iOS平台打包](package.md)
