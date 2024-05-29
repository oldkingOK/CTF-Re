# Debug-x64dbg脱壳

[文件](./assets/Bin/Debug.exe){:download="Debug.exe"}

## 更新

参考：[Windows平台反调试技术学习-看雪](https://bbs.kanxue.com/thread-280080.htm)

这里介绍了各种反调试的方法，本题用到了：

1. 时钟检测——检测程序运行时间戳，如果过慢，说明是在调试
2. TLS回调函数——在程序入口之前就可以执行
## 反调1

用 x64dbg 打开，调试到这里的时候按F7或者F8都会跑飞，直接终止

![[image-20240529201214421.png]]

查阅了一下资料，这个是反调试的一种，相当于jmp，但是可以阻止调试器追踪

```c
NTSTATUS NTAPI 
NtContinue (
  IN PCONTEXT ThreadContext,
  IN BOOLEAN  RaiseAlert
);
```

其中 `PCONTEXT` 是个包含目标 `rip` 的结构体，只需要在这个地址打个断点然后F9即可

询问ai可知，是在头文件 `winternl.h` 中有 PCONTEXT 的定义，新建 `ok.h`

```c
// file ok.h
struct PCONTEXT {
    unsigned long long P1Home;
    unsigned long long P2Home;
    unsigned long long P3Home;
    unsigned long long P4Home;
    unsigned long long P5Home;
    unsigned long long P6Home;
    unsigned long ContextFlags;
    unsigned long MxCsr;
    unsigned short SegCs;
    unsigned short SegDs;
    unsigned short SegEs;
    unsigned short SegFs;
    unsigned short SegGs;
    unsigned short SegSs;
    unsigned long EFlags;
    unsigned long long Dr0;
    unsigned long long Dr1;
    unsigned long long Dr2;
    unsigned long long Dr3;
    unsigned long long Dr6;
    unsigned long long Dr7;
    unsigned long long Rax;
    unsigned long long Rcx;
    unsigned long long Rdx;
    unsigned long long Rbx;
    unsigned long long Rsp;
    unsigned long long Rbp;
    unsigned long long Rsi;
    unsigned long long Rdi;
    unsigned long long R8;
    unsigned long long R9;
    unsigned long long R10;
    unsigned long long R11;
    unsigned long long R12;
    unsigned long long R13;
    unsigned long long R14;
    unsigned long long R15;
    unsigned long long Rip;
};
```

![[image-20240529201906240.png]]

先调试到这，然后点 Struct，右键 - Parse header，选择刚创建的文件

输入命令 `EnumTypes` 就可以在 `Log` 标签看到所有的类型定义

![[image-20240529202100777.png]]

然后回到 Struct 标签，右键 - Visit type，输入 `PCONTEXT` 回车 —— 输入 `RCX` 或者 `RBX` 回车

![[image-20240529202240838.png]]

就可以看到目标 rip 了

![[image-20240529202333350.png]]

右键 —— Follow value in disassembler，然后F2打个断点，然后F9，就可以继续调试了

参考：

- [通过NtContinue修改3环程序执行流程-看雪](https://bbs.kanxue.com/thread-269492.htm)
- [assembly - How to bypass ZwContinue? - Reverse Engineering Stack Exchange](https://reverseengineering.stackexchange.com/questions/8813/how-to-bypass-zwcontinue)

## esp定律

调到这，调试器会断开，步进看看

![[image-20240529202820279.png]]

这里也是

![[image-20240529202859360.png]]

继续深入

![[image-20240529202937705.png]]

这里有很多个 push 类似 x32 的 pushad，可以上esp大法了，在 `push rdi` 之后的esp处下个硬件断点（实测在push rbp下断点更好）

![[image-20240529203249944.png]]

这里有个大跳，把断点取消掉之后，直接F4，再F8一下，就进入OEP了

然后用 Scylla 插件，IAT自动搜索——获取导入——转储 保存到`Debug_dump.exe`——修复转储 选择 `Debug_dump.exe` —— 就会生成 `Debug_dump_SCY.exe` 

运行正常，脱壳成功

## 反调2

无论怎么打断点，在 start 打断点也好，也断不了，程序运行2-3秒就会自动关闭——因为报异常了

![[image-20240529213224806.png]]

> 没办法用frida，因为有SMC，就算定位到了程序，动调不了也只是一坨加密数据

在栈里往上翻翻，程序已经调用exit了

![[image-20240529214510391.png]]

说明还没 `start` 就已经被反调了，再翻翻

![[image-20240529214622164.png]]

![[image-20240529214629937.png]]

找到了，在这，右键——Force jump

## 反调3

直接在 `exit` 下断点

![[image-20240529215253992.png]]

这里也藏了一个 `IsDebuggerPresent` ，force jump掉

## 反调4

![[image-20240529215713115.png]]

调用完这行，程序就会退出

![[image-20240529215744189.png]]

![[image-20240529215822399.png]]

![[image-20240529215835310.png]]

这是根据程序运行时间长度来决定是否退出程序，

![[image-20240529215859468.png]]

Force jump 即可

## 程序分析

![[image-20240529220053393.png]]

读取 `myflag.txt` 到这

然后进入第一个函数，动调一下，是个SMC，动态加载出代码，调完之后F5就可以看到加密流程了

![[image-20240529221016385.png]]

是个简单的加加减减异或加密

```python
enc = [
  0xCF, 0xD9, 0xC0, 0xC8, 0xDF, 0xCD, 0x0C, 0xD2, 0x43, 0x98, 
  0x10, 0xC0, 0x83, 0x43, 0x9A, 0x10, 0xCD, 0x42, 0x8C, 0x4A, 
  0x10, 0xC8, 0x82, 0x83, 0x4A, 0x9F, 0x8C, 0xDF, 0x98, 0x42, 
  0x8C, 0xDF, 0x84, 0x82, 0x83, 0x46, 0x52, 0x52, 0x52, 0x0E
]

r = []

for i in range(len(enc)):
    try1 = (enc[i] ^ 0x51) + 30
    try2 = (enc[i] ^ 0xCD) + 32
    try3 = (enc[i] ^ 0xAB) - 32
    if try1 in range(0, 0x7E) and (try1 <= 0x60 or try1 >= 0x7B) and (try1 <= 0x40 or try1 >= 0x5B):
        r += [try1]
    if try2 in range(0, 0x7E) and (not (try2 <= 0x60 or try2 >= 0x7B)) and (try2 <= 0x40 or try2 >= 0x5B):
        r += [try2]
    if try3 in range(0, 0x7E) and (not (try3 <= 0x40 or try3 >= 0x5B)):
        r += [try3]

print(bytes(r))
```

