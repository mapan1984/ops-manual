# 1.3 磁盘

## iostat 使用

使用：

    iostat [ options  ] [ <interval> [ <count>  ]  ]

比如间隔 2 每秒展示一次统计数据，总共展示 20 次：

    iostat -x -p 2 20

参数 options 与含义：

    -x 显示详细信息
    -p[磁盘] 显示磁盘和分区的情况
    -C 显示CPU使用情况
    -d 显示磁盘使用情况
    -k 以 KB 为单位显示
    -m 以 M 为单位显示
    -N 显示磁盘阵列(LVM) 信息
    -t 显示终端和CPU的信息
    -V 显示版本信息

* `rrqm/s` 和 `wrqm/s` (read request merged, write request merged)：每秒合并的读和写请求。如果请求队列中多个逻辑请求的地址位于相邻/同一个块，操作系统会将这些请求合并为一个请求到实际磁盘。一般顺序访问相比随机访问会出现更多的合并数
* `r/s`：每秒发送到设备的读请求数（合并之后的数值）
* `w/s`：每秒发送到设备的写请求数（合并之后的数值）
* `rkB/s`: 每秒读数据量(kB为单位)
* `wkB/s`: 每秒写数据量(kB为单位)
* `rsec/s` 和 `wsec/s`：每秒读和写的扇区数（每扇区大小为512字节）
* `avgrq–sz`：平均每次 IO 请求的扇区数
* `avgqu–sz`：平均在设备队列中等待的 IO 请求数
* `await`：平均每个 IO 请求花费的时间（等待时间与处理时间，单位为 ms）
* `r_await` 和 `w_await`: 平均每个 IO 读请求与写请求的所花费的时间
* `svctm`：平均每个 IO 请求（服务）处理时间 **这项指标不可信，将被废弃**
* `%util`：采集周期内有 IO 处理的时间比率，即 IO 队列非空的时间比率。这里有 IO 处理没有考虑 IO 有多少，只考虑有没有，但是一般块设备可以并发处理请求，所以单纯看这个值并不能说明设备处理是否饱和

> 注意：iostat 从 /proc/diskstats 获取信息，计算得到展示数，/proc/diskstats 提供的是累计值，展示的数据需要计算时间间隔前后的变化，所以 iostat 首次运行时显示自系统启动开始的各项统计信息，之后运行 iostat 将显示自上次运行该命令以后的统计信息

> util = (r/s+w/s) * (svctm/1000)

> 常见 linux 的磁盘 IO 指标的缩写习惯：
> rq 是 request
> r 是 read
> w 是 write
> qu 是 queue
> sz 是 size
> a 是 average
> tm 是 time
> svc 是 service。

## /proc/diskstats

    $ cat /proc/diskstats
       8       0 sda 284582428 3662549 46775621561 463232949 331115798 379042858 23932284589 1218204980 0 1137934235 1949683834
       8       1 sda1 22 0 176 248 0 0 0 0 0 256 256
       8       2 sda2 31919620 529912 1308796698 168580225 3802763 484906 37468832 3650707 0 102423621 187605638
       8       3 sda3 1903036 20859 112724155 11083792 7317913 295559 67078769 7332650 0 14976486 23037140
       8       4 sda4 19758029 135050 2122710340 29034910 78120710 6565898 1001751842 54812811 0 81205754 132614573

`/proc/diskstats` 有 14 个字段，除了 `in_flight` 字段外都是累计值，从系统启动之后一直累加，按顺序分别为：

0. `major_dev_num`
1. `minor_dev_num`
2. `device`
3. `read_ios`: 读操作的次数。
4. `read_merges`: 合并读操作的次数。如果两个读操作读取相邻的数据块时，可以被合并成一个，以提高效率。合并的操作通常是 I/O scheduler（也叫elevator）负责的。
5. `read_sectors`: 读取的扇区数量。
6. `read_ticks`: 读操作消耗的时间(以毫秒为单位)，包括了在队列中等待的时间。
7. `write_ios`: 写操作的次数。
8. `write_merges`: 合并写操作的次数。
9. `write_sectors`: 写入的扇区数量。
10. `write_ticks`: 写操作消耗的时间(以毫秒为单位)。
11. `in_flight`: 当前未完成的 I/O 数量。在 I/O 请求进入队列时该值加 1，在 I/O 结束时该值减 1。注意：是 I/O 请求进入队列时，而不是提交给硬盘设备时。
12. `io_ticks`: 该设备用于处理 I/O 的自然时间(`wall-clock time`)。请注意 `io_ticks` 与 `read_ticks` 和 `write_ticks` 的区别，`read_ticks` 和 `write_ticks` 是把每一个 I/O 所消耗的时间累加在一起，因为硬盘设备通常可以并行处理多个 I/O，所以 `read_ticks` 和 `write_ticks` 往往会比自然时间大。而 `io_ticks` 表示该设备有 I/O（即非空闲）的时间，不考虑 I/O 有多少，只考虑有没有。在实际计算时，字段 `in_flight` 不为零的时候 `io_ticks` 保持计时，字段 `in_flight` 为零的时候 `io_ticks` 停止计时。
13. `time_in_queue`: 对 `io_ticks` 的加权值。`io_ticks` 是自然时间，不考虑当前有几个 I/O，而 `time_in_queue` 是用当前的 I/O 数量（即字段 `in-flight`）乘以自然时间。虽然该字段的名称是 `time_in_queue`，但并不真的只是在队列中的时间，其中还包含了硬盘处理 I/O 的时间。iostat 在计算 `avgqu-sz` 时会用到这个字段。

监控项：

1. 每秒读 io 次数：r/s = Δread_ios/Δt
2. 每秒写 io 次数：w/s = Δwrite_ios/Δt
3. 每个读操作平均所需的时间: r_await = Δread_ticks/Δread_ios
    > 不仅包括硬盘设备读操作的时间，还包括了在kernel队列中等待的时间。
4. 每个写操作平均所需的时间: w_await = Δwrite_ticks/Δwrite_ios
    > 不仅包括硬盘设备写操作的时间，还包括了在kernel队列中等待的时间。
5. 该硬盘设备的繁忙比率: %util = Δio_ticks/Δt
    > 表示该设备有I/O（即非空闲）的时间比率，不考虑I/O有多少，只考虑有没有。


``` python
def diskstats():
    file_path = '/proc/diskstats'

    # http://lxr.osuosl.org/source/Documentation/iostats.txt
    columns_disk = ['major_dev_num', 'minor_dev_num', 'device', 'reads', 'reads_merged', 'sectors_read', 'ms_reading', 'writes', 'writes_merged', 'sectors_written', 'ms_writing', 'current_ios', 'ms_doing_io', 'weighted_ms_doing_io']
    columns_partition = ['major_dev_num', 'minor_dev_num', 'device', 'reads', 'sectors_read', 'writes', 'sectors_written']

    result = {}
    for line in (l for l in open(file_path, 'r').xreadlines() if l != ''):
        parts = line.split()
        if len(parts) == len(columns_disk):
            columns = columns_disk
        elif len(parts) == len(columns_partition):
            columns = columns_partition
        else:
            continue
        data = dict(zip(columns_disk, parts))
        result[data['device']] = dict((k, int(v)) for k, v in data.iteritems() if k != 'device')
    return result

if __name__ == '__main__':
    for device, stats in diskstats().iteritems():
        print device
        for name, stat in stats.iteritems():
            print '    %s: %s' % (name.replace('_', ' '), stat)
```

## 参考

- 容易被误读的IOSTAT: http://linuxperf.com/?p=156
- https://blog.csdn.net/MrSate/article/details/104421383
- https://tech.meituan.com/2017/05/19/about-desk-io.html
- https://gist.github.com/mmalone/1081615/a37b09ce1d6ac6960742444c50f99728bffc9859
