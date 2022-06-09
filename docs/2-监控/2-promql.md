# 2.2 PromQL

## 监控数据模型

prometheus 的监控数据被组织为时间序列数据，每一项由 metric name 和 labels (key-value pairs) 标识，格式为：

    <metric name>{<label name>=<label value>, ...} <value>

example:

    api_http_requests_total{method="POST", handler="/messages"}

    <--------------- metric ---------------------><-timestamp -><-value->
    http_request_total{status="200", method="GET"}@1434417560938 => 94355
    http_request_total{status="200", method="GET"}@1434417561287 => 94334

    http_request_total{status="404", method="GET"}@1434417560938 => 38473
    http_request_total{status="404", method="GET"}@1434417561287 => 38544

    http_request_total{status="200", method="POST"}@1434417560938 => 4748
    http_request_total{status="200", method="POST"}@1434417561287 => 4785

## 数据类型

1. Gauge: 可增可减的任意值，直接反应当前时间的状态
2. Counter: 只增不减，通常需要与 `rate`, `irate` 等函数搭配，计算单位时间发生次数
3. Histogram: 包含自开始监控以来监控样本个数，样本值总和，以及各值区间内样本个数
4. Summary: 包含自监控以来监控样本个数，样本值总和，以及 50%, 90%, 99% 等样本的值

### Counter

    rate(http_requests_total[5m])

    topk(10, http_requests_total)

### Gauge

    delta(cpu_temp_celsius{host="zeus"}[2h])

    predict_linear(node_filesystem_free{job="node"}[1h], 4 * 3600)

### Histogram

### Summary

## promql

表达式类型：

1. 瞬时向量(Instant vector): 一组时间序列，序列的每一项是单独的样本

        [ 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, ... ]

2. 区间向量(Range vector): 一组时间序列，序列的每一项包含一段时间范围内的样本数据

        [ [1, 2], [3, 4], [5, 6], [7, 8], [9, 10], ... ]

3. 标量(Scalar): 一个浮点型的数据值
4. 字符串(String): 一个简单的字符串值

> 只有瞬时向量可以绘制为图表数据

### 瞬时向量

直接以 metrics 名查询

    http_requests_total
    http_requests_total{}

利用标签进行过滤(完全匹配/正则表达式)：

`=` 相同标签

    http_requests_total{instance="localhost:9090"}

`!=` 不相同标签

    http_requests_total{instance!="localhost:9090"}

`=~` 正则表达式匹配

    http_requests_total{environment=~"staging|testing|development", method!="GET"}

`!~` 正则表达式不匹配

    http_requests_total{environment!~"staging|testing|development", method!="GET"}

不含某个标签（以不含 `topic` 标签为例）：

    kafka_server_BrokerTopicMetrics_OneMinuteRate{name="BytesOutPerSec", topic=""}

### 区间向量

时间单位：

* s - 秒
* m - 分钟
* h - 小时
* d - 天
* w - 周
* y - 年

时间位移：

    http_request_total{} # 瞬时向量表达式，选择当前最新的数据
    http_request_total{}[5m] # 区间向量表达式，选择以当前时间为基准，5分钟内的数据

## 聚合操作

作用于瞬时向量，将多组瞬时向量的值聚合，得到新的时间序列

* sum (求和)
* min (最小值)
* max (最大值)
* avg (平均值)
* stddev (标准差)
* stdvar (标准差异)
* count (计数)
* count_values (对 value 进行计数)
* bottomk (样本值最小的 k 个元素)
* topk (样本值最大的k个元素)
* quantile (分布统计)

这些操作符被用于聚合所有标签维度，或者通过 `without` 或者 `by` 子语句来保留不同的维度。

	<aggr-op>([parameter,] <vector expression>) [without|by (<label list>)]

查询系统所有http请求的总量

    sum(http_request_total)

按照 mode 计算主机 CPU 的平均使用时间

    avg(node_cpu) by (mode)

按照主机查询各个主机的 CPU 使用率

    sum(sum(irate(node_cpu{mode!='idle'}[5m]))  / sum(irate(node_cpu[5m]))) by (instance)

`label list` 也可以放在前面

    sum by (job) (matrics{})

## 内置函数

### rate

`rate` 作用于区间向量，得到区间内样本值的增长速率。

对于 Counter 类型的数据，即监控样本值是根据时间的累计值，所得增长速率，也可以视为单位时间内的增量。

特别是对总共耗费时间的累计值，单位时间内的增量，同样可以视为单位时间的占比。

例如：

    node_disk_io_time_seconds_total{device="vdb"}

可以表示磁盘 vdb 发生磁盘 io 时间的累计值，那么

    rate(node_disk_io_time_seconds_total{device="vdb"}[5m])

以 5 分钟为区间，计算区间内花费在磁盘 io 的时间的增长速率，增长速率相当于单位时间花费在磁盘 io 的时间的增长量，同样相当于单位时间内花费在磁盘 io 的占比，也即磁盘使用率 `%util`

### increase

`increase()` 作用于区间向量，获取区间中的第一个样本值和最后一个样本值之间的增长量。

### topk

前 n 条时序

    topk(n, )

### bottomk

后 n 条时序

    bottomk(n, )

