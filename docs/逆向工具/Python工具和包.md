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

## 图形处理

```python
pip install opencv-contrib-python easyocr
```

```python
import easyocr
# 创建reader对象
reader = easyocr.Reader(['en'],gpu=False, model_storage_directory="./Model")    result = reader.readtext(f'./data2/{i}.jpg')
```

```python
# 导入所有必要的库
import cv2
import os
  
# 从指定的路径读取视频
cam = cv2.VideoCapture( "./iGotSmokynomial.mp4" )
  
try :
      
    # 创建名为data的文件夹
    if not os.path.exists( 'data' ):
        os.makedirs( 'data' )
  
# 如果未创建，则引发错误
except OSError:
    print ( 'Error: Creating directory of data' )
  
# frame
currentframe = 0
count = 0

while ( True ):
    # reading from frame
    ret, frame = cam.read()
  
    if ret:
        # 如果视频仍然存在，继续创建图像
        gray_frame = cv2.cvtColor(frame, cv2.COLOR_BGR2GRAY)
        white_pixel_count = cv2.countNonZero(gray_frame)
        total_pixels = frame.shape[0] * frame.shape[1]
        # 写入提取的图像
        if white_pixel_count > (0.99 * total_pixels):
            name = './data/' + str (count) + '.jpg'
            cv2.imwrite(name, gray_frame)
            count += 1
        # 增加计数器，以便显示创建了多少帧
        currentframe += 1
    else :
        break
# 一旦完成释放所有的空间和窗口
cam.release()
cv2.destroyAllWindows()
```