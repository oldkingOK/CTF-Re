# Python工具和包

## 解包PyInstaller

[PyInstxtractor下载](./assets/bin/pyinstxtractor.py){:download="pyinstxtractor.py"} —— [github](https://github.com/extremecoders-re/pyinstxtractor)

[PyCdc下载](./assets/bin/pycdc.exe){:download="pycdc.exe} —— [github](https://github.com/zrax/pycdc)

使用方法

```
python pyinstxtractor.py Py.exe
.\pycdc.exe .\PyLu.pyc > PyLu.py
```

## 常用函数

```python
n = 123
hex(n) # 0x7b
hex(n)[2:] # 7b
bin(n) # 0b1111011
chr(n) # {
ord('{') # 123
```
## Crypto

```
pip install pycryptodome z3-solver
```

### bytes相关

```python
from Crypto.Util.number import bytes_to_long, long_to_bytes
bytes.fromhex('7b') # b'{'
bytes([n]).hex() # 7b
bytearray.fromhex('7bab')
```

[PyCryptodome官方文档](https://pycryptodome.readthedocs.io/en/latest/src/util/util.html#module-Crypto.Util.number)

### 加密/解密相关

```python
from string import printable # 可打印字符集
```

### 解方程

==z3一把梭==

[Introduction | Online Z3 Guide (microsoft.github.io)](https://microsoft.github.io/z3guide/programming/Z3%20Python%20-%20Readonly/Introduction/)

```python
import z3
solver = z3.Solver()
flag = [z3.Int("i_%s" % (i+1)) for i in range(32)]
solver.add()
print(solver.check())
m = solver.model()
print(m)
```