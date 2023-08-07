## Htop 基本操作

Htop 类似于 top，但比 top 更现代化，支持鼠标操作，支持颜色主题。在命令行键入 htop，会呈现如下界面。(图片来源：https://blog.csdn.net/freeking101/article/details/79173903)


平均负载区的三个数字分别表示过去 5、10、15 分钟系统的平均负载。进程区每一列的意义分别是：
- PID: 进程号
- USER: 进程所有者的用户名
- PRI: 优先级别
- NI: NICE 值（优先级别数值），越小优先级越高
- VIRT: 虚拟内存
- RES: 物理内存
- SHR: 共享内存
- S: 进程状态（S[leep], R[unning], Z[ombie], N 表示优先级为负数）
- CPU%: 该进程占用的 CPU 使用率
- MEM%: 该进程占用的物理内存和总内存的百分比
- TIME+: 该进程启动后占用的 CPU 时间
- Command: 该进程的启动命令

常用快捷键可在 htop 界面按？显示。

- H: 显示/隐藏用户子线程
- Space: 标记进程
- k: 杀死已标记进程
