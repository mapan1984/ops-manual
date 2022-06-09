# 2.4.2.2 使用 jmx_exporter 采集 kafka 监控

## 以 javaagent 的方式启动

修改 kafka 的 `kafka-run-class.sh` 文件，增加 `javaagent` 配置：

``` sh
# ...

if [[ $JMX_PORT && -f /usr/local/jmx_prometheus_javaagent-0.16.1.jar && -f /etc/default/jmx_prometheus_javaagent.yaml ]]; then
  JMX_EXPORTER=-javaagent:/usr/local/jmx_prometheus_javaagent-0.16.1.jar=5556:/etc/default/jmx_prometheus_javaagent.yaml
else
  JMX_EXPORTER=""
fi

# ...

# Launch mode
if [ "x$DAEMON_MODE" = "xtrue" ]; then
  nohup $JAVA $KAFKA_HEAP_OPTS $KAFKA_JVM_PERFORMANCE_OPTS $KAFKA_GC_LOG_OPTS $KAFKA_JMX_OPTS $KAFKA_LOG4J_OPTS $JMX_EXPORTER -cp $CLASSPATH $KAFKA_OPTS "$@" > "$CONSOLE_OUTPUT_FILE" 2>&1 < /dev/null &
else
  exec  $JAVA $KAFKA_HEAP_OPTS $KAFKA_JVM_PERFORMANCE_OPTS $KAFKA_GC_LOG_OPTS $KAFKA_JMX_OPTS $KAFKA_LOG4J_OPTS $JMX_EXPORTER -cp $CLASSPATH $KAFKA_OPTS "$@"
fi
```

增加 `javaagent` 配置后可以从 5556 端口获取 jmx metrics：

    $ curl http://localhost:5556/metrics

修改 `prometheus.yml`，增加 job：

``` yaml
scrape_configs:
  - job_name: 'kafka1'
    static_configs:
    - targets: ['10.9.164.4:5556']
  - job_name: 'kafka2'
    static_configs:
    - targets: ['10.9.140.204:5556']
  - job_name: 'kafka3'
    static_configs:
    - targets: ['10.9.145.115:5556']
```

## rules 设置

``` yaml
rules:
  # Special cases and very specific rules
  - pattern : kafka.server<type=(.+), name=(.+), clientId=(.+), topic=(.+), partition=(.*)><>Value
    name: kafka_server_$1_$2
    type: GAUGE
    labels:
      clientId: "$3"
      topic: "$4"
      partition: "$5"
  - pattern : kafka.server<type=(.+), name=(.+), clientId=(.+), brokerHost=(.+), brokerPort=(.+)><>Value
    name: kafka_server_$1_$2
    type: GAUGE
    labels:
      clientId: "$3"
      broker: "$4:$5"

  # Generic per-second counters with 0-2 key/value pairs
  - pattern: kafka.(\w+)<type=(.+), name=(.+)PerSec\w*, (.+)=(.+), (.+)=(.+)><>Count
    name: kafka_$1_$2_$3_total
    type: COUNTER
    labels:
      "$4": "$5"
      "$6": "$7"
  - pattern: kafka.(\w+)<type=(.+), name=(.+)PerSec\w*, (.+)=(.+)><>Count
    name: kafka_$1_$2_$3_total
    type: COUNTER
    labels:
      "$4": "$5"
  - pattern: kafka.(\w+)<type=(.+), name=(.+)PerSec\w*><>Count
    name: kafka_$1_$2_$3_total
    type: COUNTER

  # Generic gauges with 0-2 key/value pairs
  - pattern: kafka.(\w+)<type=(.+), name=(.+), (.+)=(.+), (.+)=(.+)><>Value
    name: kafka_$1_$2_$3
    type: GAUGE
    labels:
      "$4": "$5"
      "$6": "$7"
  - pattern: kafka.(\w+)<type=(.+), name=(.+), (.+)=(.+)><>Value
    name: kafka_$1_$2_$3
    type: GAUGE
    labels:
      "$4": "$5"
  - pattern: kafka.(\w+)<type=(.+), name=(.+)><>Value
    name: kafka_$1_$2_$3
    type: GAUGE

  # Emulate Prometheus 'Summary' metrics for the exported 'Histogram's.
  #
  # Note that these are missing the '_sum' metric!
  - pattern: kafka.(\w+)<type=(.+), name=(.+), (.+)=(.+), (.+)=(.+)><>Count
    name: kafka_$1_$2_$3_count
    type: COUNTER
    labels:
      "$4": "$5"
      "$6": "$7"
  - pattern: kafka.(\w+)<type=(.+), name=(.+), (.+)=(.*), (.+)=(.+)><>(\d+)thPercentile
    name: kafka_$1_$2_$3
    type: GAUGE
    labels:
      "$4": "$5"
      "$6": "$7"
      quantile: "0.$8"
  - pattern: kafka.(\w+)<type=(.+), name=(.+), (.+)=(.+)><>Count
    name: kafka_$1_$2_$3_count
    type: COUNTER
    labels:
      "$4": "$5"
  - pattern: kafka.(\w+)<type=(.+), name=(.+), (.+)=(.*)><>(\d+)thPercentile
    name: kafka_$1_$2_$3
    type: GAUGE
    labels:
      "$4": "$5"
      quantile: "0.$6"
  - pattern: kafka.(\w+)<type=(.+), name=(.+)><>Count
    name: kafka_$1_$2_$3_count
    type: COUNTER
  - pattern: kafka.(\w+)<type=(.+), name=(.+)><>(\d+)thPercentile
    name: kafka_$1_$2_$3
    type: GAUGE
    labels:
      quantile: "0.$4"
```
