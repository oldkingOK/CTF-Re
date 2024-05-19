Keypatch
## IDA符号表恢复

[符号表恢复 | Antel0p3's blog](https://antel0p3.github.io/2023/08/12/symbol-restore/)

### IDA flair

flair通过选择的签名文件和程序中的函数进行签名匹配来帮助我们还原库函数  
其中签名文件可以从[sig-database](https://github.com/push0ebp/sig-database)中获取，有制作好的各架构各版本签名文件

> 已经放在了 `D:\Documents\SZPOJP\IDA插件` 下

用`DIE`查出来操作系统、编译器版本（如果查不出来，就每一个都试一次）

![[image-20240430133705457.png]]

找到对应的签名文件，这里是

```
sig-database\ubuntu\libc6\20.04 (focal)\amd64\libc6_2.31-0ubuntu9.4_amd64.sig
```

复制签名文件(sig文件)导入到IDA的对应签名目录下`IDAx.x\sig\pc`

IDA中按住`SHIFT+F5`

右键选择`Apply new signature...`

![[image-20240430133833256.png]]

选择签名文件进行匹配

![[image-20240430133935844.png]]

可以看到很多函数名已经恢复，代码也就好分析多了

### 手动编译sig

[使用IDA flair恢复静态编译去符号的库函数名_ida工具flair70-CSDN博客](https://blog.csdn.net/Breeze_CAT/article/details/103788796)

```shell
sudo apt install g++
find /lib/ -name "*c++*"
```

```log
/lib/x86_64-linux-gnu/libstdc++.so.6
/lib/x86_64-linux-gnu/libmpdec++.so.3
/lib/x86_64-linux-gnu/libstdc++.so.6.0.30
/lib/x86_64-linux-gnu/libmpdec++.so.2.5.1
/lib/gcc/x86_64-linux-gnu/11/libstdc++.so
/lib/gcc/x86_64-linux-gnu/11/libsupc++.a
/lib/gcc/x86_64-linux-gnu/11/libstdc++fs.a
/lib/gcc/x86_64-linux-gnu/11/libstdc++.a
```

看来就是 `libstdc++.a`

```shell
./pelf libc.a test.pat
#如果这句话报错: Unknown relocation type 42 (offset in section=0x16).那么要加一个参数：
./pelf -r42:0:0 libc.a test.pat
#如果有出现别的错误，继续添加这个参数 -r错误号:0:0
```

然后会出现一个 `test.exec` ，这里是冲突信息，同样的签名对应了不同的函数名，在需要的函数名前面加上 `+` 号，然后删掉前几行的注释

```
./sigmake test.pat test.sig
```

### C++翻译

[oldkingOK/demumblida: Use demumble in ida (github.com)](https://github.com/oldkingOK/demumblida)

把形似 `_ZNKSt7__cxx1112basic_stringIcSt11char_traitsIcESaIcEE7compareEPKc` 的函数名翻译成可读的文本，并自动注释

快捷键 `Ctrl Alt D`
### Bindiff

从[Releases · google/bindiff (github.com)](https://github.com/google/bindiff/releases) 下载并安装

用于比较二进制函数的签名等信息

1. 恢复符号表
2. 查看二进制的改动
## 易语言

[fjqisba/E-Decompiler: 用来辅助分析易语言程序的IDA插件 (github.com)](https://github.com/fjqisba/E-Decompiler)

把 E-decompiler 和 esig 放进 `IDA/plugins`

需要patch一下IDA和修改配置文件以支持中文函数名（参考：[IDA7.5支持中文函数命名的办法 - 吾爱破解](https://www.52pojie.cn/thread-1414525-1-1.html)）

**注意：** 只有IDA32会显示易语言插件
### 修改配置文件

`IDA_Pro_7.5\cfg\ida.cfg`

```c
NameChars =
        "$?@"           // asm specific character
        "_0123456789"
        "ABCDEFGHIJKLMNOPQRSTUVWXYZ"
        "abcdefghijklmnopqrstuvwxyz",
        // This would enable common Chinese characters in identifiers:
        // Block_CJK_Unified_Ideographs,
        CURRENT_CULTURE;
```

把 `Block_CJK_Unified_Ideographs` 的注释取消掉即可

### Patch IDA

把 `IDA_Pro_7.5\ida.dll` 扔进 IDA，搜索 `calc_c_cpp_name` 函数

![[image-20240502150648350.png|600]]

然后把 `*v10 = '_'` NOP掉

## 与VSCode联动运行脚本

[ioncodes/idacode: An integration for IDA and VS Code which connects both to easily execute and debug IDAPython scripts. (github.com)](https://github.com/ioncodes/idacode)

复制完之后，需要编辑 `idacode.py`，加载依赖

```shell
# 安装依赖，在ida中输入：
!pip install debugpy tornado
```

然后输入 `!pip --version` 显示 `site-packages` 的路径，我这里是：

`D:\\Program Files\\Python310\\lib\\site-packages`

然后在 `idacode.py` 的最上边加两行代码：

```python
import sys
sys.path.append('c:\\users\\mov\\appdata\\roaming\\python\\python310\\site-packages')
```

## Rust分析

[Release First Release 🎉 · timetravelthree/IDARustDemangler (github.com)](https://github.com/timetravelthree/IDARustDemangler/releases/tag/v0.1.0)

