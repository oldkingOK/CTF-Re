# MacOS虚拟机

## 完整教程

### 下载ISO

[黑苹果系统镜像-黑苹果星球 (heipg.cn)](https://heipg.cn/macos?os=cdr)

推荐下载 10.15 的，其他亲测很难装上

可以用邮箱登录，已经有会员了
### Unlock

[Releases · paolo-projects/auto-unlocker (github.com)](https://github.com/paolo-projects/auto-unlocker/releases)

### 创建虚拟机

选择“稍后安装”，然后选择插入磁盘iso

### 修改配置文件

> 用vscode打开 `macOS 14.vmx`

修改

- `board-id.reflectHost = "FALSE"`
- `ethernet0.virtualDev = "vmxnet3"`

粘贴

```text
board-id = "Mac-AA95B1DDAB278B95"  
hw.model.reflectHost = "FALSE"  
hw.model = "MacBookPro19,1"  
serialNumber.reflectHost = "FALSE"  
serialNumber = "C01234567890"
smc.version = "0"
cpuid.0.eax = "0000:0000:0000:0000:0000:0000:0000:1011"
cpuid.0.ebx = "0111:0101:0110:1110:0110:0101:0100:0111"
cpuid.0.ecx = "0110:1100:0110:0101:0111:0100:0110:1110"
cpuid.0.edx = "0100:1001:0110:0101:0110:1110:0110:1001"
cpuid.1.eax = "0000:0000:0000:0001:0000:0110:0111:0001"
cpuid.1.ebx = "0000:0010:0000:0001:0000:1000:0000:0000"
cpuid.1.ecx = "1000:0010:1001:1000:0010:0010:0000:0011"
cpuid.1.edx = "0000:0111:1000:1011:1111:1011:1111:1111"
featureCompat.enable = "TRUE"
```

### 参考

1. [Running a MacOS 13 Ventura VM in VMware (i12bretro.github.io)](https://i12bretro.github.io/tutorials/0764.html)
2. [VMware安装macOS报已禁用 CPU 错误 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/649809292)
3. [在 Windows 下使用 VMware Workstation 安装 macOS 的详细教程-黑苹果星球 (heipg.cn)](https://heipg.cn/tutorial/install-macos-by-using-vmware-in-windows.html)

# 优化

## 优化三部曲

### 安装任意来源APP

```shell
sudo spctl --master-disable
```

[黑苹果优化三部曲：时间同步/去除跑码模式/安装任意来源APP - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/595641213)

### Share文件夹

[边学边干折腾记——VMware虚拟化软件+MacOS虚拟机+星空组网2/2 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/693623836)

```sh
sh -c "$(curl -fsSL https://html.sqlsec.com/hidpi.sh)"
```