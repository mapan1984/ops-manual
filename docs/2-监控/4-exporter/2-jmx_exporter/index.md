# 2.4.2 jmx_exporter

[jmx_exporter](https://github.com/prometheus/jmx_exporter) 可以采集 java 程序通过 jmx 暴露的数据。

有 2 种运行方式，可以作为 javaagent 运行，也可以作为单独的 http server 运行，作为 javaagent 运行时可以额外采集到进程的 cpu，memory 等信息。

## 配置

在介绍 2 种运行方式之前先介绍配置文件，配置项是统一的。

jmx_exporter 的配置文件 `config.yaml`：

``` yaml
---
startDelaySeconds: 0

# 连接 JMX 的地址
# hostPort: 0.0.0.0:9999

# 如果 JMX 需要通过用户名密码认证，这这里设置
username:
password:

# 完整的 JMX URL，如果设置了 hostPort 则不需要设置 jmxUrl
jmxUrl: service:jmx:rmi:///jndi/rmi://0.0.0.0:9999/jmxrmi

# 0.17 之后，如果以 javaagent 运行，则 hostPort 与 jmxUrl 都不需要设置

# 如果 JMX 通过 SSL 访问，设置为 true，同时需要通过以下 system properties 设置证书信息：
#   -Djavax.net.ssl.keyStore=/home/user/.keystore
#   -Djavax.net.ssl.keyStorePassword=changeit
#   -Djavax.net.ssl.trustStore=/home/user/.truststore
#   -Djavax.net.ssl.trustStorePassword=changeit
ssl: false

# 是否将监控项 name, label 转换为小写
lowercaseOutputName: false
lowercaseOutputLabelNames: false

# 暴露的 MBean 的 ObjectName 的白名单列表
whitelistObjectNames: ["org.apache.cassandra.metrics:*", "kafka.*:*"]
# 不暴露的 MBean 的 ObjectName 的黑名单列表，优先级高于 whitelistObjectNames
blacklistObjectNames: ["org.apache.cassandra.metrics:type=ColumnFamily,*"]

# ObjectName 到监控项的转换规则，下面详细介绍，如果为空则采用默认规则
rules:
  - pattern: 'org.apache.cassandra.metrics<type=(\w+), name=(\w+)><>Value: (\d+)'
    name: cassandra_$1_$2
    value: $3
    valueFactor: 0.001
    labels: {}
    help: "Cassandra metric $1 $2"
    type: GAUGE
    attrNameSnakeCase: false
```

## javaagent 运行方式

下载运行：

    $ wget https://repo1.maven.org/maven2/io/prometheus/jmx/jmx_prometheus_javaagent/0.16.1/jmx_prometheus_javaagent-0.16.1.jar

在运行 `youJar.jar` 时将 jmx_exporter 作为 javaagent 运行，这里 8080 为 jmx_exporter 暴露端口，config.yaml 为 jmx_exporter 配置文件：

    $ java -javaagent:./jmx_prometheus_javaagent-0.16.1.jar=8080:config.yaml -jar yourJar.jar

测试访问：

    $ curl http://localhost:8080/metrics

## httpserver 运行方式

    $ git clone https://github.com/prometheus/jmx_exporter.git
    $ cd jmx_exporter/jmx_prometheus_httpserver
    $ mvn package

得到 target/jmx_prometheus_httpserver-0.16.2-SNAPSHOT-jar-with-dependencies.jar

## rules 详细介绍

JMX `MBean` 对象需要以一个 `ObjectName` 作为定位标记，`ObjectName` 包含 `domain` 与 `properties` 2 部分，jmx_exporter 配置中的 `rules` 就是将 `ObjectName` 转换为 prometheus 接受的上报格式。

默认的转换规则下

    domain:<beanpropertyName1=beanPropertyValue1, beanpropertyName2=beanPropertyValue2, ...><key1, key2, ...>:attrName=value

将被转换为

    domain_beanPropertyValue1_key1_key2_...keyN_attrName{beanpropertyName2="beanPropertyValue2", ...}: value

例如

    kafka.server:<type=BrokerTopicMetrics,name=BytesInPerSec>:OneMinuteRate=343

将被转换为：

    kafka_server_BrokerTopicMetrics_OneMinuteRate{name="BytesInPerSec"}: 343

* domain: `kafka.server -> kafka_server`
* beanPropertyValue1: `BrokerTopicMetrics`
* attrName: `OneMinuteRate`
