自定义脚本
===========

NOTE:秉着游戏和SDK开发完全解耦的思想，遇到某些渠道SDK的特殊要求，可能需要和游戏耦合的，尽可能通过自定义脚本完成，而不叫直接写死。
自定义脚本一样，采用python编写。所以，你需要了解一点python的语法知识

举几个例子：
```
1、游戏中AndroidManifest.xml中所有的activity的android:launchMode属性都要设置为"standard"
2、某个activity的data节点是"fb"+游戏包名
3、WXEntryActivity需要放在游戏包名+.wxapi包名路径下
4、游戏启动Activity必须为渠道SDK提供的某个闪屏Activity
5、游戏Application必须为渠道SDK提供的某个Application
6、友盟统计中我们需要根据渠道来区分
......
```
等等这些情况，我们都能够通过自定义脚本来完成。否则，你就需要耦合该渠道SDK和具体的游戏。

U8SDK中，在每个渠道SDK打包的时候，可以执行一段自定义脚本。这段脚本在渠道SDK资源整合结束，二次打包之前执行。

脚本定义
-------
之前在[渠道SDK接入的配置](android_package.md)中,我们说过，每个渠道SDK接好之后，都配置到打包工具的config/sdk目录下。

如果当前渠道有需要执行特殊的逻辑，需要执行一段自定义脚本，那么在该渠道的配置目录下新建一个sdk_script.py文件，内容如下：

```
import file_utils
import os
import os.path
import config_utils
from xml.etree import ElementTree as ET
from xml.etree.ElementTree import SubElement
from xml.etree.ElementTree import Element
from xml.etree.ElementTree import ElementTree
import os
import os.path
import zipfile
import re
import subprocess
import platform
from xml.dom import minidom
import codecs
import sys

androidNS = 'http://schemas.android.com/apk/res/android'

def execute(channel, decompileDir, packageName):
	//这里写自定义逻辑


1、脚本入口函数固定，是execute。含有三个参数
2、channel:渠道对象,里面有当前渠道的参数等数据
3、decompileDir:当前渠道的工作目录
4、pacakageName:最终的包名

```

如果是插件目录，那么新建一个script.py，对应里面的接口是：

```
import file_utils
import os
import os.path
from xml.etree import ElementTree as ET
from xml.etree.ElementTree import SubElement
from xml.etree.ElementTree import Element
from xml.etree.ElementTree import ElementTree
import os
import os.path
import zipfile
import re
import subprocess
import platform
from xml.dom import minidom
import codecs
import sys

androidNS = 'http://schemas.android.com/apk/res/android'


def execute(channel, pluginInfo, decompileDir, packageName):
	//这里写自定义逻辑

```

脚本实例1
-------

1、场景

移动MM的渠道要求将mm.purchasesdk.iapservice.PurchaseServic设置一个名称为{packageName}+".purchaseservice.BIND"的action;mm.purchasesdk.iapservice.BillingLayoutActivity设置一个名称为{pacakageName}+".com.mmiap.activity"的action

2、解决方案

这个比较适合通过自定义脚本来修改AndroidManifest.xml文件。我们事先在SDKManifest.xml中这个service和activity中加上不带包名的action
然后通过自定义脚本来获取这个service和activity，然后修改action，加上包名前缀

3、代码片段

```
def execute(channel, decompileDir, packageName):
	manifestFile = decompileDir + "/AndroidManifest.xml"
	manifestFile = file_utils.getFullPath(manifestFile)
	ET.register_namespace('android', androidNS)
	key = '{' + androidNS + '}name'

	tree = ET.parse(manifestFile)
	root = tree.getroot()

	applicationNode = root.find('application')
	if applicationNode is None:
		return 1

	activityNodeLst = applicationNode.findall('service')
	if activityNodeLst is None:
		return 1

	for activityNode in activityNodeLst:
		name = activityNode.get(key)
		if name == 'mm.purchasesdk.iapservice.PurchaseService':
			intentNodes = activityNode.findall('intent-filter')
			if intentNodes != None and len(intentNodes) > 0:
				for intentNode in intentNodes:
					actionNodes = intentNode.findall('action')
					if actionNodes != None and len(actionNodes) > 0:
						for actionNode in actionNodes:
							actionName = actionNode.get(key)
							if actionName == '.purchaseservice.BIND':
								newName = packageName + actionName
								actionNode.set(key, newName)


	activityNodeLst = applicationNode.findall('activity')
	if activityNodeLst is None:
		return 1

	for activityNode in activityNodeLst:
		name = activityNode.get(key)
		if name == 'mm.purchasesdk.iapservice.BillingLayoutActivity':
			intentNodes = activityNode.findall('intent-filter')
			if intentNodes != None and len(intentNodes) > 0:
				for intentNode in intentNodes:
					actionNodes = intentNode.findall('action')
					if actionNodes != None and len(actionNodes) > 0:
						for actionNode in actionNodes:
							actionName = actionNode.get(key)
							if actionName == '.com.mmiap.activity':
								newName = packageName + actionName
								actionNode.set(key, newName)								


	tree.write(manifestFile, 'UTF-8')

	return 0
```

脚本实例2
-------

1、场景

360渠道SDK，如果要使用微信功能，我们需要将WXEntryActivity.java放到{packageName}+".wxapi"包名下面。

2、解决方案

因为最终的包名是游戏包名加上当前渠道后缀来的，所以我们不可能直接将该类放在对应的包名路径下，然后编译生成jar包。这样其他的游戏就无法使用该渠道SDK对应的功能了。

所以，我们还是通过自定义脚本来完成这个工作。

```
1、在该渠道的配置目录下新建一个extraFiles子目录，然后将360SDK.jar和WXEntryActivity.java拷贝到这个文件中。
2、打开WXEntryActivity.java文件，看看开头package， 也就是确认当前文件的包名是什么，一会我们要替换他。这里是com.u8.sdk.qh360.wxapi
3、编写自定义脚本，修改文件的package,重新编译WXEntryActivity.java，生成smali合并到最终的包名+".wxapi"路径下。
4、将该Activity配置到AndroidManifest.xml中

```

3、代码片段

```

def execute(channel, decompileDir, packageName):

	sdkDir = decompileDir + '/../sdk/' + channel['sdk']
	if not os.path.exists(sdkDir):
		file_utils.printF("The sdk temp folder is not exists. path:"+sdkDir)
		return 1

	extraFilesPath = sdkDir + '/extraFiles'
	relatedJar = os.path.join(extraFilesPath, '360SDK.jar')
	WXPayEntryActivity = os.path.join(extraFilesPath, 'WXEntryActivity.java')
	file_utils.modifyFileContent(WXPayEntryActivity, 'com.u8.sdk.qh360.wxapi', packageName+".wxapi")


	splitdot = ';'
	if platform.system() == 'Darwin':
		splitdot = ':'

	cmd = '"%sjavac" -source 1.7 -target 1.7 "%s" -classpath "%s"%s"%s"' % (file_utils.getJavaBinDir(), WXPayEntryActivity, relatedJar, splitdot, file_utils.getFullToolPath('android.jar'))

	ret = file_utils.execFormatCmd(cmd)
	if ret:
		return 1

	packageDir = packageName.replace('.', '/')
	srcDir = sdkDir + '/tempDex'
	classDir = srcDir + '/' + packageDir + '/wxapi'

	if not os.path.exists(classDir):
		os.makedirs(classDir)

	sourceClassFilePath = os.path.join(extraFilesPath, 'WXEntryActivity.class')
	targetClassFilePath = classDir + '/WXEntryActivity.class'

	file_utils.copy_file(sourceClassFilePath, targetClassFilePath)

	targetDexPath = os.path.join(sdkDir, 'WXEntryActivity.dex')

	if platform.system() == "Windows":
		dxTool = file_utils.getFullToolPath('dx.bat')
		cmd = '"%s" --dex --output="%s" "%s"' % (dxTool, targetDexPath, srcDir)

	else:
		dxTool = file_utils.getFullToolPath("/lib/dx.jar")

		cmd = file_utils.getJavaCMD() + ' -jar -Xmx512m -Xms512m "%s" --dex --output="%s" "%s"' % (dxTool, targetDexPath, srcDir)


	ret = file_utils.execFormatCmd(cmd)

	if ret:
		return 1

	ret = apk_utils.dex2smali(targetDexPath, decompileDir+'/smali', "baksmali.jar")

	if ret:
		return 1

	manifest = decompileDir + '/AndroidManifest.xml'
	ET.register_namespace('android', androidNS)
	name = '{' + androidNS + '}name'
	configChanges = '{' + androidNS + '}configChanges'
	exported = '{' + androidNS + '}exported'
	screenOrientation = '{' + androidNS + '}screenOrientation'
	tree = ET.parse(manifest)
	root = tree.getroot()

	appNode = root.find('application')
	if appNode is None:
		return 1

	activityNode = SubElement(appNode, 'activity')
	activityNode.set(name, packageName + '.wxapi.WXEntryActivity')
	activityNode.set(configChanges, 'keyboardHidden|orientation')
	activityNode.set(exported, 'true')
	activityNode.set(screenOrientation, 'portrait')

	tree.write(manifest, 'UTF-8')

	return 0
　```


脚本实例3
-------

1、场景

友盟统计插件，需要在AndroidManfeist.xml中配置一个UMENG_CHANNEL，这个值每个渠道都不同，后台根据这个渠道来区分来源

2、解决方案

因为这个值是每个渠道都不同的，所以，我们不可能每个渠道打包的时候，手动来设置。所以，最好的版本，就是通过自定义脚本来实现。
解决方案的思路是：我们在插件config.xml中的param中定义一个key为UMENG_CHANNEL的参数，然后通过自定义脚本，将这个参数的值，设置为当前渠道的名称

3、代码片段

```
import file_utils
import os
import os.path
from xml.etree import ElementTree as ET
from xml.etree.ElementTree import SubElement
from xml.etree.ElementTree import Element
from xml.etree.ElementTree import ElementTree
import os
import os.path
import zipfile
import re
import subprocess
import platform
from xml.dom import minidom
import codecs
import sys

androidNS = 'http://schemas.android.com/apk/res/android'


def execute(channel, pluginInfo, decompileDir, packageName):

	if 'params' in pluginInfo and len(pluginInfo['params']) > 0:
		for param in pluginInfo['params']:
			name = param.get('name')
			if name == 'UMENG_CHANNEL':
				param['value'] = channel['name']
				break
```
