Make 的基本用法
==============

> 本文是我对《陈皓 - 跟我一起写 Makefile》的学习笔记

makefile 的规则
---

一个 makefile 可以有很多个 rules，一个 rule 长这样：

```makefile
target ... : prerequisites ...
    recipe
    ...
    ...
```

- target：可以是一个 object file（目标文件），也可以是一个可执行文件，还可以是一个标签（label）。
- prerequisites：生成该 target 所依赖的文件和/或 target。
- recipe：该 target 要执行的命令（任意的 shell 命令）。

一个 rule 包含三个部分

- 一个或多个 targets
- 0 个或多个 dependencies
- 0 个或多个 commands（recipe）

这是一个文件的依赖关系，也就是说，target 这一个或多个的目标文件依赖于 prerequisites 中的文件，其生成规则定义在 command 中。说白一点就是说：

```
prerequisites中如果有一个以上的文件比target文件要新的话，recipe所定义的命令就会被执行。
```

这就是 makefile 的规则，也就是 makefile 中最**核心**的内容。

重要参数：

- `-n` : dry run
- `-f` : 指定 makefile
- `-s` : silent/quiet，静默模式，不显示任何输出

规则说明：

- recipe 中的命令默认使用`/bin/sh`解释 shell 命令
- 输入`make target`意味着
    1. 确定所有的依赖都是最新的
    2. 如果 target 比任何一个 dependency 旧，则重新构建 target
- 输入`make`默认构建 Makefile 中的第一个 target
- Phony target（伪目标）：伪目标的名字并不表示真的要生成这样一个文件，伪目标仅包含 recipe 和 target，不包含任何 dependency

命令的开头
---

- recipe 中的命令一定要以一个<kbd>Tab</kbd>键作为开头，**不能用空格代替**
- recipe 中的命令若以`-`开头，表示如果命令执行出错，继续执行下一条命令
- recipe 中的命令若以`@`开头，表示命令本身不会输出，但命令的输出（如有）会输出

命令的执行
---

make 会一条一条执行 recipe 中的命令，需要注意的是，如果你要让上一条命令的结果应用在下一条命令时，你应该使用分号分隔这两条命令。比如你的第一条命令是 cd 命令，你希望第二条命令得在 cd 之后的基础上运行，那么你就不能把这两条命令写在两行上，而应该把这两条命令写在一行上，用分号分隔。如：

```makefile
#1
exec:
    cd /home/hchen
    pwd

#2
exec:
    cd /home/hchen; pwd
```

当我们执行 `make exec` 时，第一个例子中的 cd 没有作用，pwd 会打印出当前的 Makefile 目录，而第二个例子中，cd 就起作用了，pwd 会打印出“/home/hchen”。

嵌套执行 make
---

在一些大的工程中，我们会把我们不同模块或是不同功能的源文件放在不同的目录中，我们可以在每个目录中都书写一个该目录的 Makefile，这有利于让我们的 Makefile 变得更加地简洁，而不至于把所有的东西全部写在一个 Makefile 中，这样会很难维护我们的 Makefile，这个技术对于我们模块编译和分段编译有着非常大的好处。

例如，我们有一个子目录叫 subdir，这个目录下有个 Makefile 文件，来指明了这个目录下文件的编译规则。那么我们总控的 Makefile 可以这样书写：

```makefile
subsystem:
    cd subdir && $(MAKE)
```

其等价于：

```makefile
subsystem:
    $(MAKE) -C subdir
```

定义$(MAKE) 宏变量的意思是，也许我们的 make 需要一些参数，所以定义成一个变量比较利于维护。这两个例子的意思都是先进入“subdir”目录，然后执行 make 命令。

我们把这个 Makefile 叫做“总控 Makefile”，总控 Makefile 的变量可以传递到下级的 Makefile 中（如果你显示的声明），但是不会覆盖下层的 Makefile 中所定义的变量，除非指定了 `-e` 参数。

定义命令包
---

如果 Makefile 中出现一些相同命令序列，那么我们可以为这些相同的命令序列定义一个变量。定义这种命令序列的语法以 `define` 开始，以 `endef` 结束，如：

```makefile
define run-yacc
    yacc $(firstword $^)
    mv y.tab.c $@
endef
```

这里，“run-yacc”是这个命令包的名字，其不要和 Makefile 中的变量重名。在 `define` 和 `endef` 中的两行就是命令序列。这个命令包中的第一个命令是运行 Yacc 程序，因为 Yacc 程序总是生成“y.tab.c”的文件，所以第二行的命令就是把这个文件改改名字。还是把这个命令包放到一个示例中来看看吧。

```makefile
foo.c : foo.y
    $(run-yacc)
```

我们可以看见，要使用这个命令包，我们就好像使用变量一样。在这个命令包的使用中，命令包“run-yacc”中的 `$^` 就是 `foo.y` ， `$@` 就是 `foo.c` （有关这种以 `$` 开头的特殊变量，我们会在后面介绍），make 在执行命令包时，命令包中的每个命令会被依次独立执行。

使用变量
---

在 Makefile 中的定义的变量，就像是 C/C++语言中的宏一样，他代表了一个文本字串，在 Makefile 中执行的时候其会自动原模原样地展开在所使用的地方。其与 C/C++所不同的是，你可以在 Makefile 中改变其值。在 Makefile 中，变量可以使用在“目标”，“依赖目标”， “命令”或是 Makefile 的其它部分中。

命名规则：变量的命名字可以包含字符、数字，下划线（可以是数字开头），但不应该含有 `:` 、 `#` 、 `=` 或是空字符（空格、回车等）。变量是大小写敏感的，“foo”、“Foo”和“FOO”是三个不同的变量名。传统的 Makefile 的变量名是全大写的命名方式，但我推荐使用大小写搭配的变量名，如：MakeFlags。这样可以避免和系统的变量冲突，而发生意外的事情。

有一些变量是很奇怪字串，如 `$<` 、 `$@` 等，这些是自动化变量，我会在后面介绍。

> macro `@` evaluates to the name of the current target.
> 可用`make -p`打印内部宏

变量在声明时需要给予初值，而在使用时，需要给在变量名前加上 `$` 符号，但最好用小括号 `()` 或是大括号 `{}` 把变量给包括起来。如果你要使用真实的 `$` 字符，那么你需要用 `$$` 来表示。

变量可以使用在许多地方，如规则中的“目标”、“依赖”、“命令”以及新的变量中。先看一个例子：

```makefile
objects = program.o foo.o utils.o
program : $(objects)
    cc -o program $(objects)

$(objects) : defs.h
```

变量会在使用它的地方精确地展开，就像 C/C++中的宏一样，例如：

```makefile
foo = c
prog.o : prog.$(foo)
    $(foo)$(foo) -$(foo) prog.$(foo)
```

展开后得到：

```makefile
prog.o : prog.c
    cc -c prog.c
```

当然，千万不要在你的 Makefile 中这样干，这里只是举个例子来表明 Makefile 中的变量在使用处展开的真实样子。可见其就是一个“替代”的原理。

另外，给变量加上括号完全是为了更加安全地使用这个变量，在上面的例子中，如果你不想给变量加上括号，那也可以，但我还是强烈建议你给变量加上括号。

与C/C++不同，为变量赋值时，右侧变量可以是后面定义的变量：

```makefile
foo = $(bar)
bar = $(ugh)
ugh = Huh?

all:
    echo $(foo)
```

我们执行“make all”将会打出变量 `$(foo)` 的值是 `Huh?` （ `$(foo)` 的值是 `$(bar)` ， `$(bar)` 的值是 `$(ugh)` ， `$(ugh)` 的值是 `Huh?` ）可见，变量是可以使用后面的变量来定义的。

还有另一种使用变量的方式（推荐）：

```makefile
x := foo
y := $(x) bar
x := later
```

其等价于：

```makefile
y := foo bar
x := later
```

值得一提的是，这种方法，**前面的变量不能使用后面的变量**，只能使用前面已定义好了的变量。如果是这样：

```makefile
y := $(x) bar
x := foo
```

那么，y 的值是“bar”，而不是“foo bar”。

总结一下：

- `=`操作符允许先使用变量，后为变量赋值，但容易引发递归定义的问题
- `:=`操作符遵循常规变量先定义后使用的原则，推荐使用

### 行尾注释的副作用

```makefile
nullstring :=
space := $(nullstring) # essential for one space
dir := /foo/bar         # dir for xxx
all:
        @echo "$(space),$(dir),$(nullstring),hehe"
```

```bash
$ make -n
echo " ,/foo/bar                ,,hehe"
$ make
 ,/foo/bar              ,,hehe
```

注意其中的空格，由此可见，行尾注释之前的空格也会被附加到变量值中。如果行尾没有注释，space 变量将没有空格，dir 变量也将恢复正常，没有后面的空格。**用“#”注释符来表示变量定义的终止**。这样，我们可以定义出其值是一个空格的变量。

### `?=` 操作符

```makefile
FOO ?= bar
```

其含义是，如果 FOO 没有被定义过，那么变量 FOO 的值就是“bar”，如果 FOO 先前被定义过，那么这条语将什么也不做，其等价于：

```makefile
ifeq ($(origin FOO), undefined)
    FOO = bar
endif
```

### `+=` 操作符

我们可以使用 `+=` 操作符给变量追加值，如：
```makefile
objects = main.o foo.o bar.o utils.o
objects += another.o
```
于是，我们的 `$(objects)` 值变成：“main.o foo.o bar.o utils.o another.o”（another.o 被追加进去了）。它等价于下面的写法：
```makefile
objects = main.o foo.o bar.o utils.o
objects := $(objects) another.o
```
很明显，`+=` 更简洁。

如果变量之前没有定义过，那么， `+=` 会自动变成 `=` ，如果前面有变量定义，那么 `+=` 会继承于前次操作的赋值符。如果前一次的是 `:=` ，那么 `+=` 会以 `:=` 作为其赋值符。

仍然，小心使用`=`和`+=`时引发的递归定义：
```makefile
v = $(value)
value += $v
all:
        @echo "value is $(value)"
```

```bash
$ make
Makefile:6: *** Recursive variable 'value' references itself (eventually).  Stop.
```

### 目标变量

前面我们所讲的在 Makefile 中定义的变量都是“全局变量”，在整个文件，我们都可以访问这些变量[^a]。当然，我也同样可以为某个目标设置局部变量，这种变量被称为“Target-specific Variable”，它可以和“全局变量”同名，因为它的作用范围只在这条规则以及连带规则中，所以其值也只在作用范围内有效。而不会影响规则链以外的全局变量的值。

其语法是：
```makefile
<target ...> : <variable-assignment>;
<target ...> : overide <variable-assignment>
```
`<variable-assignment>;`可以是前面讲过的各种赋值表达式，如 `=` 、 `:=` 、 `+=` 或是 `?=` 。第二个语法是针对于 make 命令行带入的变量，或是系统环境变量。

这个特性非常的有用，当我们设置了这样一个变量，这个变量会作用到由这个目标所引发的所有的规则中去。如：
```makefile
prog : CFLAGS = -g
prog : prog.o foo.o bar.o
    $(CC) $(CFLAGS) prog.o foo.o bar.o

prog.o : prog.c
    $(CC) $(CFLAGS) prog.c

foo.o : foo.c
    $(CC) $(CFLAGS) foo.c

bar.o : bar.c
    $(CC) $(CFLAGS) bar.c
```
在这个示例中，不管全局的 `$(CFLAGS)` 的值是什么，在 prog 目标，以及其所引发的所有规则中（prog.o foo.o bar.o 的规则）， `$(CFLAGS)` 的值都是 `-g`.
### 高级用法

拼接：

```makefile
first_second = Hello
a = first
b = second
all = $($a_$b)
```

这里的 `$a_$b` 组成了“first_second”，于是，`$(all)` 的值就是“Hello”。当然，“把变量的值再当成变量”这种技术，同样可以用在操作符的左边：

```makefile
dir = foo
$(dir)_sources := $(wildcard $(dir)/*.c)
define $(dir)_print
    lpr $($(dir)_sources)
endef
```

这个例子中定义了三个变量：“dir”，“foo_sources”和“foo_print”。

Reference
---

- [跟我一起写 Makefile - 陈皓](https://seisman.github.io/how-to-write-makefile)
- [A Short Introduction to Makefile](https://www3.nd.edu/~zxu2/acms60212-40212/Makefile.pdf)

[^a]:当然，“自动化变量”除外，如 `$<` 等这种类量的自动化变量就属于“规则型变量”，这种变量的值依赖于规则的目标和依赖目标的定义。