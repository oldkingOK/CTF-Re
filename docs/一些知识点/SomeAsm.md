# 常见疑难杂汇编指令

> 2024-04-13
# imul 和 mul

[汇编语言IMUL指令：有符号数乘法](https://c.biancheng.net/view/3603.html)

# idiv 和 div

单操作数，只能传入寄存器

- 除数是BYTE,被除数存于AX,商存放在AL,余数放在AH
- 除数是WORD,被除数存于DX:AX,商存放在AX,余数放在DX
- 除数是DWORD,被除数存于EDX:EAX,商存放在EAX,余数放在EDX

### 汇编测试

```asm
lea     rax, [rbp+var_4] ; Load Effective Address
mov     rdx, rax
lea     rax, Format     ; Load Effective Address
mov     rcx, rax        ; Format
call    scanf           ; Call Procedure
mov     eax, [rbp+var_4]
mov     edx, eax
mov     ecx, 3
idiv    ecx             ; Signed Divide
```

执行程序，输入字符，回车——程序卡死

使用ida调试，运行到 `idiv` 的时候报错：**Integer overflow**

查阅资料：

> You're actually dividing a 128-bit number in RDX:RAX by RCX. So if RDX is uninitialized the result will likely be larger than 64-bits, resulting in an overflow exception. Try adding a CQO before the division to sign-extend RAX into RDX.
>
> [来源](https://stackoverflow.com/questions/8858104/64bit-nasm-division-idiv)
>
> 意思是做除法之前，要先**符号扩展**
>
> 参考：[【CSDN】汇编语言符号扩展指令及应用示例](https://blog.csdn.net/ComputerInBook/article/details/90151238)

调试过程中，观察寄存器的值，发现 `idiv ecx` 的时候，`edx = 3`, `eax = 3 ; 就是输入的值`

由于计算机运算的是 `EDX:EAX / ECX`，即 `0x300000003 / 3 = 0x100000001`，已经超过了 `EAX` 范围，所以导致溢出

所以要先初始化高位寄存器，即“符号扩展”——正数就填充高位寄存器为0；负数就填充1

符号扩展指令表

1. **CBW**（Convert Byte to Word）

    AL 扩展到 AX
2. **CWD**（Convert Word to Doubleword）

    AX 扩展到 DX:AX
3. **CDQ**（Convert Doubleword to Quadword）

    EAX 扩展到 EDX:EAX
4. **CQO**（Convert Quadword to ）

    RAX 扩展到 RDX:RDX （64位机器专用）
5. **CWDE**（Convert Word to Doubleword）

    这个 `E(Extension)` 指相对于CWD的扩展，将AX扩展到EAX
6. **CDQE**（Convert Doubleword to Quadword）

    EAX扩展到RAX
7. **MOVZX**

    指令将源操作数的内容复制到目的操作数中，并将该值零扩展至16位或32位。该指令仅适用于**无符号**整数。
    
    因为MOV指令不能将一个较小的操作数复制到一个较大的操作数。
8. **MOVSX**

    指令将源操作数的内容复制到目的操作数中，并将该值符号扩展至16位或32位。该指令仅适用于**有符号**整数。