# 2.4.1.3 磁盘监控指标

## 容量

    $ df
    Filesystem     1K-blocks    Used Available Use% Mounted on
    /dev/vda1       41152716 3936940  35416976  11% /
    /dev/vdb       206292968   62136 195728688   1% /data/disk1

* node_filesystem_avail_bytes: 实际可用空间，相当于 `Available * 1024`
* node_filesystem_free_bytes: 物理可用空间，包括保留空间，会比 node_filesystem_avail_bytes 大一点
* node_filesystem_size_bytes: 总大小，相当于 `1K-blocks * 1024`

### df -h

    100 - ((node_filesystem_avail_bytes{} * 100) / node_filesystem_size_bytes{})

### df -i

    100 - node_filesystem_files_free{}/node_filesystem_files{} * 100

## io 负载

### r/s

    rate(node_disk_reads_completed_total{}[5m])

### w/s

    rate(node_disk_writes_completed_total{}[5m])

### rkB/s

### wkB/s

### %util

磁盘发生 io 时间的累计值，Counter 类型

    node_disk_io_time_seconds_total

磁盘使用率 %util （即单位时间内磁盘发生 io 时间占比）

    rate(node_disk_io_time_seconds_total{}[5m])

### avgqu-sz

    rate(node_disk_io_time_weighted_seconds_total{}[5m])

### r_await (ms)

    rate(node_disk_read_time_seconds_total{}[5m]) / rate(node_disk_reads_completed_total{}[5m]) * 1000

### w_await (ms)

    rate(node_disk_write_time_seconds_total{}[5m]) / rate(node_disk_writes_completed_total{}[5m]) * 1000

## 参考

- https://www.ipcpu.com/2021/04/prometheus-node_exporter/
- https://brian-candler.medium.com/interpreting-prometheus-metrics-for-linux-disk-i-o-utilization-4db53dfedcfc
