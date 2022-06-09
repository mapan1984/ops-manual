# 2.4.1.2 CPU 监控指标

## cpu 时间

    node_cpu_seconds_total

### the percentage of CPU used

`node_cpu_seconds_total{mode="idle"}` 代表空闲时间，指标是 counter 类型，

    100 - (avg by (instance) (rate(node_cpu_seconds_total{mode="idle"}[1m])) * 100)
