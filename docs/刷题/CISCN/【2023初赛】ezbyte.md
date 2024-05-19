

- [gaoyucan - 博客园 (cnblogs.com)](https://www.cnblogs.com/gaoyucan/p/17440735.html)
- [ezbyte-CSDN博客](https://blog.csdn.net/qq_54894802/article/details/130956092) —— 主要思路
- [C++异常处理机制（throw、try、catch、finally） - 知道了呀~ - 博客园 (cnblogs.com)](https://www.cnblogs.com/-citywall123/p/12901301.html)
- [c++ 异常处理（1） - twoon - 博客园 (cnblogs.com)](https://www.cnblogs.com/catch/p/3604516.html)
- [c++ 异常处理（2） - twoon - 博客园 (cnblogs.com)](https://www.cnblogs.com/catch/p/3619379.html)

分析了两年半的程序，就是这么简单：

```cpp
std::cin >> str;
std::cout << str << std::endl;
```

通过对比正常程序和去除了符号表的程序

正常程序的 `std::cout << str`：

![[image-20240505222716725.png]]

去除符号表之后：

![[image-20240505222735628.png]]

## 程序分析

```c
sub_4D1840((unsigned int)"%100s", (unsigned int)v7, (unsigned int)v8, 0, v3, v4);
// std:cin >> v7
v5 = sub_46F4F0(&unk_5D5520, v7);
ZNSirsEPFRSiS_E_1(v5, ZSt4endlIcSt11char_traitsIcEERSt13basic_ostreamIT_T0_ES6_);
// std::cout << v7 << endl
sub_404C21((unsigned __int8 *)v7);
// 检验函数
```

这个函数是对部分位数进行检测，必须为 `flag{________________________________3861}`

然后有一部分汇编指令没有生成伪代码：

```asm
.text:0000000000404CDB mov     rax, [rbp+var_28]
.text:0000000000404CDF mov     rax, [rax+5]
.text:0000000000404CE3 mov     [rbp+var_20], rax ; 意思是var_20 = flag[5:5+8]
.text:0000000000404CE7 mov     rax, [rbp+var_28]
.text:0000000000404CEB mov     rax, [rax+0Dh]
.text:0000000000404CEF mov     [rbp+var_18], rax ; var_18 = flag[5+8:5+8*2]
.text:0000000000404CF3 mov     rax, [rbp+var_28]
.text:0000000000404CF7 mov     rax, [rax+15h]
.text:0000000000404CFB mov     [rbp+var_10], rax ; var_10 = flag[5+8*2:5+8*3]
.text:0000000000404CFF mov     rax, [rbp+var_28]
.text:0000000000404D03 mov     rax, [rax+1Dh]
.text:0000000000404D07 mov     [rbp+var_8], rax ; var_18 = flag[5+8*3:5+8*4]
.text:0000000000404D0B mov     r15, [rbp+var_8]
.text:0000000000404D0F mov     r14, [rbp+var_10]
.text:0000000000404D13 mov     r13, [rbp+var_18]
.text:0000000000404D17 mov     r12, [rbp+var_20]
.text:0000000000404D1B call    sub_404BF5      ; Call Procedure
```

把中间的32个字符分成4组，分别存进 `r12 r13 r14 r15`

然后这个 `sub_404BF5` 就会抛出一个异常，检查main函数

![[image-20240508221202612.png]]

右上角有一些代码，通过判断r12来输出”Yes!“，但是代码里面都没有提到r12，看来是藏起来了

[通过DWARF Expression将代码隐藏在栈展开过程中-软件逆向-看雪-安全社区|安全招聘|kanxue.com](https://bbs.kanxue.com/thread-271891.htm)
藏到了一个虚拟机里，可以通过 `readelf -w ezbyte_patch > output.txt` 获取

然后搜索 `r12`

```
(DW_OP_constu: 2616514329260088143; 
DW_OP_constu: 1237891274917891239; 
DW_OP_constu: 1892739; 
DW_OP_breg12 (r12): 0; 
DW_OP_plus; 
DW_OP_xor; 
DW_OP_xor; 
DW_OP_constu: 8502251781212277489; 
DW_OP_constu: 1209847170981118947; 
DW_OP_constu: 8971237; 
DW_OP_breg13 (r13): 0; 
DW_OP_plus; 
DW_OP_xor; 
DW_OP_xor; 
DW_OP_or; 
DW_OP_constu: 2451795628338718684; 
DW_OP_constu: 1098791727398412397; 
DW_OP_constu: 1512312; 
DW_OP_breg14 (r14): 0; 
DW_OP_plus; 
DW_OP_xor; 
DW_OP_xor; 
DW_OP_or; 
DW_OP_constu: 8722213363631027234; 
DW_OP_constu: 1890878197237214971; 
DW_OP_constu: 9123704; 
DW_OP_breg15 (r15): 0; 
DW_OP_plus; 
DW_OP_xor; 
DW_OP_xor; 
DW_OP_or)
```

茶园官方文档 https://dwarfstd.org/doc/DWARF5.pdf

```
DW_OP_constu: 8722213363631027234：将一个无符号整数压入堆栈。
DW_OP_constu: 1890878197237214971：将另一个无符号整数压入堆栈。
DW_OP_constu: 9123704：将第三个无符号整数压入堆栈。
DW_OP_breg15 (r15): 0：从 r15 这个寄存器中读取一个值，并将其加上偏移量 0。
DW_OP_plus：从堆栈中弹出两个值，相加后再将结果压入堆栈。
DW_OP_xor：从堆栈中弹出两个值，进行异或运算后再将结果压入堆栈。
DW_OP_xor：重复前面的操作，再进行一次异或运算。
DW_OP_constu: 2451795628338718684：将另一个无符号整数压入堆栈。
DW_OP_constu: 1098791727398412397：将另一个无符号整数压入堆栈。
DW_OP_constu: 1512312：将第三个无符号整数压入堆栈。
DW_OP_breg14 (r14): 0：从 r14 这个寄存器中读取一个值，并将其加上偏移量 0。
DW_OP_plus：从堆栈中弹出两个值，相加后再将结果压入堆栈。
DW_OP_xor：从堆栈中弹出两个值，进行异或运算后再将结果压入堆栈。
DW_OP_xor：重复前面
```