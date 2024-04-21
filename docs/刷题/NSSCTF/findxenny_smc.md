> 初识SMC技术

[原链接【NSSCTF】ezvm](https://www.nssctf.cn/problem/2225)

[文件](./assets/bin/nssctf_round2_findxenny.exe){:download="nssctf_round2_findxenny.exe"}

# 前置知识

**SMC**（Software-Based Memory Encryption）是一种局部代码加密技术，它可以将一个可执行文件的指定区段进行加密，使得黑客无法**直接**分析区段内的代码，从而增加恶意代码分析难度和降低恶意攻击成功的可能性。

即增大静态分析的难度，使用动态调试即可破招

## CTF中的SMC

SMC一般有俩种破解方法，
1. 第一种是找到对代码或数据加密的函数后通过idapython写解密脚本。
2. 第二种是动态调试到SMC解密结束的地方dump出来。

SMC的实现是需要对目标内存进行修改的，.text一般是没有写权限的。

那么就需要拥有修改目标内存的权限：

- 在linux系统中，可以通过mprotect函数修改目标内存的权限
- 在Windows系统中，VirtualProtect函数实现内存权限的修改

因此也可以观察是否有这两个函数来判断是否进行了SMC。 

## 参考文章：

- [【CSDN】CTF Reverse逆向学习之SMC动态代码加密技术](https://blog.csdn.net/Sciurdae/article/details/133717752)
- [【知乎】探究SMC局部代码加密技术](https://zhuanlan.zhihu.com/p/624554464)

# poc

```c
if ( (unsigned __int64)sub_7FF7DA121753(v15) >= 0xC )
```

这个是检查长度的，输入长度需要大于等于 `0xC`

```c
...
sub_7FF7DA121514(v9, v10, v11);
if ( (unsigned int)qword_7FF7DA139370(*v17)
|| (unsigned int)qword_7FF7DA139378(*v18)
|| (unsigned int)qword_7FF7DA139380(*v19) )
{
    v13 = sub_7FF7DA121262(std::cout, "Try harder");
    std::ostream::operator<<(v13, sub_7FF7DA1211FE);
}
else
{
    v12 = sub_7FF7DA121262(std::cout, "I can't believe my golden doge eye! weare comarde!");
    std::ostream::operator<<(v12, sub_7FF7DA1211FE);
}
```

> *2h后更新*
> 
> 函数 `sub_7FF7DA121514` 内部调用了 `VirtualProtect`，与SMC的特征相符

这三个qword就是SMC的结果，动态调试一下，发现在 `sub_7FF7DA121514` 函数之后，这三个qword就都赋值上了，分别双击进去，然后按P创建函数

分别是

```c
__int64 __fastcall sub_17E2A13A8C0(__int64 a1)
{
    if ( a1 == 0x665F756F795F686Fi64 )
    // 即 f_uoy_ho
    // 注意这里是以小端序显示数值，字符串应该是
    // oh_you_f
    // 而不是 f_uoy_ho
        return 0i64;
    else
        return -1i64;
}

__int64 __fastcall sub_17E2A13A4A0(__int64 a1)
{
    __int64 v1; // rcx
    __int64 v3[2]; // [rsp+0h] [rbp-40h]
    int v4[12]; // [rsp+10h] [rbp-30h] BYREF

    v3[0] = a1;
    qmemcpy(v4, "ound_our", 8);
    v1 = 0i64;
    while ( *((_BYTE *)v3 + v1) == *((_BYTE *)v4 + v1) )
    {
        if ( ++v1 >= 8 )
        return 0i64;
    }
    return -1i64;
}

_int64 __fastcall sub_17E2A13A210(__int64 a1)
{
    if ( a1 == 0x796E6E33785Fi64 )
    // _x3nny
        return 0i64;
    else
        return -1i64;
}
```

然后拼接字符串即可得到flag

```
oh_you_found_our_x3nny
```

> **注意**
>
> 看汇编一定比看F5准确
>
> 比如这里的 `0x665F756F795F686Fi64` 是小端序，如果从左到右读，是个未知的字符串
>
> 汇编中就有很清晰的字符串 `oh_you_f`

# 小知识

发现挺多程序都包含 `-858993460` 这个魔数的，不知道有什么用

```c
for ( i = 250i64; i; --i )
{
    *(_DWORD *)v3 = -858993460;
    v3 += 4;
}
```

这个 `-858993460` 其实是 `CCCCCCCC`，C编译器定义变量的**栈空间**填充的值**默认**是CC

这里的代码意思是用 **CC** 初始化栈空间