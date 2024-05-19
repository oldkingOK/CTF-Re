# 编译

```shell
gcc foo.c -o foo.o # 默认——动态链接库编译
g++ test.cpp --static -o test # 静态链接编译
```

### gcc 去掉符号表

- -s：去掉符号表和调试信息。
- -g：只去掉调试信息，保留符号表。
- -N <符号名称>：去掉指定的符号