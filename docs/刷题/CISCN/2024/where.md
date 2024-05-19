[C 将C代码封装成python可以import调用的so - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/438203378?theme=dark)

直接分析代码比较困难，直接strace查看调用

![[image-20240519091339529.png]]

```shell
strace -o strace.log python whereistheflag.py
```

![[image-20240519091502882.png]]

这里比较可以，去 `getrandom` 找找交叉引用——找不到

`_pyx_pw_11whereThel1b_13check_flag`

在头部写一个 `input()` 就可以附加调试了

![[image-20240519093433389.png]]

这里有点像比较加密后的字符串，但是没断到，看来有比较长度或者其他的逻辑

```
_Pyx_PyObject_FastCallDict(函数，参数，参数长度) 是调用函数
```

![[image-20240519095957500.png]]

refcnt就是引用次数，和gc回收有关

`_pyx_pymod_exec_whereThel1b` 是常量池

![[image-20240519100803357.png]]

所以这里的意思是

![[image-20240519100934742.png]]

randint(self, 0, 2)


[关于Cython生成的so动态链接库逆向_cython 逆向-CSDN博客](https://blog.csdn.net/qq_59700927/article/details/134978170)

```
help(whereThel1b)
```

![[image-20240519101933005.png|500]]

看起来whereistheflag函数是随机的，但是trytry不是，直接分析trytry

![[image-20240519102822688.png]]

这里就是trytry的代码