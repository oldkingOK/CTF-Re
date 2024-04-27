# WebAssembly逆向

相当于直接运行字节码

```asm
...
(local $var86 i64)
(local $var87 i64)
(local $var88 i64)
(local $var89 i64)
(local $var90 i64)
(local $var91 i64)
(local $var92 i64)
local.get $var0
i32.const 36
i32.add
local.tee $var8
...
```

嵌入在网页中的字节码，用F12就可以看到 `*.wasm`
### 到达指定分数给flag

用 [Cetus](https://github.com/Qwokka/Cetus) 扫内存，类似于 CheatEngine（搜索 `cheatengine for wasm` 搜到的 ）

针对 Wasm 的搜内存工具

![[image-20240427101634902.png|300]]

### 需要分析代码逻辑

[Webassembly逆向手法 - 先知社区 (aliyun.com)](https://xz.aliyun.com/t/13747)

wat是文本文件，wasm是二进制文件，浏览器可以直接看 wasm 是因为自带 wasm2wat，即[WebAssembly/wabt: The WebAssembly Binary Toolkit (github.com)](https://github.com/WebAssembly/wabt) 中的程序

- [**wat2wasm**](https://webassembly.github.io/wabt/doc/wat2wasm.1.html): translate from [WebAssembly text format](https://webassembly.github.io/spec/core/text/index.html) to the [WebAssembly binary format](https://webassembly.github.io/spec/core/binary/index.html)
- [**wasm2wat**](https://webassembly.github.io/wabt/doc/wasm2wat.1.html): the inverse of wat2wasm, translate from the binary format back to the text format (also known as a .wat)
- [**wasm-objdump**](https://webassembly.github.io/wabt/doc/wasm-objdump.1.html): print information about a wasm binary. Similiar to objdump.
- [**wasm-interp**](https://webassembly.github.io/wabt/doc/wasm-interp.1.html): decode and run a WebAssembly binary file using a stack-based interpreter
- [**wasm-decompile**](https://webassembly.github.io/wabt/doc/wasm-decompile.1.html): decompile a wasm binary into readable C-like syntax.
- [**wat-desugar**](https://webassembly.github.io/wabt/doc/wat-desugar.1.html): parse .wat text form as supported by the spec interpreter (s-expressions, flat syntax, or mixed) and print "canonical" flat format
- [**wasm2c**](https://webassembly.github.io/wabt/doc/wasm2c.1.html): convert a WebAssembly binary file to a C source and header
- [**wasm-strip**](https://webassembly.github.io/wabt/doc/wasm-strip.1.html): remove sections of a WebAssembly binary file
- [**wasm-validate**](https://webassembly.github.io/wabt/doc/wasm-validate.1.html): validate a file in the WebAssembly binary format
- [**wast2json**](https://webassembly.github.io/wabt/doc/wast2json.1.html): convert a file in the wasm spec test format to a JSON file and associated wasm binary files
- [**wasm-stats**](https://webassembly.github.io/wabt/doc/wasm-stats.1.html): output stats for a module
- [**spectest-interp**](https://webassembly.github.io/wabt/doc/spectest-interp.1.html): read a Spectest JSON file, and run its tests in the interpreter

#### 逆向

1. 用 wasm2c，变成c代码之后，再gcc编译，扔进ida分析
2. 把wasm拖进Jeb里分析
3. 用浏览器自带的动态调试