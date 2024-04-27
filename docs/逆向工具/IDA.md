# IDA的糕集使用寄巧

## 快捷键

从官网扒下来 p了一下的图

![图图](./assets/IDA.png)

## idapython

[官方文档](https://hex-rays.com/products/ida/support/idapython_docs/)

## 快照、标记、结构体

[【看雪论坛】IDA技巧——结构体](https://bbs.kanxue.com/thread-266419.htm)

### 快照

- **Ctrl_Shift_W** 保存快照
- **Ctrl_Shift_T** 还原快照

### 标记

- **Alt_M** 打标机
- **Ctrl_M** 传送标记

### 结构体

1. **Shift_F1** 本地属性窗口
2. 按 **Insert** 以C语言语法定义结构体
3. **Ctrl_F5** 搜索刚定义的结构体，双击导入结构体
4. 右键变量 -> `Convert to struct...`

### 结构体数组

1. **Alt_Q** 把第一个元素定义为结构体
2. **Shift_\*** 定义数组

### 扒数据

**Shift_E** 提取二进制数据
在C代码界面 **R** ascii码转换成字符