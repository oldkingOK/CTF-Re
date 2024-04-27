# 固件逆向

一般会给像下面一样的固件二进制信息——Intel hex文件

> Intel Hex文件是一种编码格式，用于将二进制信息转换为文本记录，这些记录可以通过串行通信或其他接口传输到微控制器等设备上。

```hex
:020000040000FA
:100000000C9434000C9446000C9446000C9446006A
:100010000C9446000C9446000C9446000C94460048
:100020000C9446000C9446000C9446000C944F002F
:100030000C9446000C9446000C9446000C94460028
:100040000C9446000C9446000C9446000C94460018
:100050000C9446000C9446000C9446000C94460008
...
:107FA0009093C60008958091C00087FFFCCF809118
:107FB000C00084FD01C0A8958091C6000895E0E648
:107FC000F0E098E1908380830895EDDF803219F02E
:107FD00088E0F5DFFFCF84E1DECF1F93182FE3DFCA
:107FE0001150E9F7F2DF1F91089580E0E8DFEE27F6
:047FF000FF270994CA
:027FFE00040479
:00000001FF
```

DIE是无法查到的，直接拖进IDA可以看到是 `Intel Hex`，IDA可以反汇编大多数的芯片架构，所以难点在于识别出是什么架构

### 出现重复的 `0C94` 

`0C94` 是**AVR**架构的跳转指令

IDA中对应 `Atmel AVR`

### 如果题目提示是stm32

就是 ARMv7-M（参考：[STM32——IDA反编译 Hex/Bin文件成C代码](https://www.cnblogs.com/panda-w/p/11548121.html)）

![[image-20240427094741401.png]]

### 也有可能是 Micro:bit

扔进官方的网站（参考：[Galaxians CTF | LEDs (shiltemann.github.io)](https://shiltemann.github.io/CTF-writeups-public/writeups/HackyEaster_2022/level4-6-leds.html)）

- [图形化编程 Microsoft MakeCode for micro:bit (microbit.org)](https://makecode.microbit.org/)
- [MicroPython编程 micro:bit Python Editor (microbit.org)](https://python.microbit.org/v/3)

两个都可以试试
# 另外

这里有一个单片机模拟器，看起来很不错

[Wokwi - Online ESP32, STM32, Arduino Simulator](https://wokwi.com/)