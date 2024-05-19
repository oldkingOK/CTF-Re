# 【CISCN2021华南】ezre

ELF64
    操作系统: Ubuntu Linux(20.04,ABI: 3.2.0)[AMD64, 64 位, EXEC]
    编译器: GCC(9.3.0)
    语言: C/C++

[CISCN 2021华南 ezre | NSSCTF](https://www.nssctf.cn/problem/2457)

[ptrace函数深入分析 - 黑箱 - 博客园 (cnblogs.com)](https://www.cnblogs.com/heixiang/p/10988992.html)

ps：右键可以直接自动生成结构体

![[image-20240515115626952.png|500]]



[威力巨大的系统调用——ptrace - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/653385264)

神奇，当`v4=0`的时候，是子进程，`v4!=0` 就是父进程

> wait ()会**暂时停止目前进程的执行**, 直到有信号来到或子进程结束

## 经常调着调着程序停了

![[image-20240515194216273.png]]

在开头定了个闹钟，改大一点就行

是一个虚拟机

| 地址         | v8  | 作用        | 对应  |
| ---------- | --- | --------- | --- |
| 401780h    | 3   | 未知        |     |
| qword0 = 0 | 0   | 跳转base到q2 | nop |
#### case 2 和 case 4 是一个相反的操作

2是：ret
跳转到 `base = v13[...]` `v12 += 8`

```asm
pop loc1
jmp loc1
```

4是：call
`v13[...] = base->q0` ，
`__iptr++` 
`v12 -= 8`
`base = base-> q1`

```asm
push loc1
jmp loc2
```

#### 当 pfunc0 = 0 或 v8 = 0

都像是jmp

```asm
pfunc0 == 0 -> jmp base->q1
v8 == 0 -> jmp base->q0
v8 == 1 -> jmp base->q1
```

### 改改类型

```
ptrace(PTRACE_GETREGS, a1, 0LL, v10);
```

`PTRACE_PEEKDATA` 

这里的v10默认是`char v10[128]` 128个8位=16个64位，应该是 `__int64 v10[16]`

通过chatgpt，给出了v10的结构体：user_regs_struct

然后查源码，得到下面结构体，导入IDA（不能重名，所以用ok）

```
struct ok_regs_struct
{
   unsigned long long int r15;
   unsigned long long int r14;
   unsigned long long int r13;
   unsigned long long int r12;
   unsigned long long int rbp;
   unsigned long long int rbx;
   unsigned long long int r11;
   unsigned long long int r10;
   unsigned long long int r9;
   unsigned long long int r8;
   unsigned long long int rax;
   unsigned long long int rcx;
   unsigned long long int rdx;
   unsigned long long int rsi;
   unsigned long long int rdi;
   unsigned long long int orig_rax;
   unsigned long long int rip;
   unsigned long long int cs;
   unsigned long long int eflags;
   unsigned long long int rsp;
   unsigned long long int ss;
   unsigned long long int fs_base;
   unsigned long long int gs_base;
   unsigned long long int ds;
   unsigned long long int es;
   unsigned long long int fs;
   unsigned long long int gs;
};
```

很多东西就很清晰了，比如

![[image-20240515215407193.png]]

这里就是让子进程跳转到 `base->q1` ，然后把 `base->q3` push到子进程的栈中，就是 `call` 

第一次3就是 `puts` ，正确

这里的 `base->q3` 是 `4012CB` 

![[image-20240515215746028.png]]



![[image-20240515220135037.png]]

CC 就是 `int 3` 用于触发异常的

## 大概知道了，有以下难点：

1. 控制流平坦化——由父进程统一进行代码流程分发
2. 虚拟机分析——jmp、call、push、pop均由父进程操作
3. 多线程交互——子进程的部分栈操作，用父进程模拟

目前想到的方案是用frida hook每一个流程，然后把程序的流程用正常的汇编表示

# Frida学习

官方仓库：[frida/frida: Clone this repo to build Frida (github.com)](https://github.com/frida/frida)

官方文档：[Quick-start guide | Frida • A world-class dynamic instrumentation toolkit](https://frida.re/docs/quickstart/)

```shell
pip install frida-tools # CLI tools
pip install frida       # Python bindings
npm install frida       # Node.js bindings
```

[Release Frida 16.2.1 · frida/frida (github.com)](https://github.com/frida/frida/releases/tag/16.2.1)

下载 frida-server-16.2.1-linux-x86_64.xz

如何hook函数：[Functions | Frida • A world-class dynamic instrumentation toolkit](https://frida.re/docs/functions/)

```
-f TARGET # 运行并附加
```

[frida 常用的一些demo 打印native 函数的堆栈 参数,返回值 等_frida 打印调用堆栈-CSDN博客](https://blog.csdn.net/qq_36535153/article/details/120282554)

突然发现，父进程的堆栈操作和子进程没关系，只有 `v10.rip` 的作用最大，只用分析 `call` 和 `jmp` 就行

```shell
frida -f ezre -l frida_hi.js -o frida_hi.log
```

```js
// frida插桩，每执行到 0x401AA5 一次就输出一次hi
Interceptor.attach(ptr("0x401AA5"), {
    onEnter: function(args) {
        console.log("call ", this.context.rax);
    }
});
```

```log
❯ frida -f ezre -l frida_hi.js -o frida_hi.log
     ____
    / _  |   Frida 16.2.1 - A world-class dynamic instrumentation toolkit
   | (_| |
    > _  |   Commands:
   /_/ |_|       help      -> Displays the help system
   . . . .       object?   -> Display information about 'object'
   . . . .       exit/quit -> Exit
   . . . .
   . . . .   More info at https://frida.re/docs/home/
   . . . .
   . . . .   Connected to Local System (id=local)
Spawned `ezre`. Resuming main thread!                                   
Input your flag:
call  0x4010c0
call  0x4010f0
[Local::ezre ]-> Error!
call  0x4010c0
Process terminated
[Local::ezre ]->
WARNING: your terminal doesn't support cursor position requests (CPR).

Thank you for using Frida!
```

看了下， `0x4010c0` 是 `puts` ， `0x4010f0` 是 `read` ，正确的

程序会由于没有read到东西而死循环+停下来，所以这里看看能不能hook read函数

不太好hook，因为read函数执行逻辑发生在子进程，只能patch了

- [使用frida来spawn Fork 的子进程 - 『软件调试区』 - 吾爱破解 - LCG - LSG |安卓破解|病毒分析|www.52pojie.cn](https://www.52pojie.cn/thread-1843175-1-1.html)
- [frida-python/examples/child_gating.py at main · frida/frida-python (github.com)](https://github.com/frida/frida-python/blob/main/examples/child_gating.py)
- [FRIDA-API使用篇：rpc、Process、Module、Memory使用方法及示例 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/101401252)
- [Frida basics - Frida HandBook (learnfrida.info)](https://learnfrida.info/basic_usage/)

找到了hook子进程的资料，成功hook到read

```log
on read( 0x0 , 0x7ffed821f9a0 , 0x2b )
```

长度是0x2b - 43，0x0应该是标准输入流

```
buf_ptr = ptr(args[1]); // 往 read 的 buf 中写入数据
buf_ptr.writeUtf8String("aaaaaaaa");
```

打印指定地址的内存状况

```js
console.log(hexdump(ptr(0x401123), {
            offset: 0,
            length: 8,
            header: true,
            ansi: false
        }));
```

发现运行的时候，这部分代码被修改了

![[image-20240516100739184.png]]

![[image-20240516100824144.png]]

直接把修改后的bytes patch之后，汇编是：

![[image-20240516100904870.png]]

直接被改成jmp了，得想办法直接ret

## 还有一个办法

在call read 之前hook一下，拿到日志，然后正常运行程序，理论上来说会卡在read，这时候再hook一下，就可以拿到之后的日志了

```shell
frida -l frida_hi.js -o hook_part1.log
./ezre
```

```shell
❯ frida -n ezre
     ____
    / _  |   Frida 16.2.1 - A world-class dynamic instrumentation toolkit
   | (_| |
    > _  |   Commands:
   /_/ |_|       help      -> Displays the help system
   . . . .       object?   -> Display information about 'object'
   . . . .       exit/quit -> Exit
   . . . .
   . . . .   More info at https://frida.re/docs/home/
   . . . .
   . . . .   Connected to Local System (id=local)
Failed to spawn: ambiguous name; it matches: ezre (pid: 21768), ezre (pid: 21769)
```

这样可以看两个pid，第二个是子进程的，这里hook第一个

拿到了，其实关键部分就是 part2，第一部分只调用了 call puts 和 call read

## 自动化跳转代码

```python
import ida_kernwin

# 跳转到指定地址
ida_kernwin.jumpto(0x40130d)
```

## 正向分析有点难

从 `Error` 反推

```log
jmp  0x4013ec
jmp  0x401458
jmp  0x401486
jmp  0x4014e9
jmp  0x401266
jmp  0x401410   // cmp
jmp  0x4012b2   // Error
call  0x4010c0  // puts
```

#### 401410

```asm
.text:0000000000401410
.text:0000000000401410             loc_401410:
.text:0000000000401410 8B 45 AC    mov     eax, [rbp+var_54]
.text:0000000000401413 48 98       cdqe                    ; EAX -> RAX (with sign)
.text:0000000000401415 48 8D 15 64 lea     rdx, byte_405880 ; Load Effective Address
.text:0000000000401415 44 00 00
.text:000000000040141C 0F B6 14 10 movzx   edx, byte ptr [rax+rdx] ; Move with Zero-Extend
.text:0000000000401420 8B 45 AC    mov     eax, [rbp+var_54]
.text:0000000000401423 48 98       cdqe                    ; EAX -> RAX (with sign)
.text:0000000000401425 0F B6 44 05 movzx   eax, [rbp+rax+var_40] ; Move with Zero-Extend
.text:0000000000401425 C0
.text:000000000040142A 38 C2       cmp     dl, al          ; Compare Two Operands
.text:000000000040142C CC          int     3 
```

很像了，这里的 `cmp dl, al` 就是核心比较汇编代码，直接hook然后打印

Python里的spawn可以设置stdin为`inherit` ，就可以继承 sys.stdin

```python
pid = self._device.spawn(argv, env=env, stdio="inherit")
```

这里的dl是恒定的，和dx的值一样，是1f，所以密文的第一个字符是 `1f`

```js
Interceptor.attach(ptr("0x401AA5"), {
    onEnter: function(args) {
        console.log("call ", this.context.rax);
    }
});

/*
.text:0000000000401903             loc_401903:
.text:0000000000401903 48 8D 95 80 lea     rdx, [rbp+var_280] ; 从子进程获取寄存器
.text:0000000000401903 FD FF FF
.text:000000000040190A 8B 85 4C FD mov     eax, [rbp+var_2B4]
.text:000000000040190A FF FF
*/
Interceptor.attach(ptr("0x40190A"), {
    onEnter: function(args) {
        base_v10 = ptr(this.context.rdx);
    }
});
var base_v10 = ptr(0);

// ptrace(PTRACE_GETREGS, a1, 0LL, &v10);
Interceptor.attach(ptr("0x401929"), {
    onEnter: function(args) {
        regs = {
            r15: base_v10.add(0x00).readULong(),
            r14: base_v10.add(0x08).readULong(),
            r13: base_v10.add(0x10).readULong(),
            r12: base_v10.add(0x18).readULong(),
            rbp: base_v10.add(0x20).readULong(),
            rbx: base_v10.add(0x28).readULong(),
            r11: base_v10.add(0x30).readULong(),
            r10: base_v10.add(0x38).readULong(),
            r9: base_v10.add(0x40).readULong(),
            r8: base_v10.add(0x48).readULong(),
            rax: base_v10.add(0x50).readULong(),
            rcx: base_v10.add(0x58).readULong(),
            rdx: base_v10.add(0x60).readULong(),
            rsi: base_v10.add(0x68).readULong(),
            rdi: base_v10.add(0x70).readULong(),
            orig_ra: base_v10.add(0x78).readULong(),
            rip: base_v10.add(0x80).readULong(),
            cs: base_v10.add(0x88).readULong(),
            eflags: base_v10.add(0x90).readULong(),
            rsp: base_v10.add(0x98).readULong(),
            ss: base_v10.add(0xA0).readULong(),
            fs_base: base_v10.add(0xA8).readULong(),
            gs_base: base_v10.add(0xB0).readULong(),
            ds: base_v10.add(0xB8).readULong(),
            es: base_v10.add(0xC0).readULong(),
            fs: base_v10.add(0xC8).readULong(),
            gs: base_v10.add(0xD0).readULong(),
        }
    } 
})
var regs = {
    r15: 0,
    r14: 0,
    r13: 0,
    r12: 0,
    rbp: 0,
    rbx: 0,
    r11: 0,
    r10: 0,
    r9: 0,
    r8: 0,
    rax: 0,
    rcx: 0,
    rdx: 0,
    rsi: 0,
    rdi: 0,
    orig_ra: 0,
    rip: 0,
    cs: 0,
    eflags: 0,
    rsp: 0,
    ss: 0,
    fs_base: 0,
    gs_base: 0,
    ds: 0,
    es: 0,
    fs: 0,
    gs: 0,
};

// /**
//  * v10.rip = base_qword->q3;
//  */
// Interceptor.attach(ptr("0x401C7D"), {
//     onEnter: function(args) {
//         console.log("jmp ", this.context.rax);
//     }
// });

Interceptor.attach(ptr("0x401930"), {
    onEnter: function(args) {
        if (this.context.rdx == '0x40142d') {
            console.log("找到cmp dl, al在", this.context.rdx);
            console.log("此时 ax = ", regs.rax.toString(16), " dx = ", regs.rdx.toString(16));
            console.log(JSON.stringify(regs, null, 2));
        }
        // console.log("sub p rip = ", this.context.rdx);
    }
});
```


```asm
.text:0000000000401410
.text:0000000000401410             loc_401410:
.text:0000000000401410 8B 45 AC    mov     eax, [rbp+var_54]
.text:0000000000401413 48 98       cdqe                    ; EAX -> RAX (with sign)
.text:0000000000401415 48 8D 15 64 lea     rdx, byte_405880 ; Load Effective Address
.text:0000000000401415 44 00 00
.text:000000000040141C 0F B6 14 10 movzx   edx, byte ptr [rax+rdx] ; Move with Zero-Extend
.text:0000000000401420 8B 45 AC    mov     eax, [rbp+var_54]
.text:0000000000401423 48 98       cdqe                    ; EAX -> RAX (with sign)
.text:0000000000401425 0F B6 44 05 movzx   eax, [rbp+rax+var_40] ; Move with Zero-Extend
.text:0000000000401425 C0
.text:000000000040142A 38 C2       cmp     dl, al          ; Compare Two Operands
.text:000000000040142C CC          int     3
```

这里的 `mov  eax, [rbp+var_54]` `[rbp+rax+var_40]` 就是加密后的数据所在，

`var_54` 是下标， `var_40` 是第一个元素

## 先把所有密文dump出来吧

```c
unsigned char ida_chars[] =
{
  0x1F, 0x18, 0x0F, 0xA9, 0xE4, 0x3D, 0x7A, 0x3A, 0x4D, 0x17, 
  0xD1, 0x18, 0x10, 0x68, 0x1C, 0x4D, 0x79, 0x19, 0x03, 0x41, 
  0x4B, 0x60, 0x1C, 0x67, 0x5C, 0x38, 0x23, 0x1F, 0x53, 0x00, 
  0x14, 0x4D, 0x19, 0x7A, 0x7B, 0x5D, 0x71, 0x42, 0x3E, 0x37, 
  0x9C, 0x8E, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0xDD, 0xCC, 
  0xBB, 0xAA, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 
  0x00, 0x00, 0x00, 0x00
};
```

## 然后看看是从哪里写入加密后的flag的

![[image-20240516123649169.png]]

只有 0x4014cd 程序块是往 rbp+rax+var_40 写东西的，所以hook一下这里（地址是下一个程序块的首地址，即 0x4014e9 ），读取出下标和内容

![[image-20240516124223581.png]]

是 rax 和 dl

没事了， eax << 2 就是为了访问 qword 数组

大概能看出规律，是把大于 0xF 的数先加密+压缩到 0xF 以下，然后再 加密+解压到 0xF 以上

## 太难分析了

frida侧信道爆破

因为大概看了下，是对字符进行单个加密，那就一位一位的爆破

爆破不了，遍历第一位 printable 都没找到

## 漏分析了

![[image-20240516161312364.png]]

这里也会导致 jmp

[EFLAGS寄存器（标志寄存器） - Reverse-xiaoyu - 博客园 (cnblogs.com)](https://www.cnblogs.com/Reverse-xiaoyu/p/11397584.html)

![[image-20240516170712423.png]]

## Dump是正常的

就是如果输入不够0x29位，就会用到未初始化的内存，导致结果随机

## 思路

1. 用idapython从IDA中导出所有子程序程序节点的首地址，做成 `地址-汇编` 的形式
2. hook所有节点，在关键节点打印一些消息，然后执行一次程序

## 有个神奇的加密

输入0123456789abcdefghijklmnopqrstuvwxyzABCDE

每赋值一次flag，就从map中拿一个数据

## 是

根据 `var_44` 来决定下一次分发：

```asm
.text:000000000040130D             loc_40130D:
.text:000000000040130D 8B 45 B0    mov     eax, [rbp+var_50] ; 从qword选到的0
.text:0000000000401310 48 8D 55 A8 lea     rdx, [rbp+var_58] ; [rbp+var_58]是-1
.text:0000000000401314 8B 4D BC    mov     ecx, [rbp+var_44] ; 从qword选到的1
.text:0000000000401317 89 CE       mov     esi, ecx
.text:0000000000401319 89 C7       mov     edi, eax
.text:000000000040131B CC          int     3               ; Trap to Debugger
```

```asm
.text:0000000000401442 55          push    rbp
.text:0000000000401443 48 89 E5    mov     rbp, rsp
.text:0000000000401446 89 7D EC    mov     [rbp+var_14], edi
.text:0000000000401449 89 75 E8    mov     [rbp-18h], esi ; 主要看这个
.text:000000000040144C 48 89 55 E0 mov     [rbp+var_20], rdx
.text:0000000000401450 81 7D EC E7 cmp     [rbp+var_14], 3E7h ; Compare Two Operands; 03 E7 是在 qword 的末端，这里是判断是否结尾

.text:000000000040126B 48 8B 45 E0 mov     rax, [rbp+var_20]
.text:000000000040126F 8B 00       mov     eax, [rax]; eax = -1

.text:0000000000401434 48 8B 45 E0 mov     rax, [rbp+var_20]
.text:0000000000401438 8B 00       mov     eax, [rax]; eax = -1
.text:000000000040143A 83 F8 06    cmp     eax, 6

.text:0000000000401330 48 8B 45 E0 mov     rax, [rbp+var_20]
.text:0000000000401334 8B 00       mov     eax, [rax] ;eax = -1
.text:0000000000401336 8B 55 EC    mov     edx, [rbp+var_14] ; = 3E7h
.text:0000000000401339 89 C1       mov     ecx, eax  ; -1 FFFFFFFF
.text:000000000040133B D3 EA       shr     edx, cl ;0        ; Shift Logical Right
.text:000000000040133D 89 D0       mov     eax, edx 
.text:000000000040133F 83 E0 01    and     eax, 1          ; Logical AND
.text:0000000000401342 85 C0       test    eax, eax        ; Logical Compare

.text:0000000000401296 48 8B 45 E0 mov     rax, [rbp+var_20]
.text:000000000040129A 8B 00       mov     eax, [rax] ; -1
.text:000000000040129C 89 45 FC    mov     dword ptr [rbp+var_8+4], eax ; -1

.text:00000000004013E3 8B 45 FC    mov     eax, dword ptr [rbp+var_8+4]

.text:0000000000401361 5D          pop     rbp
```

```
.text:0000000000401359 89 45 B8    mov     [rbp+var_48], eax
.text:000000000040135C 83 7D B8 08 cmp     [rbp+var_48], 8 ; Compare Two Operands
```

0

## 发现

```asm
jmp 0x40126b  // 
            mov     rax, [rbp+var_20]
            mov     eax, [rax]
```

在 `var_44 = 0 , var_58 = 6` 的时候，会直接跳到 

```asm
jmp 0x4012c2  // 
            movzx   eax, [rbp+var_15] ; Move with Zero-Extend
            test    al, al          ; Logical Compare
```

然后又看懂了， `var_15` 其实就是 `var_44 + 3` 上面调用的（==关键==，因为查看交叉引用是看不到 `var_15` 的“写”过程的）