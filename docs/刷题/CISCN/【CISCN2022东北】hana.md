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