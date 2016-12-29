自定义Application
=========

游戏母包Application配置
-------

NOTE:游戏母包AndroidManifest.xml中的application节点中的android:name属性必须设置为 com.u8.sdk.U8Application


**1、游戏中AndroidManifest.xml中，将application节点的android:name属性设置为 com.u8.sdk.U8Application**

~~~~~~
    <application
        android:allowBackup="true"
        android:icon="@drawable/ic_launcher"
        android:label="@string/app_name"
        android:name="com.u8.sdk.U8Application" >
        <activity
            android:name="com.u8.sdk.test.MainActivity"
            android:label="@string/app_name" 
            android:screenOrientation="nosensor"
            android:launchMode="singleTask"
            android:configChanges="orientation|keyboardHidden|screenSize">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>        
    </application>
~~~~~~

**2、如果游戏有自己的Application**

如果游戏有自己的Application需求，或者需要在Application的生命周期方法中，做一些操作。 那么可以实现IApplicationListener里面的接口。

比如，我们定义一个GameProxyApplication实现IApplicationListener接口：

~~~~~~

    public class GameProxyApplication implements IApplicationListener{

        @Override
        public void onProxyCreate() {
            // TODO 需要在Application的onCreate中的操作，放在这里
            //如果需要获取到原始Application对象，通过 U8SDK.getInstance().getApplication()来获取
            
            Log.d("Game", "On Application Create");
            
        }

        @Override
        public void onProxyAttachBaseContext(Context base) {
            // TODO 需要在Application的attachBaseContext中的操作，放在这里
            
        }

        @Override
        public void onProxyConfigurationChanged(Configuration config) {
            // TODO 需要在Application的onConfigurationChanged中的操作，放在这里
            
        }

        @Override
        public void onProxyTerminate() {
            // TODO 需要在Application的onTerminate中的操作，放在这里
            
        }

    }

~~~~~~

**3、配置GameProxyApplication**

上面定义了游戏的Application，紧接着，在AndroidManifest.xml中，将上面我们定义的GameProxyApplication，配置到meta-data中，android:name为U8_Game_Application。

~~~~~~
    <application
        android:allowBackup="true"
        android:icon="@drawable/ic_launcher"
        android:label="@string/app_name"
        android:name="com.u8.sdk.U8Application" >
        <meta-data android:name="U8_Game_Application" android:value="com.u8.sdk.test.GameProxyApplication" />
        <activity
            android:name="com.u8.sdk.test.MainActivity"
            android:label="@string/app_name" 
            android:screenOrientation="nosensor"
            android:launchMode="singleTask"
            android:configChanges="orientation|keyboardHidden|screenSize">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>  
    </application>
~~~~~~


渠道SDK自定义Application
-------

部分渠道SDK，需要继承他们提供的Application，或者需要在Application对应的方法中调用他们的一些接口。这个时候，我们怎么来做呢？

比如搜狗渠道SDK，需要在Application的onCreate中调用他的prepare接口。我们按照如下的方式，来实现Application逻辑


**1、实现IApplicationListener接口**

首先定义一个SGProxyApplication(类名自定义)，实现IApplicationListener接口

~~~~~~

    public class SGProxyApplication implements IApplicationListener{

        private SogouGamePlatform mSogouGamePlatform = SogouGamePlatform.getInstance();

        @Override
        public void onProxyCreate() {
            
            initOnApplicationCreate(U8SDK.getInstance().getSDKParams());
            
        }

        @Override
        public void onProxyAttachBaseContext(Context base) {
            // TODO Auto-generated method stub
            
        }

        @Override
        public void onProxyConfigurationChanged(Configuration config) {
            // TODO Auto-generated method stub
            
        }


        @Override
        public void onProxyTerminate() {
            SogouGamePlatform.getInstance().onTerminate();
        }


        private void initOnApplicationCreate(SDKParams params){
            this.gid = params.getInt("SG_GID");
            this.appKey = params.getString("SG_APPKEY");
            this.gameName = params.getString("SG_GAME_NAME");
            this.payRatio = params.getInt("SG_PAY_RATIO");
            this.coinName = params.getString("SG_COIN_NAME");
            this.fixedPay = params.getBoolean("SG_FIXED_PAY");
            
            SogouGameConfig config = new SogouGameConfig();     
            // 开发模式为true，false是正式环境
            // 请注意，提交版本设置正式环境 
            config.devMode = false;
            config.gid = this.gid;
            config.appKey=this.appKey;
            config.gameName=this.gameName;
            // SDK准备初始化
            mSogouGamePlatform.prepare(U8SDK.getInstance().getApplication(), config);
            
        }


    }

~~~~~~

**2、配置ProxyApplication**

上面实现了SGProxyApplication之后，我们还需要在SDKManifest.xml中配置这个proxyApplication属性

配置在applicationConfig节点的proxyApplication属性上：

~~~~~~

    <applicationConfig keyword="com.sogou.gamecenter.sdk.activity.SogouLoginActivity" 
        proxyApplication="com.u8.sdk.SGProxyApplication">


        <!-- applicationConfig中其他内容，activity,service... -->

    </applicationConfig>        

~~~~~~

