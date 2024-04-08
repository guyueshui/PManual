find 命令
=========

```bash
$ find --help
Usage: find [-H] [-L] [-P] [-Olevel] [-D help|tree|search|stat|rates|opt|exec] [path...] [expression]
```

前面的选项不常用，初级使用只需掌握
```bash
find [path...] [expression]
```

- `path` - search path
- `expression` - expands to `-options [-print -exec -ok]`
    + `-options`: 指定 find 常用选项
    + `-print`: 将匹配到的文件写入标准输出 [默认]
    + `-exec`: 在匹配到的文件上执行一串命令。格式为`<command> {} \;`，注意 {} 和 \; 之间的空格。
        * `find . -size 0 -exec rm {} \;` - 删除当前目录下 size 为 0 的文件
        * `rm -i $(find . -size 0)` - 同上
        * `find . -size 0 | xargs rm -f &` - 同上
    + `-ok`: 同上，执行命令前会询问

常用选项
-------

- name - 按照文件名查找
    + `find <dir> -name "*.cpp"`: 在目录 dir 下查找后缀为 cpp 的文件
    + `-name`默认不支持正则表达式，顶多支持通配符`*`
- perm - 按照文件权限查找
- user - 按照文件所有者查找
- group - 按照文件所有组查找
- type - 按照文件类型查找
- size - 按照文件大小查找
- ...

正则表达式
---------

```bash
find path -regex "<regex>"
```
但是默认的正则表达式引擎我也不知道是啥，反正不解析我习惯的那种正则语法。故使用：
```bash
find . -regextype posix-extended -regex ".*\.(log|aux|blg)"
```
上述命令找出当前文件夹及子文件夹所有后缀名为`log`,`aux`,`blg`的文件。

几个例子
-------

- `find . -name "*name*"` - 找出当前文件夹文件名包含“name”的文件
- `find . ! -type d -print` - 在当前目录查找非目录文件
- `find . -newer file1 ! file2` - 查找比 file1 新但比 file2 旧的文件
- `find -type d -empty | xargs -n 1 rmdir` - 批量删除当前目录下的空文件夹
- `find -tyle l -exec ls -l {} +` - 找出当前文件夹下损坏的软连接

更多示例
--------

参考：http://linux.51yip.com/search/find
```bash
andy@ubuntu:~$ find ./ -name "null_*" -exec basename {} \; | sort   #搜索文件，并只显示文件名，以升序排列。
null_0
null_1
null_2
null_3
null_4
null_5
null_6
null_7
null_8
null_9
```

```bash
#匹配多个条件中的一个，采用OR条件操作
sugar@ubuntu:~/app/shell$ find . \( -name "*.sh" -o -name "*.txt" \) -print 
./cutTest.sh
./alertusage.sh
./debugmethod.sh
./debug.sh
./test.txt
./plusTest.sh
./getDate.sh
./noPasswd.sh
```

```bash
# 寻找 当前目录 修改时间在 2019-01-24 的文件
find . -type f -newermt 2019-01-24 ! -newermt 2019-01-25 

# 寻找 当前目录 修改时间在 2019-01-24 08:00 ~ 2019-01-25 08:00 的文件
find . -type f -newermt "2019-01-24 08:00" ! -newermt "2019-01-25 08:00"
```

```bash
# --max-depth 对查找文件的深度---及层数设定。
[root@localhost etc]# find . -maxdepth 4 -name "*.txt"
./pki/nssdb/pkcs11.txt
./brltty/Input/ba/all.txt
./brltty/Input/bd/all.txt
./brltty/Input/bl/18.txt
./brltty/Input/bl/40_m20_m40.txt
./brltty/Input/ec/all.txt
```
