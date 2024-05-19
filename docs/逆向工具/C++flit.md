# 美化C++函数名

[c++filt - 还原so中的函数名 | m4bln (mabin004.github.io)](https://mabin004.github.io/2018/08/22/c-filt/)

C++是允许函数重载的，也就引出了编译器的name mangling（名字修饰）机制,其目的是给同名的重载函数不同的签名。

这里使用 `c++filt` 的增强版本——[nico/demumble](https://github.com/nico/demumble)

cp 到 /usr/bin/ 即可

```log
$ demumble _ZNKSt7__cxx1112basic_stringIcSt11char_traitsIcESaIcEE7compareEPKc
std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char>>::compare(char const*) const
```

