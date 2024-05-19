[文件](./assets/bin/hana.exe){:download="hana.exe"}

# 参考
- [hana 题解（易语言逆向） - S1nyer - 博客园](https://www.cnblogs.com/S1nyer/p/18164717) —— 找出UPX加壳异常，巧脱壳
- [hana-CSDN博客](https://blog.csdn.net/qq_34010404/article/details/125893052) —— 硬脱壳，学习学习

[【CISCN 2022 东北】hana | NSSCTF](https://www.nssctf.cn/problem/2406)

需要两个工具：

- [IDA易语言反编译插件E-Decompiler - 吾爱破解 ](https://www.52pojie.cn/thread-1684608-1-1.html)
- [易语言知识库 - 最新易语言在线帮助手册 (125.la)](https://esdn.125.la/)

[[IDA插件#易语言]]

扔进DIE，发现有UPX的特征，但是无法直接 `upx -d`，尝试用 **ESP定律** 手动脱壳，但是没脱干净，有些易语言函数识别不出来

国赛考点重点一般不会在壳上，可能是改几个字符upx就没办法识别了，所以可以用正常的upx打包文件对比一下

这里对比出来，两个段的名称是VMP0和VMP1，正常UPX壳应该是UPX0和UPX1，改了之后就可以 `upx -d` 了

用插件恢复中文函数名之后，查看 `字符集比较` 的交叉引用，用到了 `数据加密` 函数，通过查手册，这是个RC4加密，然后找到对应的密钥即可

## 手脱壳

> 跟着教程 www.bilibili.com/video/BV1Gx4y1i7ty

扔进x32dbg，但是一直F8都没有 `pushad`，然后一直 `执行到返回` （Ctrl+F9）就可以了 ![[image-20240513152615722.png|20]]

![[image-20240513153451091.png]]

执行到 `pushad` （把所有寄存器压到堆栈），按F8单步一次，然后观察寄存器

![[image-20240513153533384.png]]

在左下角内存区域 Ctrl + G ，输入 esp，跳到esp，然后右键下一个 `硬件断点` - `访问断点` ，多少字节都可以，然后按 F9 运行，就会断到这里

![[image-20240513153706389.png]]

左下角也会有提示：

![[image-20240513153956793.png]]

`jmp hana.45FC55` 是一个大跳，选中，按 Enter 跳过去

![[image-20240513154441435.png]]

0045FC55，这就是OEP了（程序真正入口点），然后调用自带的插件

![[image-20240513154524079.png|300]]

![[image-20240513154554056.png|300]]

OEP 填上 0045FC55 ，然后点 `转储` ，保存到 `hana_dump.exe` ，不过一般是不能运行的

![[image-20240513154733459.png|200]]

得修复导入表，用 x32dbg 打开 `hana_dump.exe`，然后打开刚才的插件

点 `IAT自动搜索` - `获取导入` ，然后就会自动搜索导入表，然后点击 `修复转储`，打开 `hana_dump.exe`

就会生成一个以 `SCY` 结尾的程序

![[image-20240513155044973.png]]

这时候就已经修复好了，可以直接双击运行