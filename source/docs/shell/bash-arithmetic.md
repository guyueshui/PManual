Bash 中的整数运算
================

使用`$(( ))`包围内容（math context）遵循 C 语言的算数运算语法。说明：

- 里面可以使用 C 语言所有运算符（四则运算，位运算，移位，三目运算`?:`），以及指数运算 - `**`.
- 也可以使用`,`分隔表达式，表达式最好的值是最后一个`,`后面的表达式的值。
- 所有整数默认都是 signed，通常是 32 位，具体看平台和 bash 版本。
- 变量名称自动会替换成它们的值，unset 或空变量取值为 0.
- 没有前导 0 的数字默认为 10 进制。0x 表示 16 进制，0 表示 8 进制，符合 C 语言规范。

Arithmetic Commands
-------------------

Bash 提供两种命令使用 math context，规则同`$(( ))`，但是由于是一个命令，它会有 exit status 和 side effects.

Exit status 取决于 math context 的取值。如果取值为 0，命令视为失败并且返回 1，否则命令视为成功，返回 0.

第一个算数命令是`let`：
```bash
let a=17+23
echo "a = $a" # prints a = 40
```

第二个算数命令是`(( ))`，注意这里没有美元符号`$`，且这种用法更常见。
```bash
((a=$a+7))         # Add 7 to a
((a = a + 7))      # Add 7 to a.  Identical to the previous command.
((a += 7))         # Add 7 to a.  Identical to the previous command.

((a = RANDOM % 10 + 1))     # Choose a random number from 1 to 10.
                            # % is modulus, as in C.
```
在`(( ))`中，`>`和`<`表示单纯的大于/小于，而非重定向符号。一个包含了大于或小于号的表达式取值只可能为1（比较成功）或0（比较失败）。
```bash
if ((a > 5)); then echo "a is more than 5"; fi
```

这里有一点很容易困惑，算术命令``((a > 5))`如果 a 真的大于 5，那么表达式为 true，但 exit status 为 0，而 if 0 在 bash 中确实表示条件通过，0 在 bash 中就是表示成功。
```
$ if ((0)); then echo "trueee"; fi
$ if ((1)); then echo "trueee"; fi
trueee
$ if ((-1)); then echo "trueee"; fi
trueee
$ if ((123)); then echo "trueee"; fi
trueee
$ ((1));echo $?
0
$ ((0));echo $?
1
```
这一点和 C 语言恰恰相反，当执行一条 bash 指令（command），我们总有一个返回值（exit status），返回值取值范围从 0 到 255. 0 视为指令执行成功（当用于 if 或 while 条件判断时视为"true"）。而在 C 语言中，0 是 false，其他都是 true.

一些例子：
```bash
true; echo "$?"       # Writes 0, because a successful command returns 0.
((10 > 6)); echo "$?" # Also 0.  An arithmetic command returns 0 for true.
echo "$((10 > 6))"    # Writes 1.  An arithmetic expression evaluates to 1 for true.
```
要弄清这一点，我们这样记。在`$(( ))`和`(( ))`之中的叫做 math context，那里面的运算按照 C 语言进行。

- `$(( ))`：这是对 math context 求值。
- `(( ))`：这是一条 bash command，它有返回值（exit status），当 math context 求值为 0，视为指令失败，返回 1；当 math context 求值非 0，视为指令成功，返回 0.

Reference
---------

- [ArithmeticExpression - Greg's Wiki](http://mywiki.wooledge.org/ArithmeticExpression)