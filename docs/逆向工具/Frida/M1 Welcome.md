# M1 Welcome

[Welcome | Frida • A world-class dynamic instrumentation toolkit](https://frida.re/docs/home/)

## 什么是 Frida？

- native app的油猴脚本
- 动态代码工具集，把js或者自己的库插入native apps

## 为什么需要 Frida

1. 跟踪app所使用的API
2. 构建了一个app，用户在使用的时候有bug，但是日志不够，你又发了个全是 `printf` 打印日志的debug版本——使用Frida可以几行完成，而且兼容性好
3. 操纵程序中函数的执行，不然就得搭建测试环境
4. 内部应用程序可以使用一些黑盒测试，而无需在生产代码中加入仅用于特殊测试的逻辑

## 为什么一个Python的API，使用JS的调试逻辑？

Frida 的内核是用 C 语言编写的，它将 [QuickJS](https://bellard.org/quickjs/) 注入目标进程，在目标进程中， JS 可以完全访问内存、挂钩函数，甚至调用进程内的本地函数。Python应用程序与目标进程内运行的 JavaScript 之间有一个双向通信通道。

使用 Python 和 JS 可以快速开发无风险的 API。Frida 可以帮助您轻松捕捉 JS 中的错误，并提供异常而不是崩溃。

也可以用其他的语言，甚至直接用C

