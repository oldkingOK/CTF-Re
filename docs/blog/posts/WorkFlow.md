---
draft: false
date: 2024-05-07
slug: 逆向工作流
categories:
  - Reverse
tags:
  - Reverse
---

# 工作流

1. `exp.py` 用于启动程序和自动输入文本
2. `ida_.py` 用于执行ida脚本

在 `ida_.py` 前面加上：

```python
import site; site.addsitedir('D:\\Program Files\\IDA_Pro_v8.3_Portable\\python\\3')
```

就可以有代码提示了