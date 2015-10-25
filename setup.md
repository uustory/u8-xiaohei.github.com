环境搭建
======

Note:**U8SDK整个开发采用了java,python,objective-c等语言，针对这些我们总结下需要搭建的环境**
JDK（1.6以上）
ADT Bundle(或者eclipse，android studio)
python（2.7）,同时需要安装PIL图片处理库


Windows下环境搭建
-------

Windows下主要搭建Android的开发环境，以及U8SDK Android部分的开发环境

**1、安装JDK**

><font size=2>这个百度，Google一大堆，可以自行参考。仅仅注意，不要安装错了，根据自己系统是32位还是64位，安装对应的JDK版本。<br/></font>
><font size=2>[下载地址](http://www.oracle.com/technetwork/java/javase/downloads/index-jsp-138363.html)</font>
><font size=2>[安装指导](http://jingyan.baidu.com/article/b24f6c82c989da86bfe5dab2.html)</font>

**2、安装ADT Bundle**

><font size=2>如果你之前已经安装过eclipse以及Android SDK开发环境，那么这里，你就无需安装了。如果你什么还没有安装，那么，建议你还是安装一个ADT Bundle吧，这是已经整合了Android SDK的eclipse版本。<br/></font>
><font size=2>[下载地址](http://www.androiddevtools.cn/)</font>
><font size=2>[安装指导](http://blog.csdn.net/aizquan/article/details/8974750)</font>

**3、安装python**

* 安装python

><font size=2>windows下，建议安装python2.7版本的32位版本。</font><br/>
><font size=2>[下载地址](https://www.python.org/downloads/)</font>
><font size=2>[安装指导](http://jingyan.baidu.com/article/19192ad8399a62e53e5707e0.html)</font><br/>
><font size=2>安装好python之后，我们还需要安装一个pil图片处理库。关于pil的安装，我们这里重点讲解一下。安装pil的时候，请注意你当前的python版本，32位还是64位。我们这里提供一个简便的安装方法。</font>

* 安装easy_install（[下载地址](https://bitbucket.org/pypa/setuptools/downloads)）

><font size=2>根据你当前使用python版本，下载对应版本的easy_install安装文件。比如，你安装的是32位的python2.7版本。那么你可以下载[setuptools-0.7.win32-py2.7.msi](https://bitbucket.org/pypa/setuptools/downloads/setuptools-0.7.win32-py2.7.msi),然后进行安装即可。</font><br/>
><font size=2>安装完easy_install之后，打开命令行，运行[easy_install]命令应该可以看到easy_install命令的相关信息。如果提示“easy_install 不是内部或者外部命令，也不是可运行的程序或批处理文件”，那请检查easy_install是否安装正确。</font>


* 安装 pip

><font size=2>easy_install安装完成之后，我们可以运行easy_install pip 命令来安装pip。注意cmd控制台中的信息，确保安装是成功的。</font><br/>
><font size=2>安装成功之后，运行[pip]命令，应该可以看到pip命令的信息。</font>

* 安装 PIL(pillow)

><font size=2>pip安装好之后，执行命令 pip install pillow 。 注意cmd控制台的信息，确保安装是成功的。</font><br/>
><font size=2>安装成功之后，PIL库就安装完成了。

关于PIL的安装，如果遇到问题，你可以访问我们博客上对应的文章。

[U8SDK扩展——处理各渠道角标](http://www.uustory.com/?p=1666)
[U8SDK——自动处理渠道SDK的角标（补充）](http://www.uustory.com/?p=1778)


Mac下环境搭建
--------

**1、安装JDK**

><font size=2>这个百度，Google一大堆，可以自行参考。仅仅注意，不要安装错了，根据自己系统是32位还是64位，安装MAC下的JDK版本。</font><br/>
><font size=2>[下载地址](http://www.oracle.com/technetwork/java/javase/downloads/index-jsp-138363.html)</font>
><font size=2>[安装指导](http://blog.csdn.net/anialy/article/details/21195775)</font>

**2、安装ADT Bundle**

><font size=2>如果你之前已经安装过eclipse以及Android SDK开发环境，那么这里，你就无需安装了。如果你什么还没有安装，那么，建议你还是安装一个ADT Bundle吧，这是已经整合了Android SDK的eclipse版本。</font><br/>
><font size=2>[下载地址](http://www.androiddevtools.cn/)</font>
><font size=2>[安装指导](http://blog.csdn.net/wwj_748/article/details/44806253)</font>

**3、安装python**

* 安装python

><font size=2>mac下已经自带了python2.7版本，所以无需安装python。直接安装PIL库即可。mac下的python自带了easy_install库，所以我们可以直接通过easy_install来完成PIL的安装。</font>

* 安装 pip

><font size=2>我们可以运行sudo easy_install pip 命令来安装pip。注意控制台中的信息，确保安装是成功的。</font><br/>
><font size=2>安装成功之后，运行[pip]命令，应该可以看到pip命令的信息。</font>

* 安装 PIL(pillow)

><font size=2>pip安装好之后，执行命令 sudo pip install pillow 。 注意控制台的信息，确保安装是成功的。</font><br/>
><font size=2>安装成功之后，PIL库就安装完成了。</font>

关于PIL的安装，如果遇到问题，你可以访问我们博客上对应的文章。

[U8SDK扩展——处理各渠道角标](http://www.uustory.com/?p=1666)
[U8SDK——自动处理渠道SDK的角标（补充）](http://www.uustory.com/?p=1778)

