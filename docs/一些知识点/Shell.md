# 壳

## upx壳

一般可以直接 `upx -d`，如果不行，就找个正常的程序

```shell
upx hello.exe -o hello_upx.exe
```

比较一下，目前发现的题目有：

- 把段名改成VMP0和VMP1
- 把UPX改成upx