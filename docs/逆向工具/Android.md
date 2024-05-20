# 安卓逆向

## 1. 安卓环境

真机或者模拟器，最好用备用机器作为真机，需要root和安装Magisk

| 环境    | 优点         | 缺点                     |
| ----- | ---------- | ---------------------- |
| 真机    | 稳定，arm64架构 | root的过程比较麻烦            |
| 蓝叠模拟器 | 稳定         | 仅支持x64架构，不能安装Magisk    |
| WSA   | 体验很好       | 不稳定，遇到崩溃的app会导致WSA系统重启 |
|       |            |                        |
## 2. 打开调试总开关

> 即把 `ro.debuggable` 设置成1

### 使用Magisk resetprop

> 需要root；需要安装Magisk；真机、WSA均可
> 
> 重启后失效

```sh
adb shell "su -c magisk resetprop ro.debuggable 1"
adb shell "stop;start;"
```

### MagiskHidePropsConf模块

> 需要root；需要安装Magisk；重启后依然生效

[Magisk-Modules-Repo/MagiskHidePropsConf](https://github.com/Magisk-Modules-Repo/MagiskHidePropsConf)

安装模块之后

```shell
adb shell "su -c prop ro.debuggable 1" # 然后按y回车
adb shell "su -c magisk resetprop ro.debuggable" # 检查是否设置成功
```

### 使用mprop

> 需要root；不需要安装Magisk；重启后失效；只有armv8和v7

[wpvsyou/mprop](https://github.com/wpvsyou/mprop)

```shell
adb push mprop /data/local/tmp/
adb shell "su -c chmod +x /data/local/tmp/mprop"
adb shell "su -c /data/local/tmp/mprop ro.debuggable 1"
```


> ddms没有被删除tools/monitor.bat就是（注意配合java8才能运行，高版本不行，很坑）
>
> 在monitor.ini里-vmargs前面设置1.8的环境信息就行了  
> 
> 不过好像亲测Java18也能运行

```
-vm C:\Program Files\Java\jdk1.8.0_191\bin\javaw.exe  
-vmargs
```

## 部署WSA（不稳定）

[MustardChef/WSABuilds](https://github.com/MustardChef/WSABuilds?tab=readme-ov-file)

注意需要使用最新版的 7z 解压，`Run.bat` 即可安装
## 调试

> 不允许调试的app，强行调试

```shell
adb connect 127.0.0.1:58526
adb shell "su -c magisk resetprop ro.debuggable 1"
adb shell "stop;start;"
```

```shell
adb shell am start -D -n com.example.ctf/.MainActivity
```

然后就可以 Jeb Attach 了

### IDA调试so

```shell
adb push android_x64_server /data/local/tmp/
adb forward tcp:23946 tcp:23946 # 端口
adb shell
su
cd /data/local/tmp
chmod +x android_x64_server
./android_x64_server
```

```shell
$package="com.example.ezadvm"
adb shell am start -D -n $package/.MainActivity # 启动
# IDA Attach
# 查看pid
# $tmp_pid=(adb shell "ps | grep '$package' | tr -s ' ' | cut -d ' ' -f 2")
jdb -connect com.sun.jdi.SocketAttach:hostname=127.0.0.1,port=8700
```

总：

准备：

```shell
adb connect 127.0.0.1:58526
adb shell "su -c magisk resetprop ro.debuggable 1"
adb shell "stop;start;"
```

```shell
adb forward tcp:23946 tcp:23946 # 端口
adb shell "su -c /data/local/tmp/android_x64_server"
```

```shell
$package="com.example.re11113"
adb shell am start -D -n $package/.MainActivity # 启动
$tmp_pid=(adb shell "ps | grep '$package' | tr -s ' ' | cut -d ' ' -f 2")
echo "attach_process($tmp_pid)"
# 复制到这里，IDA Attach
adb forward tcp:8700 jdwp:$tmp_pid
jdb -connect com.sun.jdi.SocketAttach:hostname=127.0.0.1,port=8700
# connect之后会卡住，在IDA按F9就可以继续运行了
```

但是报错了：

```log
got SIGSEGV signal (Segmentation violation) (exc.code b, tid 11422)
```

难搞

```shell
adb shell "su -c logcat | grep $tmp_pid" > logcat.log
```

## 参考资料

- [Android搞机之打开系统调试总开关ro.debuggable - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/660604996)
- [新手向总结：IDA动态调试So的一些坑 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/145383282)

