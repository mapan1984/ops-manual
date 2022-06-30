# 1.2 CPU

## CPU Load

CPU 负载是指正在使用和等待使用 CPU 的进程数。

对于单核处理器，如果 CPU 负载小于 1，意味着每个进程都能被及时处理，如果大于 1，意味着 CPU 满负荷，且有部分进程正处于等待状态，不能被及时处理。

对于多核处理器，比如 n 核，每个 CPU 满负荷处理时系统负载为 n，系统负载超过 n，意味着有进程不能被及时处理。

## CPU Usage

CPU 使用率是指单位时间内 CPU 工作时间的占比。

## 查看 CPU 信息

    $ cat /proc/cpuinfo

## uptime 输出解释

    $ uptime
     16:58:33 up  2:07,  1 user,  load average: 2.76, 1.78, 1.46

表示系统在 1, 5, 15 分钟内的平均工作负载分别为 2.76, 1.78, 1.46。

## vmstat 使用

使用：

    vmstat [<interval> [<count>]]

比如每隔 1 秒展示一次统计数据，总共展示 2 次：

    $ vmstat 1 2
    procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
     r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
     6  0      0 4081200  46644 980480    0    0     9    20  559  431 15  8 77  0  0
     8  0      0 4082736  46644 980480    0    0     0     0 65887 21435 17 11 72  0  0

输出上一秒内的 CPU 使用率

    echo "CPU Usage: "$[100-$(vmstat 1 2|tail -1|awk '{print $15}')]"%"

> 注意：vmstat 从 /proc/stat 获取信息，计算得到展示结果，/proc/stat 提供的是累计值，展示的数据需要计算时间间隔前后的变化，所以 vmstat 首次运行时显示自系统启动开始的各项统计信息，之后运行 vmstat 将显示自上次运行该命令以后的统计信息

## top 输出解释

    load average: 7.59, 6.63, 4.27

表示系统在 1, 5, 15 分钟内的平均工作负载分别为 7.59, 6.63, 4.27。

    %Cpu(s): 15.9 us, 13.7 sy,  0.0 ni, 63.3 id,  0.3 wa,  0.0 hi,  6.8 si,  0.0 st

* us, user: 花在 userland 的 cpu 时间百分比
* sy, system: 花在 kernel 的 cpu 时间百分比
* ni, nice: 改变过优先级的进程占用 cpu 时间百分比
* id, idle: cpu 空闲状态的时间百分比
* wa, iowait: cpu 空闲并且同时有未完成的 io 请求，占用 cpu 时间的百分比
    1. cpu 空闲不一定是因为进程都在等待 io，进程休眠还可能是因为等待定时器、等待键盘输入等等原因，但有未完成的 io 请求意味着确实有进程在等待 io
    2. 因为「有未完成的 io 请求」和 「cpu 空闲」不是明确的因果关系，所以出现 iowait 升高时，不一定是出现 io 瓶颈，还需要根据 io 相关的监控一起判断
    3. 但是如果机器上的进程很少，只有一个消耗 cpu 和 io 负载的进程，那么出现 iowait 升高，很可能是该进程出现 io 瓶颈
* hi
* si
* st, stolen: time is stolen from a virtual machine

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

## /proc/stat

    $ cat /proc/stat
    cpu  1556845 9332 426834 71797181 20571 0 77501 0 0 0
    cpu0 797997 18 205486 35880989 15411 0 37980 0 0 0
    cpu1 758848 9314 221348 35916191 5159 0 39520 0 0 0
    intr 381840449 56 10 0 0 632 0 0 0 0 0 0 0 15 0 0 0 0 0 0 22 0 0 0 0 1 1 1 1 1 1 1 1 1 1 1 1 0 0 0 32627201 1141 29904569 1652 0 555151 0 3 0 0 0 258369 0 181135 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
    ctxt 694123415
    btime 1655693978
    processes 766648
    procs_running 2
    procs_blocked 0
    softirq 379953149 6 87302416 1 208869762 993234 0 92 46347545 0 36440093

监控程序一般从 `/proc/stat` 中采集 CPU 监控数据，`/proc/stat` 中所有数字都是自系统开机后的累计值。

### cpu

以 `cpu` 开头的一行数据汇总了机器所有 cpu 核的数据，之后的 `cpu0`, `cpu1` 表示不同 cpu 核分开的数据(这里是 2 核 cpu)，每一行都有 10 列数字，按顺序分别表示：

1. user – time spent in user mode
2. nice – time spent processing nice processes in user mode
3. system – time spent executing kernel code
4. idle – time spent idle
5. iowait – time spent waiting for I/O to completed. This is considered idle time too. *since Linux 2.5.41*
6. irq – Time spent serving hardware interrupts. See the description of the intr line for more details. *since Linux 2.6.0*
7. softirq – time spent servicing software interrupts. *since Linux 2.6.0*
8. steal – Time stolen by other operating systems running in a virtual environment. *since Linux 2.6.11*
9. guest – Time spent for running a virtual CPU or guest OS under the control of the kernel. *since Linux 2.6.24*
10. guest_nice – time spent running a virtual CPU for a “niced” guest operating system. *since Linux 2.6.33*

这里的 CPU 时间以 `USER_HZ`(也被称为 `Jiffies`) 为单位，通常为 `1/100` 秒。`USER_HZ` 为编译时常量，可以通过以下方式查询：

``` bash
$ getconf CLK_TCK
100
```

``` python
import os
ticks = os.sysconf(os.sysconf_names['SC_CLK_TCK'])
```

``` c
#include <unistd.h>
#include <sys/types.h>
#include <sys/param.h>

const double ticks = (double)sysconf( _SC_CLK_TCK );
```

`SC_CLK_TCK` 为 100，表示每秒有 100 个 CPU 时钟(clock ticks)，即每个 CPU 时间为 `1/100` 秒。

### 计算 CPU 使用率

根据 `/proc/stat` 中 CPU 时间数据，可以计算 CPU 使用率。

CPU 使用率是指单位时间内 CPU 工作时间的占比，可以表示为:

    CPU Usage (%) = 100 - idle / (user + nice + system + idle + iowait + irq + softirq + steal + guest + guest_nice) * 100

用以下命令可以计算自系统开机后，CPU 的平均使用率：

    cat /proc/stat | grep cpu | tail -1 | awk '{print $5/($2+$3+$4+$5+$6+$7+$8+$9+$10)*100}' | awk '{print "CPU Usage: " 100-$1}'

因为 `/proc/stat` 中的数据是累计，如果要计算实时的 CPU 使用率，需要以固定时间间隔读取 `/proc/stat`，计算此时间内 CPU 使用时间占比。下面的脚本可以每隔 1 秒输出这 1 秒内的 CPU 使用率。

``` bash
#!/bin/bash

while :; do

  # Get the first line with aggregate of all CPUs
  cpu_now=($(head -n1 /proc/stat))

  # Get all columns but skip the first (which is the "cpu" string)
  cpu_sum="${cpu_now[@]:1}"

  # Replace the column seperator (space) with +
  cpu_sum=$((${cpu_sum// /+}))

  # Get the delta between two reads
  cpu_delta=$((cpu_sum - cpu_last_sum))

  # Get the idle time Delta
  cpu_idle=$((cpu_now[4]- cpu_last[4]))

  # Calc time spent working
  cpu_used=$((cpu_delta - cpu_idle))

  # Calc percentage
  cpu_usage=$((100 * cpu_used / cpu_delta))

  # Keep this as last for our next read
  cpu_last=("${cpu_now[@]}")
  cpu_last_sum=$cpu_sum

  echo "CPU usage at $cpu_usage%"

  # Wait a second before the next read
  sleep 1
done
```

### 其他行含义

* `intr`: 以 `intr` 开头的一行数据表示自系统开机后发生的所有中断(interrupt)，查看 `/proc/interrupts` 可以看到更多详细信息。
* `ctxt`: 表示所有 CPU 上下文切换的次数
* `btime`: 表示自系统开机时的 unix 时间(单位秒)
* `processes`: 表示已创建的进程与线程的数量
* `procs_running`: 表示正在占用 CPU 运行的进程数量
* `procs_blocked`: 表示阻塞状态的进程数量

## 参考

- 理解 IOWAIT: http://linuxperf.com/?p=33
- https://www.idnt.net/en-US/kb/941772
- https://www.baeldung.com/linux/get-cpu-usage
- https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk65143
