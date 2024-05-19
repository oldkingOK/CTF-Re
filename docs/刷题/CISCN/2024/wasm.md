# wasm

window下不能使用wasm2c

在linux下可以：

```shell
sudo apt install wabt
wasm2c vuln.c
```

是把 name=abc 输入进去当作输入

![[image-20240519121544343.png]]

这样放进 `flag.txt` ，在 `file.txt` 会出现截断

`flag{aaaaaaaaaaaaaaaaaaaaaaaaaa}`

长度估计就是这个了

尝试导入wasm到浏览器

贼麻烦，放弃

## 使用strace大法

安装 wasmtime 之后，`strace -o strace.log wasmtime --dir=. vuln.wasm`

