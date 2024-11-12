## `cut` 按列剪切文本

```bash
$ whatis cut
cut (1)              - remove sections from each line of files
```

基本用法：

```bash
# 以：为分隔符分割每行，并选择第 1,2,4 列输出
$ cut -d: -f1,2,4 /etc/passwd
root:x:0
bin:x:1
daemon:x:2
mail:x:12
ftp:x:11
http:x:33
nobody:x:65534
dbus:x:81
```
