# 2.4.1.3 磁盘监控指标

## 容量

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
