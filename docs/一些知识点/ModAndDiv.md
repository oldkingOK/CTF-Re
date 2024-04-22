# 编译器对取余和除法运算的优化

> 2024-04-11

# 环境

- [CompilerExplorer](godbolt.org)
- x64 msvc v19.35

# 概述

看到奇怪的位运算、魔数，就考虑**求余**和**除法**

# 知识点

## 1. 算数右移和逻辑右移

[参考](https://demon.tw/programming/assembly-sar-shr.html)

> 汇编语言中SAR和SHR指令都是右移指令，SAR是算数右移指令（shift arithmetic right），而SHR是逻辑右移指令（shift logical right）。
> 
> 两者的区别在于SAR右移时保留操作数的符号，即用符号位来补足，而SHR右移时总是用0来补足。
>
> 例如10000000算数右移一位是11000000，而逻辑右移一位是01000000。

## 2. C标准的int

`#include<stdint.h>` 就可以使用各种int类型

注意：`long long int` 才是64位的， `long int` 和 `int`相同，即 `%lld` 才是64位

## 3. 求余的优化——生成**Magic Number**

> 小知识：取模!=取余
>
> 主要区别于负数Mod
> 
> 进行求模运算时：`c = [a/b] = -7 / 4 = -2`（向负无穷方向舍入），
>
> 进行求余运算时：`c = [a/b] = -7 / 4 = -1`（向0方向舍入）；
>
> 求模时：r = a - c*b =-7 - (-2)*4 = 1，
> 
> 求余时：r = a - c*b = -7 - (-1)*4 =-3。

#### 1）当模数为2的N次幂

无符号数很简单，右移N位即可

有符号数比较复杂

1. 逻辑与——保留N个低位和符号位
    比如 Mod 4
    ```c
    int tmp = i & 0x8000000C
    ```

2. 自减一
3. 逻辑或——保留N个低位的值，其他都赋值1
4. 自加一

代码如下：

```c
int mod(int a)
{
	int tmp = a & 0x80000003;
	if (tmp >= 0) return tmp;
	tmp--;
	tmp = tmp | -4;
	tmp++;
	return tmp;
}
```

这里和李师傅讨论了一下，【自减一】和【自加一】只会影响边缘值，比如 `-4 Mod -4`

`-4` 是 `0b11...1100`，【减减】和【或】之后，会变成 `0b11...1111`，

此时【加加】就会得到0

#### 2）非2次幂

[汇编语言IMUL指令：有符号数乘法](https://c.biancheng.net/view/3603.html)

[【PDF】DivcMore](https://github.com/oldkingOK/oldkingOK.github.io/blob/main/src/assets/pdf/divcMore.pdf)——[来源](https://stackoverflow.com/questions/2661697/most-optimized-way-to-calculate-modulus-in-c)

[【知乎】【编译笔记】变量除以常量的优化（一）——无符号除法](https://zhuanlan.zhihu.com/p/151038723)

知乎有个很精彩的定理证明，需要再次细看