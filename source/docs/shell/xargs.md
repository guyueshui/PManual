## `xargs` 命令参数转发

```bash
$ whatis xargs
xargs (1)            - build and execute command lines from standard input

# -i is equivalent to -I{}
echo "https://github.com/abc/efg.git" | xargs -i git clone {}
```

Ref:

- [xargs 命令：一个给其他命令传递参数的过滤器](http://c.biancheng.net/linux/xargs.html)
