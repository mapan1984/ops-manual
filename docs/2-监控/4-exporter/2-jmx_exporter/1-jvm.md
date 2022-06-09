# 2.4.2.1 使用 jmx_exporter 采集 jvm 监控

## gc

java gc 的 MBean 对象是 [GarbageCollectorMXBean](https://docs.oracle.com/javase/8/docs/api/java/lang/management/GarbageCollectorMXBean.html)

* `jvm_gc_collection_seconds_count` 表示 gc 发生的次数，累计值（Counter）
* `jvm_gc_collection_seconds_sum` 表示 gc 消耗的时长，累计值（Counter）

### 每秒发生的 gc 次数

    rate(jvm_gc_collection_seconds_count[2m])

### 每秒耗费在 gc 的时长

    rate(jvm_gc_collection_seconds_sum[2m])

### 每次 gc 耗费的时长 

    rate(jvm_gc_collection_seconds_sum[2m]) / rate(jvm_gc_collection_seconds_count[2m])

## memory

### 堆空间使用率

    jvm_memory_bytes_used{area="heap"} / jvm_memory_bytes_max{area="heap"} * 100

## 参考

- https://www.robustperception.io/measuring-java-garbage-collection-with-prometheus
