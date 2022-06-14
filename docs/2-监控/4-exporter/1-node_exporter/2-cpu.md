# 2.4.1.2 CPU 监控指标

## cpu 时间

表示 cpu 时间，指标是 counter 类型

    node_cpu_seconds_total

表示 cpu 空闲时间，指标是 counter 类型

    node_cpu_seconds_total{mode="idle"}

表示单位时间内，cpu 空闲时间占比

    rate(node_cpu_seconds_total{mode="idle"}[5m])

以实例划分，计算每个实例所有 cpu 核 的空闲时间占比

    avg by (instance) (rate(node_cpu_seconds_total{mode="idle"}[5m]))

### the percentage of CPU used

    100 - (avg by (instance) (rate(node_cpu_seconds_total{mode="idle"}[5m])) * 100)
