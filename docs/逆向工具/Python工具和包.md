# Python工具和包

## 解包PyInstaller

[PyInstxtractor下载](./assets/bin/pyinstxtractor.py){:download="pyinstxtractor.py"} —— [github](https://github.com/extremecoders-re/pyinstxtractor)

[PyCdc下载](./assets/bin/pycdc.exe){:download="pycdc.exe} —— [github](https://github.com/zrax/pycdc)

使用方法

```
python pyinstxtractor.py Py.exe
.\pycdc.exe .\PyLu.pyc > PyLu.py
```

## Crypto

```
pip install pycryptodome
```

### bytes相关

```python
from Crypto.Util.number import bytes_to_long, long_to_bytes
bytes.fromhex()
```

[PyCryptodome官方文档](https://pycryptodome.readthedocs.io/en/latest/src/util/util.html#module-Crypto.Util.number)

### 加密/解密相关

```python
from string import printable # 可打印字符集
```