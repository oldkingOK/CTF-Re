# Linux的一些知识

## 常用命令

### 网络

```shell
tcpdump # 抓包工具
iptables # 配置网络，比如设置流量转发
ss -up # netstat的升级版，查看端口使用情况，u是udp，p是查看进程
traceroute # 追踪数据包在网络上的传输路径
ip addr # 查看本机ip

apt install ncat # 用来简单测试tcp、udp的连接
```

### 文件

```shell
file <文件> # 用来识别文件类型，也可用来识别文件的编码格式
readelf <文件> # 用来显示一个或者多个elf格式的目标文件的信息
stat <文件> # 查看文件的详细信息
tail -f <文件> # 一般用来看日志，跟随文件的更新
less <文件> # 从头开始看文件，以vim的操作方式
more <文件> # 也是从头开始看文件，但只能从上往下看，看完了自动退出
```

#### lsof

（List open files）用于列出当前系统中进程打开的所有文件的命令

#### find

```shell
find / -mtime +3 -type f -print # 三天“以前”改过的文件
find / -mtime -3 -type f -print # 三天“内”改过的文件
find / -type f -newermt '2024-2-10' ! -newermt '2024-04-25' # 指定时间
```

- `-type f` 指文件
- `ctime` c:change
- ctime参数指文件日期等状态性参数修改，mtime参数指内容改变

### 二进制分析

#### strace

```shell
strace -o xx.log <命令> # 追踪系统调用过程
```

![[image-20240427104442921.png]]

### 定时任务

```shell
cron
crontab
```
### xargs

有些命令不支持管道符输入，就可以用 xargs 把管道符左边的内容当作参数输入到右边的命令

```shell
find . -name "*.txt" | xargs file
find . -type f | xargs grep "wisher_GUI" # type f 文件
```

## Linux哲学

> 一切皆文件 Everything is a file

[Linux内核：进程管理——进程文件系统 /proc详解 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/619966043)

比如每一个进程，就会显示为 `/proc` 中的一个文件夹，以进程的 `pid` 命名，进程打开的文件（连接也会表示为文件）会放在 `/proc/<pid>/fd`，软链接到真正的文件

![[image-20240427104852648.png]]
