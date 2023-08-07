Shell 基础
=========

### 行内操作

- `^a`: jump to BOL
- `^e`: jump to EOL
- `^u`: delete the line
- `^k`: delete to EOL
- `^w`: delete a word forward
- `alt+f`: jump a word forward
- `alt+b`: jump a word backward
- `^r`: search history
- `alt+.`: complete second parameter

### 任务控制

1. 执行`command`
2. 按`^z`挂起当前 job
4. 按`bg`后台继续该 job
3. 按`fg`召回前台

### 后台运行命令

```sh
command &
```
或者如果你不想看到任何输出，使用
```sh
command &> /dev/null &
```

- 如此你可以继续使用当前 shell
- 使用`bg`查看是否有任务在后台运行
- 使用`jobs`查看后台任务
- 使用`fg`将任务召回前台
- 不能退出 shell，否则进程会被杀掉
- **使用`disown`丢掉进程，可以退出 shell**

又：
```sh
nohup command &> /dev/null &
```
等价于以上的操作。单纯的`nohup command`会在当前目录创建一个隐藏文件以写入命令的输出。以上命令将程序的输出重定向至比特桶丢弃。

### 同时输出到 console 和文件

将命令输出重定向到文件：
```bash
SomeCommand > SomeFile.txt  # overwrite
SomeCommand >> SomeFile.txt # append
```
将命令输出 (stdout) 及报错 (stderr) 重定向到文件：
```bash
SomeCommand &> SomeFile.txt
SomeCommand &>> SomeFile.txt
```
同时输出到 console 和文件：
```bash
SomeCommand 2>&1 | tee SomeFile.txt     # overwrite
SomeCommand 2>&1 | tee -a SomeFile.txt  # append
```

如何正确重定向：https://segmentfault.com/q/1010000002454596
这里的 1，2 其实是文件描述符，在 linux 中，这几个文件描述符有特殊含义：

- 0 -> stdin
- 1 -> stdout
- 2 -> stderr

执行
```bash
<cmd> 2>&1 > log.txt #1
<cmd> > log.txt 2>&1 #2
```
放在`>`后面的`&`表示重定向的目标不是一个文件，而是一个文件描述符。也就是说，将 stderr 重定向到文件描述符为 1 的那个文件（也就是 stdout）。

```{note}
引自 http://www.gnu.org/software/bash/manual/bashref.html#Redirections

Note that the order of redirections is significant. For example, the
command

ls > dirlist 2>&1 directs both standard output (file descriptor 1) and
standard error (file descriptor 2) to the file dirlist, while the
command

ls 2>&1 > dirlist directs only the standard output to file dirlist,
because the standard error was made a copy of the standard output
before the standard output was redirected to dirlist.
```


```
          || visible in terminal ||   visible in file   || existing
  Syntax  ||  StdOut  |  StdErr  ||  StdOut  |  StdErr  ||   file
==========++==========+==========++==========+==========++===========
    >     ||    no    |   yes    ||   yes    |    no    || overwrite
    >>    ||    no    |   yes    ||   yes    |    no    ||  append
          ||          |          ||          |          ||
   2>     ||   yes    |    no    ||    no    |   yes    || overwrite
   2>>    ||   yes    |    no    ||    no    |   yes    ||  append
          ||          |          ||          |          ||
   &>     ||    no    |    no    ||   yes    |   yes    || overwrite
   &>>    ||    no    |    no    ||   yes    |   yes    ||  append
          ||          |          ||          |          ||
 | tee    ||   yes    |   yes    ||   yes    |    no    || overwrite
 | tee -a ||   yes    |   yes    ||   yes    |    no    ||  append
          ||          |          ||          |          ||
 n.e. (*) ||   yes    |   yes    ||    no    |   yes    || overwrite
 n.e. (*) ||   yes    |   yes    ||    no    |   yes    ||  append
          ||          |          ||          |          ||
|& tee    ||   yes    |   yes    ||   yes    |   yes    || overwrite
|& tee -a ||   yes    |   yes    ||   yes    |   yes    ||  append
```

Ref: 
1. https://askubuntu.com/questions/420981/how-do-i-save-terminal-output-to-a-file
2. https://www.gnu.org/software/bash/manual/bash.html#Redirections

### 从系统中踢出某个用户

```bash
# See the pid of the user's login process.
$ who -u
yychi    tty1         2020-02-19 21:06  旧        460

# Let him know he will be kick off.
$ echo "You'll be kick off by system administrator." | write yychi

# Kick off.
$ kill -9 460

# Done.
```

Ref: https://www.putorius.net/kick-user-off-linux-system.html

Reference
---------

- [Running Bash Commands in the Background the Right Way [Linux]](https://www.maketecheasier.com/run-bash-commands-background-linux/)