IOS常见问题
=========

Appstore
---------
**怎样提交Appstore审核？**

打包工具生成的ipa包可能是无法直接提交到appstore的，这取决于配置的provision的类型， 如果provision是AdHoc的，那么ipa包只能在已经添加到provision的设备上安装，但是仍然可以通过Archive包来上传，打开xcode的organizer，可以看到打包工具生成的Archive包，选择上传即可。

**IAP充值验证流程**

当appstore充值成功后会出发回调OnPayPaid，在回调函数中把充值订单验证数据上传到服务器，然后调用finishTransaction方法结束订单。服务器拿到订单数据后，向Appstore发送订单验证请求，确认订单有效后给用户发货。

PP助手
---------

**模拟器编译失败**

PP助手不支持模拟器环境，只能选择真机编译

**PP助手怎样自动打包**

渠道自定义脚本 xcode_process.py
``` python
def post_process(self, project, infoPlist, sdkparams):
    buildConfig = self.target.get_buildconfig("Debug")

    buildConfig['buildSettings']['ONLY_ACTIVE_ARCH'] = 'NO'
```
渠道配置文件
``` json
{
	"archs": "armv7 arm64",
    "BuildConfig": "Debug"
}
```

91助手
---------
**91助手在ios9上显示方向错误**

91助手SDK没有适配ios8以上系统，这个问题不影响审核通过，可以无视。。。

其他
---------
**没有源码直接用ipa包可以打渠道包吗？**

U8SDK仅支持基于源码打包，我们以后会研究这种方式的可行性。不过我们提供了iparepacker工具，可以简单的修改ipa包，这个工具已经开源：https://github.com/uustory/iparepack

**编译报错error: no provisioning profile matches 'XC Ad Hoc: *'**

在common/config.json中修改provision字段，改为苹果开发者帐号里的provision的名字。