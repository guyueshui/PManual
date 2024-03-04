---
title:  202304231411
date: 2023-04-23T14:11:41+08:00
tags: [cpp, name-mangling]

---

命名修饰
=======

> Name mangling is the encoding of function and variable names into unique names so that linkers can separate common names in the language.

Name mangling 实际上是对函数（或者变量）名称的一种编码，c++支持函数重载，而 c 不支持。所以 c++的函数签名的编码方式肯定是和 c 不一样的，举个例子：

```c
int foo(int a);  // foo
```

```cpp
int foo(int a);  // foo(int)
int foo(float a);  // foo(float)
```

也可以理解为，函数原型经过编码之后得到的唯一 id，暴露给 linker 用的。linker 根据这个名字去查找、链接相应的函数地址。

同样一段 c 代码，用 c compiler 和 c++ compiler 编出来的函数&变量名称是不一样的。c++的通常会复杂一些，包含 namespace，class，形参类型列表等。当使用 c++ compiler 且不想要上述 mangling 的时候，可以使用：
```cpp
extern "C" {
	int  foo(int a);
	void bar(int b);
}
```
来告诉编译器这些名字不要进行 name mangle. 这在 c 调用 c++方法时尤其有用。

See also [链接](链接.md)

## References

1. [Name mangling (C++ only)](https://www.ibm.com/docs/en/i/7.3?topic=linkage-name-mangling-c-only)
2. [Stability of the C++ ABI: Evolution of a Programming Language](https://www.oracle.com/technical-resources/articles/it-infrastructure/stable-cplusplus-abi.html)
