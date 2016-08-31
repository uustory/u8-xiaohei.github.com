抽象层API调用
===========

Note: U8SDK整套框架的核心原则就是彻底杜绝游戏和各个渠道SDK之间的耦合。对于每个游戏来说，只需要调用U8SDK抽象层框架提供的API即可，无需关心具体的渠道SDK是怎么接入的，需要游戏层中处理些什么逻辑。这里，我们就来看下U8SDK抽象层框架提供的API。


Android
--------

Android平台的U8SDK抽象层采用插件式的结构，便于抽象层能够无限支持更多的SDK类型，而不仅仅是渠道SDK的接入，比如可以基于U8SDK框架接入推送，分享，统计等第三方功能性SDK。

目前U8SDK支持的插件类型：

| 插件名称        | 插件类型      | 插件调用类  | 插件接口  |
| ---------------|:-----------:| ----------:|---------:|
| 用户插件        | 1            | com.u8.sdk.U8User| com.u8.sdk.IUser |
| 支付插件        | 2            | com.u8.sdk.U8Pay | com.u8.sdk.IPay  |
| 推送插件        | 3            | com.u8.sdk.U8Push| com.u8.sdk.IPush |
| 分享插件        | 4            | com.u8.sdk.U8Share| com.u8.sdk.IShare |
| 统计插件        | 5            | com.u8.sdk.U8Analytics| com.u8.sdk.IAnalytics |

这样的结构，就比较好扩张其他类型的插件，比如你希望将游戏apk的强制更新做成插件，集成到u8sdk中，你可能会定义一个IUpdate的插件类型，然后按照插件接入方式，进行接入，这样所有游戏的apk更新，就都可以使用了。

当然，我们在接入所有渠道SDK的时候，我们只需要实现两个插件，分别是［用户插件］和［支付插件］.

下面，我们就主要说明，游戏层中如果需要接入U8SDK,需要调用哪些接口。

**1、U8SDK初始化**

```
U8SDK初始化包括两部分，首先，你需要将游戏母工程的AndroidManifest.xml的application设置为com.u8.sdk.U8Application

然后在游戏的主Activity的onCreate方法中调用init方法：

U8SDK.getInstance().init(Activity context);
U8SDK.getInstance().onCreate();

最后，在主Activity的生命周期方法中，也需要对应调用U8SDK对应的方法：

  public void onActivityResult(int requestCode, int resultCode, Intent data){
    U8SDK.getInstance().onActivityResult(requestCode, resultCode, data);
    super.onActivityResult(requestCode, resultCode, data);
  }
  
  public void onStart(){
    U8SDK.getInstance().onStart();
    super.onStart();
  }
  
  public void onPause(){
    U8SDK.getInstance().onPause();
    super.onPause();
  }
  public void onResume(){
    U8SDK.getInstance().onResume();
    super.onResume();
  }
  public void onNewIntent(Intent newIntent){
    U8SDK.getInstance().onNewIntent(newIntent);
    super.onNewIntent(newIntent);
  }
  public void onStop(){
    U8SDK.getInstance().onStop();
    super.onStop();
  }
  public void onDestroy(){
    U8SDK.getInstance().onDestroy();
    super.onDestroy();
  }
  public void onRestart(){
    U8SDK.getInstance().onRestart();
    super.onRestart();
  }

```

设置回调监听类：

```
SDK登录，登出，支付成功登回调，包括所有失败等情况，SDK接入层都会通过对应的事件，通知到抽象层中。所以，我们需要在调用初始化方法之前先设置一个监听类来监听这些事件：

U8SDK.getInstance().setSDKListener(new IU8SDKListener(){

    //onResult中一般不做逻辑处理，仅仅提示一些成功活着失败的信息，或者可以打印日志，供调试
    @Override
    public void onResult(final int code, final String message) {
      
      U8SDK.getInstance().runOnMainThread(new Runnable() {
        
        @Override
        public void run() {
          //Toast.makeText(MainActivity.this, message, Toast.LENGTH_SHORT).show();
          Log.d("U8SDK", "onResult:"+message);
          switch(code){
          case U8Code.CODE_INIT_SUCCESS:
            Toast.makeText(MainActivity.this, "初始化成功", Toast.LENGTH_SHORT).show();
            break;
          case U8Code.CODE_INIT_FAIL:
            Toast.makeText(MainActivity.this, "初始化失败", Toast.LENGTH_SHORT).show();
            break;
          case U8Code.CODE_LOGIN_FAIL:
            Toast.makeText(MainActivity.this, "登录失败", Toast.LENGTH_SHORT).show();
            break;
          case U8Code.CODE_SHARE_SUCCESS:
            Toast.makeText(MainActivity.this, "分享成功", Toast.LENGTH_SHORT).show();
            break;
          case U8Code.CODE_SHARE_FAILED:
            Toast.makeText(MainActivity.this, "分享失败", Toast.LENGTH_SHORT).show();
            break;
          case U8Code.CODE_LOGOUT_SUCCESS:
            U8Analytics.getInstance().logout();
            break;
          default:
            Toast.makeText(MainActivity.this, message, Toast.LENGTH_SHORT).show();
              
          }
        }
      });
      
    }
    
    //SDK登录成功的回调，result里面是SDK返回的sid，token等数据，
    //具体的数据格式需要和U8Server协商好，登录认证时，U8Server拿到这个数据
    //时，按照固定的格式进行解析。
    @Override
    public void onLoginResult(String result) {
      Log.d("U8SDK", "The sdk login result is "+result);
      //如果这里有需要对界面进行操作，需要在UI线程中进行
      //U8SDK默认这一步成功之后，直接向U8Server进行二次登录认证，如
      //果你不想走U8SDK的登录认证流程，那么需要将U8SDK类中
      //onLoginResult中进行登录认证的代码移除，并在这里进行自己的逻辑。
    }
    
    //U8Server登录认证成功的回调，这里就可以将结果通知到游戏中，游戏客户端进行登录操作了。
    @Override
    public void onAuthResult(final UToken authResult) {
        MainActivity.this.runOnUiThread(new Runnable() {
          
          @Override
          public void run() {
            if(authResult.isSuc()){
              Toast.makeText(MainActivity.this, "获取Token成功:"+authResult.getToken(), Toast.LENGTH_SHORT).show();
              
              LoginGameTask task = new LoginGameTask();
              task.execute();
              
            }else{
              Toast.makeText(MainActivity.this, "获取Token失败", Toast.LENGTH_SHORT).show();
            }
            
          }
        });
    }         

    //SDK切换账号成功的回调，游戏中收到这个回调，如果玩家正在游戏中玩，需要退出到登录界面，重新登录
    @Override
    public void onSwitchAccount() {
      MainActivity.this.runOnUiThread(new Runnable() {
        @Override
        public void run()
        {
          Toast.makeText(MainActivity.this, "切换帐号成功", Toast.LENGTH_SHORT).show();
          btn_login.setText("登 录"); 
          textView_account.setText("未登录");
        }
      });
    }

    //SDK切换账号并登录成功的回调。用户已经切换到另一个账号，如果玩家正在游戏中玩，需要退出到登录界面，重新登录
    @Override
    public void onSwitchAccount(String result) {
      MainActivity.this.runOnUiThread(new Runnable() {
        @Override
        public void run()
        {
          Toast.makeText(MainActivity.this, "切换帐号并登录成功", Toast.LENGTH_SHORT).show();
          btn_login.setText("切换帐号");
        }
      });
    }

    //SDK登出或者SDK登录会话实效的回调，如果玩家正在游戏中玩，需要退出到登录界面，重新登录
    @Override
    public void onLogout() {
      MainActivity.this.runOnUiThread(new Runnable() {
        @Override
        public void run()
        {
          Toast.makeText(MainActivity.this, "个人中心退出帐号成功", Toast.LENGTH_SHORT).show();
          btn_login.setText("登 录");
          textView_account.setText("未登录");
        }
      });
    }

    //该接口给单机游戏使用，网游不用实现。单机游戏通过这个接口，表示支付成功，给玩家发道具
    @Override
    public void onPayResult(final PayResult result) {
        MainActivity.this.runOnUiThread(new Runnable() {
          @Override
          public void run()
          {
            Toast.makeText(MainActivity.this, "支付成功,商品:"+result.getProductID(), Toast.LENGTH_SHORT).show();
          }
        });
    }

    //这个接口暂时无用处
    @Override
    public void onInitResult(InitResult result) {
      
    }



});

```

**2、用户相关功能接口调用**

1) 登录(必须实现)

```
为了打开SDK登录界面，我们需要在UI线程中调用，所以，你可以如下方式调用：

U8SDK.getInstance().runOnMainThread(new Runnable() {
  
  @Override
  public void run() {
    U8User.getInstance().login();
  }
});

登录成功，在上面onLoginResult回调中进行处理对应逻辑

```

2) 切换账号(必须实现)

Note:部分渠道要求游戏内必须要有切换账号的按钮，点击可以切换回登录界面

```
调用切换账号接口，我们一样需要在UI线程中调用，同时，每个渠道SDK必须实现切换账号功能，如果SDK本身没有提供SDK切换账号功能，那么切换账号的接口，实际调用的就是登录的接口。

U8SDK.getInstance().runOnMainThread(new Runnable() {
  
  @Override
  public void run() {
    U8User.getInstance().switchLogin();
  }
});

切换账号成功，在上面onSwitchAccount回调中进行对应逻辑处理

```

3) 登出账号(非必需)

```
一般游戏中只需要实现切换账号功能，无需实现登出功能。该功能，是登出SDK账号，但是仅仅部分渠道支持，对于不支持登出的渠道，该方法默认不做任何逻辑。

所以如果要调用该接口，可以先判断当前渠道是否支持登出操作：

if(U8User.getInstance().isSupport("logout")) {
 
  U8SDK.getInstance().runOnMainThread(new Runnable() {
    
    @Override
    public void run() {
      U8User.getInstance().logout();
    }
  });
 
}

登出成功，在上面onLogout回调中进行处理。注意，这个回调不仅仅主动调用会出发，SDK中登出，也会触发该回调。

```

4) 显示个人中心(必需实现)

Note: 部分渠道要求游戏内必须要有［个人中心］按钮，点击进入渠道SDK的个人中心界面.但是因为是部分渠道有这个接口，部分渠道没有提供这个接口，所以，对于没有提供该接口的渠道，该方法不做任何逻辑。
游戏层需要根据支付支持该方法的判定，来显示或者隐藏该按钮。

```
游戏中可以通过U8User.getInstance().isSupport("showAccountCenter")接口判定当前渠道是否支持［个人中心］

if(U8User.getInstance().isSupport("showAccountCenter")) {
 
  U8SDK.getInstance().runOnMainThread(new Runnable() {
    
    @Override
    public void run() {
      U8User.getInstance().showAccountCenter();
    }
  });
 
}

```

5) 提交游戏数据(必须实现)

Note: 部分渠道要求在 选择服务器，创建角色，登录游戏，角色升级，退出游戏 等时刻，必须要上报游戏中玩家数据，以便渠道后台统计用户数据。所以，游戏层需要在特定的地方多次调用该方法。

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

选择服务器时，因为还没有进入游戏，无法知道角色数据，extraData中只需要传入服务器信息即可。

```
关于UserExtraData:

| 参数名称        | 参数类型          | 参数说明  |
| ------------- |:-------------:| -----:|
| dataType     | int | 调用时机|
| serverID| String| 玩家所在服务器的ID|
| serverName| String| 玩家所在服务器的名称|
| roleID | String | 玩家角色ID|
| roleName| String | 玩家角色名称|
| roleLevel| String | 玩家角色等级|
| moneyNum| String | 当前角色身上拥有的游戏币数量|


6) SDK的退出游戏确认框(必须实现)

Note: 部分渠道需要在按返回键的时候，弹出SDK的退出游戏确认框，但是不是每个渠道SDK都支持，所以，游戏需要根据isSupport方法判断是否支持，如果不支持，弹出游戏自己的退出游戏确认框

```
因为不是所有渠道SDK都支持，所以我们可以如下调用

public void exit(){
  if(U8User.getInstance().isSupport("exit")){
    U8SDK.getInstance().runOnMainThread(new Runnable() {
      
      @Override
      public void run() {
        U8User.getInstance().exit();
      }
    });
  }else{
        //游戏自己的退出确认框
        AlertDialog.Builder builder = new AlertDialog.Builder(MainActivity.this);
        builder.setTitle("退出确认");
        builder.setMessage("欧巴，现在还早，要不要再玩一会？");
        builder.setCancelable(true);
        builder.setPositiveButton("好吧",
                new DialogInterface.OnClickListener() {
                    public void onClick(DialogInterface dialog,
                            int whichButton) {
                      //这里什么都不用做
                    }
                });
        builder.setNeutralButton("一会再玩",
                new DialogInterface.OnClickListener() {
                    public void onClick(DialogInterface dialog,
                            int whichButton) {
                      //退出游戏
                      MainActivity.this.finish();
                      System.exit(0);
                    }
                });
        builder.show();     
  }
}

```

**3、支付接口**

```
支付接口比较简单，就是一个pay方法。游戏中仅仅需要将需要的参数传进去即可。

U8SDK.getInstance().runOnMainThread(new Runnable() {
  
  @Override
  public void run() {
    U8Pay.getInstance().pay(PayParams params);
  }

});

网游支付成功，通过上面onResult中code为U8CODE_PAY_SUCCESS通知支付成功。因为网游支付成功，不用做特殊逻辑。
单机支付成功，通过上面onPayResult()通知支付成功。

```
关于PayParams对象：

| 参数名称        | 参数类型          | 参数说明  |
| ------------- |:-------------:| -----:|
| productId     | String | 充值商品ID，游戏内的商品ID |
| productName      | String      |   商品名称，比如100元宝，500钻石...|
| productDesc| String      |    商品描述，比如 充值100元宝，赠送20元宝|
| price| int | 充值金额(单位：元)|
| ratio| int | 兑换比例，暂时无用途|
| buyNum| int | 购买数量，一般都是1|
| coinNum| int | 玩家当前身上剩余的游戏币 |
| serverID| String| 玩家所在服务器的ID|
| serverName| String| 玩家所在服务器的名称|
| roleID | String | 玩家角色ID|
| roleName| String | 玩家角色名称|
| roleLevel| String | 玩家角色等级|
| vip | String| 玩家vip等级 |
| payNotifyUrl| String | 支付回调地址，下单时，U8Server返回的|
| orderID | String | U8Server订单号，下单时，U8Server返回的|
| extension | String | 渠道相关的扩展数据，下单时，U8Server返回的|


iOS
--------

iOS平台的U8SDK抽象层同样采用插件式的结构，便于抽象层能够无限支持更多的SDK类型，而不仅仅是渠道SDK的接入，比如可以基于U8SDK框架接入推送，分享，统计等第三方功能性SDK。目前，iOS平台的抽象层，支持用户插件和支付插件

和Android平台一样，我们在接入所有渠道SDK的时候，我们只需要实现两个插件，分别是［用户插件］和［支付插件］.

下面，我们就主要说明，游戏层中如果需要接入U8SDK,完成渠道SDK的登录和支付,需要调用哪些接口。

**1、U8SDK初始化**

```
iOS平台U8SDK的初始化，我们首先需要在AppDelegate的didFinishLaunchingWithOptions中完成以下操作：

NSDictionary* sdkConfig = [[NSBundle mainBundle] objectForInfoDictionaryKey:@"U8SDK"];

NSMutableDictionary* sdkparams = sdkConfig ? [NSMutableDictionary dictionaryWithDictionary:sdkConfig] : [NSMutableDictionary dictionary];
[[U8SDK sharedInstance] initWithParams:sdkparams];

[[U8SDK sharedInstance] didFinishLaunchingWithOptions:launchOptions];

[[U8SDK sharedInstance] setupWithParams:nil];

同时让游戏主界面的ViewController实现U8SDKDelegate协议，并且实现协议中定义的方法。然后在ViewController中的viewDidLoad方法中调用：

[[U8SDK sharedInstance] setDelegate:self];

实现协议中的方法：

- (UIView*) GetView
{
    return [self view];
}

- (UIViewController* ) GetViewController
{
    
    return self;
}

-(void) OnPlatformInit:(NSNotification*)notification;
{
    NSLog(@"SDK 初始化成功的回调");
}
-(void) OnUserLogin:(NSNotification*)notification
{
    NSLog(@"SDK登录成功的回调");
}
-(void) OnUserLogout:(NSNotification*)notification
{
    NSLog(@"SDK用户登出或者SDK当前会话失效的回调");
}
-(void) OnPayPaid:(NSNotification*)notification
{
    NSLog(@"SDK支付成功的回调，这个回调仅仅是SDK支付成功，不代表玩家获得了游戏币，游戏币通常是SDK服务器异步通知到U8Server");
}

```

**2、用户相关功能接口调用**

1) 登录(必须实现)

```
调用登录接口，打开SDK登录界面

[[U8SDK sharedInstance].defaultUser login];

SDK登录成功的回调，通过Notification通知到抽象层中，可以在上面OnUserLogin回调中进行处理。这个时候，应该去U8Server做二次登录认证

```

2) 切换账号(必须实现)

```
部分渠道没有支持切换账号接口，那么切换账号默认调用的接口和登录相同。

[[U8SDK sharedInstance].defaultUser switchAccount];

切换账号成功的回调，和登录的回调相同，都是在OnUserLogin中处理

```

3) 登出(非必需实现)

```
部分渠道没有登出接口，那么登出接口默认不做任何实现

[[U8SDK sharedInstance].defaultUser logout];

登出成功的回调，在上面OnUserLogout回调方法中处理

```

4) 显示个人中心

Note: 部分渠道要求游戏内必须要有［个人中心］按钮，点击进入渠道SDK的个人中心界面.但是因为是部分渠道有这个接口，部分渠道没有提供这个接口，所以，对于没有提供该接口的渠道，该方法不做任何逻辑。
游戏层需要根据支付支持该方法的判定，来显示或者隐藏该按钮。

```
调用该方法时，先判断当前渠道是否提供了个人中心接口：

if([[U8SDK sharedInstance].defaultUser hasAccountCenter])
{
 
  [[U8SDK sharedInstance].defaultUser showAccountCenter];
 
}

```
**3、支付接口**

```
在调用支付的时候，和Android一样，游戏中需要传入对应的参数，然后调用支付插件的pay方法：

[[U8SDK sharedInstance].defaultPay pay:dic]; //参数dic是一个NSDictionary结构

支付成功的回调，可以在上面OnPayPaid回调方法中进行处理。一般网游这里不需要做特殊的处理，因为支付是异步的，这里支付成功，仅仅是SDK支付请求成功，并不代表玩家得到了游戏币。真正充值成功，是异步通知到游戏服务器的。

```
关于支付参数的说明：


| 参数Key        | 参数类型          | 参数说明  |
| ------------- |:-------------:| -----:|
| productId     | String | 充值商品ID，游戏内的商品ID |
| productName      | String      |   商品名称，比如100元宝，500钻石...|
| productDesc| String      |    商品描述，比如 充值100元宝，赠送20元宝|
| price| int | 充值金额(单位：元)|
| ratio| int | 兑换比例，暂时无用途|
| buyNum| int | 购买数量，一般都是1|
| coinNum| int | 玩家当前身上剩余的游戏币 |
| serverID| String| 玩家所在服务器的ID|
| serverName| String| 玩家所在服务器的名称|
| roleID | String | 玩家角色ID|
| roleName| String | 玩家角色名称|
| roleLevel| String | 玩家角色等级|
| vip | String| 玩家vip等级 |
| payNotifyUrl| String | 支付回调地址，下单时，U8Server返回的|
| orderID | String | U8Server订单号，下单时，U8Server返回的|
| extension | String | 渠道相关的扩展数据，下单时，U8Server返回的|

