# 双游戏

> 顺便学下CE

[文件](./assets/bin/doublegame.exe){:download="doublegame.exe"}

[原题地址【NSSCTF】](https://www.nssctf.cn/problem/3692)

# 概览

两种方法

1. [【传送门】](#1-ce) 静态分析+[Cheat Engine](https://www.cheatengine.org/downloads.php)（非常规解法）
2. [【传送门】](#2) 纯静态分析（常规解法）

# 1. CE

## Part.0 静态分析

尝试直接扔进ida，然后*从头开始*分析

> *来自未来的我：这里其实方法不对，直接搜字符串就行*

有点难，静态分析不出什么结果

只找到了一些线索：

- `the first game tell the score`
- `the score is saving cat's key!`
- `You win.\nThe flag is HZCTF{md5(path)+score}`

## Part.1 Snake

第一个是一个贪吃蛇游戏，CE附加到进程中

直接搜分数，搜两三次就能找到地址

然后右键->**将选中的地址添加到地址列表**->右键地址列表中的地址->**找出是什么访问了这个地址（F5）**

然后吃果然后自杀，会出几个条目

```asm
7FF79350247C - 83 3D 4D080100 64 - cmp dword ptr [7FF793512CD0],64
7FF7935024B4 - 39 05 16080100  - cmp [7FF793512CD0],eax
7FF7935024D0 - 8B 05 FA070100  - mov eax,[7FF793512CD0]
```

逐个点击——显示反汇编程序，在第一个条目旁边找到一个比较分数的指令：

```asm
doublegame.exe+1247C - cmp dword ptr [doublegame.exe+22CD0],64
```

和

```asm
doublegame.exe+1248A - cmp [doublegame.exe+22CD0],00CC07C9 ; 13371337
```

然后分别尝试将64和13371337放进数值，尝试后者之后就会跳转到一个迷宫

## Part.2 Maze

第二个是一个迷宫游戏，需要输入wasd来“拯救小猫”

```log
000000000000000000000
0 0 0 0     0     0 0
0 0 0 00000 00000 0 0
0 0               0 0
0 000 000 0 000 0 0 0
0 0     0 0 0   0 0 0
0 0 0 00000 000 000 0
0 0 0     0   0 0
0 000 0 0 000 0 0 0 0
0     0 0 0 0 0 0 0 0
0 00000 000 000 0 0 0
0     0       0   0 0
000 0 0 0 000 0 0 0 0
0 0 0 0 0 0 * 0 0 0 0
0 0000000 0 000 00000
@   0   0 0         0
0 0 0 0 0 00000000000
0 0 0 0             0
000 0 00000 0 000 000
0         0 0   0   0
000000000000000000000
```

这里的@就是人，*是小猫，路径是 `dddssssddwwwwddssddwwwwwwddddssa`

```log
do you know the key to open the door
```

从静态分析得知，key就是分数，`the score is saving cat's key!` 即13371337

```log
oh,the door open!
恭喜你完成了第一个任务!请从头用最快的方式把猫带出去
```

然后又是走迷宫

完整路径：`dddssssddwwwwddssddwwwwwwddddssaassddddwwwwddwwwwddd`

```log
You win.
The flag is HZCTF{md5(path)+score}
```

计算即得flag，注意这里的md5需要全小写

# 2. 纯静态分析

*看师傅的wp学到的，一开始没想到*

搜索字符串 `Game over`，双击引用

```c
if ( dword_140022CD0 > 13371337 ) sub_14001136B();
```

这里的 `sub_14001136B` 点进去，是打印迷宫的代码

汇编代码是

```asm
.text:0000000140012494                 jle     short loc_1400124A7
.text:0000000140012496                 call    sub_14001136B
```

直接`<Ctrl-N>` nop掉jle就行

然后就是走迷宫，略