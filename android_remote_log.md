远程日志
===========

NOTE:有时候Android App闪退的时候，logcat中看不到相关错误堆栈，或者日志信息太多不利于观看。这个时候可以开启远程日志功能，来排查错误。

1、打包工具中开启远程日志

打包工具/games/games.xml中：

```
        <log>
            <param name="ulog.enable" value="true" desc="是否进行日志打印"/>
            <param name="ulog.level" value="DEBUG" desc="日志级别(DEBUG|INFO|WARNING|ERROR)"/>
            <param name="ulog.local" value="true" desc="是否在logcat中打印"/>
            <param name="ulog.remote" value="true" desc="是否在网页中远程打印，这里设置为true，需要启动scripts/uconsole.py"/>
            <param name="ulog.remote_interval" value="1000" desc="远程打印时间间隔，单位毫秒"/>
            <param name="ulog.remote_url" value="http://192.168.1.108:8090/" desc="远程打印地址，uconsole.py启动监听的地址"/>
        </log>

        1、ulog.enable：设置为true，开启日志总开关
        2、ulog.level：日志级别，调试的话，设置为DEBUG
        3、ulog.local：logcat中日志是否输出
        4、ulog.remote：是否开启远程日志
        5、ulog.remote_interval：远程日志上报间隔
        6、ulog.remote_url：远程日志控制台地址。 这里将ip设置为你电脑上的内网ip地址。 比如192.168.1.108

```

2、开启远程日志控制台：

命令行中进入 打包工具/scripts目录下， 执行：

```
    python uconsole.py 8090
```

如果出现 web.py 不存在或者没有安装。 那么先执行web.py 安装命令：

```
    pip install web.py 

```

或者

```
    python -m pip install web.py

```

安装完成之后，再次执行上面开启日志控制台命令。


3、浏览器中开启web控制台

浏览器中访问 http://localhost:8090。 如果看到网页一直在刷，说明日志控制台启动正常。

4、打包测试

重新打一个渠道SDK， 然后安装到手机上， 保证手机网络和当前电脑网络属于同一个局域网。 否则这个上面内网ip地址，手机上访问不到。

