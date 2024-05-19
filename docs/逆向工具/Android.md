# 安卓逆向

[新手向总结：IDA动态调试So的一些坑 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/145383282)

> ddms没有被删除tools/monitor.bat就是（注意配合java8才能运行，高版本不行，很坑）
>
> 在monitor.ini里-vmargs前面设置1.8的环境信息就行了  
> 
> 不过好像亲测Java18也能运行

```
-vm C:\Program Files\Java\jdk1.8.0_191\bin\javaw.exe  
-vmargs
```

## 部署WSA

[MustardChef/WSABuilds: Run Windows Subsystem For Android on your Windows 10 and Windows 11 PC using prebuilt binaries with Google Play Store (MindTheGapps) and/or Magisk or KernelSU (root solutions) built in. (github.com)](https://github.com/MustardChef/WSABuilds?tab=readme-ov-file)

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