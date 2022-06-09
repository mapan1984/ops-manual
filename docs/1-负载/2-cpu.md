# 1.2 CPU

## top 输出解释

    load average: 7.59, 6.63, 4.27

表示系统在 1, 5, 15 分钟内的平均工作负载分别为 7.59, 6.63, 4.27。cpu 工作负载是指一段时间内，正在使用和等待使用 cpu 的平均任务数。

    %Cpu(s): 15.9 us, 13.7 sy,  0.0 ni, 63.3 id,  0.3 wa,  0.0 hi,  6.8 si,  0.0 st

* us, user: 花在 userland 的 cpu 时间百分比
* sy, system: 花在 kernel 的 cpu 时间百分比
* ni, nice: 改变过优先级的进程占用 cpu 时间百分比
* id, idle: cpu 没有处理任何事的时间百分比
* wa, iowait: cpu 空闲并且同时有未完成的 io 请求，占用 cpu 时间的百分比
    1. cpu 空闲不一定是因为进程都在等待 io，进程休眠还可能是因为等待定时器、等待键盘输入等等原因，但有未完成的 io 请求意味着确实有进程在等待 io
    2. 因为 「有未完成的 io 请求」和 「cpu 空闲」不是明确的因果关系，所以出现 iowait 升高时，不一定是出现 io 瓶颈，还需要根据 io 相关的监控一起判断
    3. 但是如果机器上的进程很少，只有一个消耗 cpu 和 io 负载的进程，那么出现 iowait 升高，很可能是该进程出现 io 瓶颈
* hi
* si
* st

进程列表每项含义：

    PID USER      PR  NI  VIRT  RES  SHR S %CPU %MEM    TIME+  COMMAND

* `VIRT`：virtual memory usage 虚拟内存
    1. 进程 *需要的* 虚拟内存大小，包括进程使用的库、代码、数据等
    2. 假如进程申请 100m 的内存，但实际只使用了 10m，那么它会增长 100m，而不是实际的使用量
* `RES`：resident memory usage 常驻内存
    1. 进程当前使用的内存大小，但不包括 swap out
    2. 包含其他进程的共享
    3. 如果申请 100m 的内存，实际使用 10m，它只增长 10m，与 `VIRT` 相反
    4. 关于库占用内存的情况，它只统计加载的库文件所占内存大小
* `SHR`：shared memory 共享内存
    1. 除了自身进程的共享内存，也包括其他进程的共享内存
    2. 虽然进程只使用了几个共享库的函数，但它包含了整个共享库的大小
    3. 计算某个进程所占的物理内存大小公式：RES – SHR
    4. swap out 后，它将会降下来

## 参考：

- 理解 IOWAIT: http://linuxperf.com/?p=33

