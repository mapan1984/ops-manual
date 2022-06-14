# 2.4.1.1 内存监控指标

总内存大小

    node_memory_MemTotal_bytes

空闲内存大小

    node_memory_MemFree_bytes

cached 占用大小

    node_memory_Cached_bytes

buffer 占用大小

    node_memory_Buffers_bytes

内存使用率（进程占用的内存）

    (node_memory_MemTotal_bytes - (node_memory_MemFree_bytes + node_memory_Buffers_bytes + node_memory_Cached_bytes)) / node_memory_MemTotal_bytes * 100

