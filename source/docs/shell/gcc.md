## Command `g++`

自定义包含路径
```bash
g++ main.cpp -I/usr/local/include
```

自定义链接静态或动态库
```bash
g++ main.cpp -L/path/to/lib_file
g++ main.cpp -L/usr/lib64 -lcurl -lssl
```
上面第二个命令链接了`/usr/lib64/`目录下的`libcurl.so`和`libssl.so`两个动态库文件。静态库也是同样链接。说起来静态库，想起了最近折腾的一个东西，你可能会想把多个静态库合成一个静态库，想当然的直接用`ar`合并，但是不行，必须要把两个静态库全解压出来，再合并所有的 object file. 参见：[here](https://www.cnblogs.com/fnlingnzb-learner/p/8127456.html)

生成机器码
```bash
g++ main.cpp -c
```

生成汇编代码
```bash
g++ main.cpp -S
```

仅预编译
```bash
g++ main.cpp -E > main.i
```
