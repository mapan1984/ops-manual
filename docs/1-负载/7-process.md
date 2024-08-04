# 进程分析

## /proc/*/status

查看进程详细信息 `cat /proc/<proc_id>/status`

    $ cat /proc/13151/status
    Name:    java
    Umask:    0022
    State:    S (sleeping)
    Tgid:    13151
    Ngid:    0
    Pid:    13151
    PPid:    1
    TracerPid:    0
    Uid:    0    0    0    0
    Gid:    0    0    0    0
    FDSize:    512
    Groups:
    VmPeak:    12437952 kB
    VmSize:     3874384 kB
    VmLck:           0 kB
    VmPin:           0 kB
    VmHWM:      403324 kB
    VmRSS:      392840 kB
    RssAnon:      369012 kB
    RssFile:       23828 kB
    RssShmem:           0 kB
    VmData:     3541084 kB
    VmStk:         132 kB
    VmExe:           4 kB
    VmLib:       24072 kB
    VmPTE:        1292 kB
    VmSwap:           0 kB
    Threads:    92
    SigQ:    1/31823
    SigPnd:    0000000000000000
    ShdPnd:    0000000000000000
    SigBlk:    0000000000000000
    SigIgn:    0000000000000003
    SigCgt:    2000000181005ccc
    CapInh:    0000000000000000
    CapPrm:    0000001fffffffff
    CapEff:    0000001fffffffff
    CapBnd:    0000001fffffffff
    CapAmb:    0000000000000000
    NoNewPrivs:    0
    Seccomp:    0
    Speculation_Store_Bypass:    vulnerable
    Cpus_allowed:    3ff,ffffffff
    Cpus_allowed_list:    0-41
    Mems_allowed:    00000000,00000000,00000000,00000000,00000000,00000000,00000000,00000000,00000000,00000000,00000000,00000000,00000000,00000000,00000000,00000000,00000000,00000000,00000000,00000000,00000000,00000000,00000000,00000000,00000000,00000000,00000000,00000000,00000000,00000000,00000000,00000001
    Mems_allowed_list:    0
    voluntary_ctxt_switches:    5
    nonvoluntary_ctxt_switches:    5

各项含义：

* Threads: 线程数


## /proc/sys/kernel/

* 线程数最大限制：/proc/sys/kernel/threads-max
* 进程数最大限制：/proc/sys/kernel/pid_max

