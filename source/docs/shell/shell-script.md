Shell 脚本基础
=============

条件表达式中的正则匹配
--------------------

```shell
x=clt
if [[ $x =~ ^\.?clt$ ]]; then
    echo match
fi
```
匹配 clt 和.clt。注意正则表达式不能带双引号，亲测带双引号的 bash 和 sh 判断失败，zsh 可以判断成功。

脚本调试
-------

参考[这里](https://www.cnblogs.com/robinunix/p/11635560.html)。

有时脚本出错不知道错在哪里，可以开启“调试”模式。

- `set -e`：若指令返回值非零，则立即退出 shell.
- `set -x`：在执行指令前，打印指令内容，包括执行的命令及其参数，常用于调试。

PS: 选项可以合并到一条 set 语句：`set -ex`.
PPS: 也可以执行`bash -ex some-script.sh`等价于在脚本里面写`set -ex`.

♻️循环执行一个命令
-----------------

几种写法，参考[此处](https://www.cnblogs.com/yychuyu/p/12967366.html)。

```bash
while ! ping -c3 baidu.com; do
    sleep 2
done
echo succeed
```

`!`可以直接判否，判断一个命令是否执行成功。

```bash
until ping -c3 baidu.com; do
    sleep 2
done
echo succeed
```