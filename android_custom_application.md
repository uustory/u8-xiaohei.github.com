游戏自定义Application
=========

NOTE:游戏母包AndroidManifest.xml中的application节点中的android:name属性必须设置为 com.u8.sdk.U8Application


配置U8Application
-------

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

自定义Application
-------

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

配置自定义Application
-------

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
