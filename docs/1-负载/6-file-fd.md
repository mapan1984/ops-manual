# 1.6 文件系统

## /proc/sys/fs/

    $ cat /proc/sys/fs/file-nr
    1920    0    6553500

表示系统支持最多文件数为 `6553500` 个，当前已用 `1920` 个文件


## 参考

- https://www.robustperception.io/kernel-file-descriptor-metrics-from-the-node-exporter/
- https://www.robustperception.io/dealing-with-too-many-open-files/
- https://www.orchome.com/1445
- https://github.com/prometheus/node_exporter/blob/v1.3.1/collector/filefd_linux.go
