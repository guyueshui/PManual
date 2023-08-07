## Command `grep`

最基本用法：
```bash
# 查找 somefile 中匹配到 something 的行
$ grep "something" somefile

# 定位 something 所在的行并将接下来的 3 行一并输出
$ grep "something" somefile -A 3

# 定位 something 所在的行并将之前的 3 行一并输出
$ grep "something" somefile -B 3

# 定位 something 所在的行并将上下 3 行一并输出
$ grep "something" somefile -C 3
```

**使用正则表达式**

`grep`支持三种正则：basic (BRE), extend (ERE), perl (PCRE). 不同的`grep`实现方式不同，详见手册。一般 extend 最为常用，语法为
```bash
# 在 somefile 中查找包含 his 或者 her 的行
$ grep -E "his|her" somefile
```

Ref:

- [grep 命令系列：grep 中的正则表达式](https://linux.cn/article-6941-1.html)