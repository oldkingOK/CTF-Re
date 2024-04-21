# 1. Base64自定义表

简单学习下Base64

[文件](./assets/bin/simple_RE.exe){:download="simple_RE.exe"}

```c
v11 += 4;
v13 = input[v12];
*(v11 - 4) = aQvejafhmuyjbac[v13 >> 2];
v14 = input[v12 + 1];
*(v11 - 3) = aQvejafhmuyjbac[(v14 >> 4) | (16 * v13) & 0x30];
v15 = input[v12 + 2];
v12 += 3i64;
*(v11 - 2) = aQvejafhmuyjbac[(v15 >> 6) | (4 * v14) & 0x3C];
*(v11 - 1) = aQvejafhmuyjbac[v15 & 0x3F];
```

这里就是base64加密，其中 `aQvejafhmuyjbac` 是自定义的加密表

乘法其实就是位操作

```c
v11 += 4;
v13 = input[v12];
*(v11 - 4) = aQvejafhmuyjbac[v13 >> 2];
v14 = input[v12 + 1];
*(v11 - 3) = aQvejafhmuyjbac[(v14 >> 4) | (v13 << 4) & 0b00110000];
v15 = input[v12 + 2];
v12 += 3i64;
*(v11 - 2) = aQvejafhmuyjbac[(v15 >> 6) | (v14 << 2) & 0b00111100];
*(v11 - 1) = aQvejafhmuyjbac[v15 & 0x3F];
```

## 代码

```python
import base64

enc = '5Mc58bPHLiAx7J8ocJIlaVUxaJvMcoYMaoPMaOfg15c475tscHfM/8=='
table = 'qvEJAfHmUYjBac+u8Ph5n9Od17FrICL/X0gVtM4Qk6T2z3wNSsyoebilxWKGZpRD'
real_table = 'ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/'
print(len(table))
print(len(real_table))

result = ''
for c in enc:
    if c == '=':
        result += '='
    else:
        result += real_table[table.index(c)]

print(base64.b64decode(result))
# 这里换成
# print(base64.b64decode(enc.translate(str.maketrans(table, real_table))))
# 也可以
```

# 2. 模的逆运算

```python
flag = 'xxxxxxxxxxxxxxxxxxxxx'
s = 'wesyvbniazxchjko1973652048@$+-&*<>'
result = ''
for i in range(len(flag)):
    s1 = ord(flag[i])//17 # 最大的ascii码 // 17 = 7，s1的范围是0-7
    s2 = ord(flag[i])%17 # s2 的范围0 - 16
    result += s[(s1+i)%34]+s[-(s2+i+1)%34] # 字符拼接
print(result)
# result = 'v0b9n1nkajz@j0c4jjo3oi1h1i937b395i5y5e0e$i'

#题目为以上部分

enc_flag = 'v0b9n1nkajz@j0c4jjo3oi1h1i937b395i5y5e0e$i'
result = ''
for i in range(0, len(enc_flag), 2):
    i_origin = i // 2
    s1 = (s.index(enc_flag[i]) - i_origin) % 34
    s2 = (-(s.index(enc_flag[i + 1]) - 34) - i_origin - 1) % 34
    result += chr(s1 * 17 + s2)

print(result)
```

# 3. Wordy

使用ida看不出什么东西，动态调试一下

[文件](./assets/bin/wordy){:download="wordy"}

把 `ida目录/dbgsrv/linux_server64` 扔进wsl，然后运行

- ida里选择Debugger-Remote Linux Debugger，
- Debugger-Process Options，填上ip:port
- Debugger-Start Process即可

调试发现，jmp命令非常奇怪，导致无法一次性反汇编，只能边调试边反汇编

这属于**花指令**的一种

但这题比较简单，一直在 `putchar`，所以一边搜索 `0xBF`，一边按C反汇编

在程序较末尾的地方就可以找到flag

*看了下其他师傅的wp*

运行idapython脚本，即可一次性去除掉花指令

```python
start = 0x55BE0E39D136
end = 0x55BE0E39F100
for i in range(start, end):
    if get_wide_byte(i) == 0xEB and get_wide_byte(i + 1) == 0xFF:
        patch_byte(i, 0x90) # nop
```