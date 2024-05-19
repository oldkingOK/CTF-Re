# Frida

看看示例脚本

[Frida basics - Frida HandBook (learnfrida.info)](https://learnfrida.info/basic_usage/)

[frida/frida-tools: Frida CLI tools (github.com)](https://github.com/frida/frida-tools)

```shell
pip install frida-tools # CLI tools
pip install frida       # Python bindings
npm install frida       # Node.js bindings
```

```js
Interceptor.attach(ptr("0x401AA5"), {
    onEnter: function(args) {
        console.log("call ", this.context.rax);
    }
});
```

```js
console.log(hexdump(ptr(0x401123), {
            offset: 0,
            length: 8,
            header: true,
            ansi: false
        }));
```

