基础篇
=====

引用自 [Liam Huang](https://liam.page/2016/11/06/Linux-Info-Cheatsheet/) 的博客。

## 系统相关

```bash
lsb_release -a              # 查看操作系统版本
head -n 1 /etc/issue        # 查看操作系统版本
cat /proc/version           # 查看操作系统内核信息
uname -a                    # 查看操作系统内核信息、CPU 信息
cat /proc/cpuinfo           # 查看 CPU 信息
hostname                    # 查看计算机名字
env                         # 列出环境变量
lsmod                       # 列出加载的内核模块
uptime                      # 查看系统运行时间、负载、用户数量
cat /proc/loadavg           # 查看系统负载
```

<!--more-->

## 内存外存

```bash
free -m                     # 查看物理内存和交换区的使用情况
grep MemTotal /proc/meminfo # 查看内存总量
grep MemFree /proc/meminfo  # 查看空闲内存总量
df -h                       # 查看各分区使用情况
fdisk -l                    # 查看所有分区
swapon -s                   # 查看所有交换分区
hdparm -i /dev/hda          # 查看 IDE 磁盘的参数
dmesg | grep IDE            # 查看系统启动时 IDE 磁盘的状态
mount | column -t           # 查看各分区的挂载状态
du -sh <目录名>              # 查看指定目录的大小
```

## 网络状态

```bash
ifconfig                    # 查看所有网络接口的属性
ip addr show                # 同上
iptables -L                 # 查看 iptables 防火墙
route -n                    # 查看本机路由表
netstat -lntp               # 查看所有监听端口
netstat -antp               # 查看所有已建立的连接
netstat -s                  # 查看网络统计信息
lsof -i:<port>              # 查看哪个程序占用了某个端口
```

lsof 很强大，参考[这里](https://linuxtools-rst.readthedocs.io/zh-cn/latest/tool/lsof.html)。

## 用户状态相关

```bash
w                           # 查看活动用户以及他们在做什么
who                         # 查看活动用户，-u显示进程号
id <用户名>                  # 查看用户的 ID、组信息
cut -d: -f1 /etc/passwd     # 查看系统中所有用户
cut -d: -f1 /etc/group      # 查看系统所有组
usermod -a -G group1,group2 user  # 将用户追加到组
groups user                 # 查看用户所属的组
```

参考：https://linux.cn/article-10768-1.html

## 进程状态相关

```bash
ps -ef                      # 查看所有进程
ps aux                      # 同上
top                         # 动态显示进程状态
```

## 用户管理

**添加主用户（带 sudo 权限）**

```bash
useradd yychi -m         # -m表示创建家目录
usermod yychi -aG yychi  # 加入yychi组
passwd yychi             # 设置密码
visudo                   # 打开sudoers文件，并uncomment wheel组
usermod yychi -aG wheel  # 将yychi加入wheel组，此时yychi可以使用sudo了
```

**查看文件夹内所有文件**
```bash
ls -lR somedir | grep "^-" | wc -l
```

## 踢掉某个用户

linux 踢出用户，首先使用`w`或`who`定位想要踢的用户，
```bash
[root@kwephis5259858 sftp]# w
 17:24:47 up 268 days, 22:10,  4 users,  load average: 0.02, 0.06, 0.02
USER     TTY        LOGIN@   IDLE   JCPU   PCPU WHAT
root     pts/0     17:21    3:07   0.60s  0.60s python update_cp_multi.py upgrade_multi_CP2.ini
root     pts/2     27Feb24 47:30m  0.41s  0.41s -bash
root     pts/79    05Feb24  0.00s  0.70s  0.00s w
root     pts/106   Tue19   47:30m  0.15s  0.01s ssh 192.168.100.33
[root@kwephis5259858 sftp]# who
root     pts/0        2024-03-04 17:21 (10.*.*.1)
root     pts/2        2024-02-27 16:18 (10.*.*.2)
[root@kwephis5259858 sftp]#
```
比如想踢掉在执行 python 的那个用户，他登录的 tty 是`pts/0`，则
```bash
pkill -9 -t pts/0
```
踢掉该用户。