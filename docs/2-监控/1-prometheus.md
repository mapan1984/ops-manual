# 2.1 Prometheus

## 下载运行

    $ wget https://github.com/prometheus/prometheus/releases/download/v2.8.1/prometheus-2.8.1.linux-amd64.tar.gz
    $ tar zxvf prometheus-2.8.1.linux-amd64.tar.gz
    $ cd prometheus-2.8.1.linux-amd64/
    $ ./prometheus --config.file=prometheus.yml

    $ curl http://localhost:9090

配置文件默认采集 prometheus 服务自身信息：

``` yaml
# my global config
global:
  scrape_interval:     15s # Set the scrape interval to every 15 seconds. Default is every 1 minute.
  evaluation_interval: 15s # Evaluate rules every 15 seconds. The default is every 1 minute.
  # scrape_timeout is set to the global default (10s).

# Alertmanager configuration
alerting:
  alertmanagers:
  - static_configs:
    - targets:
      # - alertmanager:9093

# Load rules once and periodically evaluate them according to the global 'evaluation_interval'.
rule_files:
  # - "first_rules.yml"
  # - "second_rules.yml"

# A scrape configuration containing exactly one endpoint to scrape:
# Here it's Prometheus itself.
scrape_configs:
  # The job name is added as a label `job=<job_name>` to any timeseries scraped from this config.
  - job_name: 'prometheus'

    # metrics_path defaults to '/metrics'
    # scheme defaults to 'http'.

    static_configs:
    - targets: ['localhost:9090']
```

## 运行参数

可以通过命令行启动参数修改数据存储位置，服务端口等

    $ ./prometheus --config.file=./prometheus.yml --storage.tsdb.path=/data/prometheus --web.listen-address=:8080

| 启动参数                                     | 默认值 | 含义                        |
|----------------------------------------------|--------|-----------------------------|
| --storage.tsdb.path                          | data/  | tsdb 数据存储位置           |
| --storage.tsdb.retention.time                | 15d    | tsdb 数据保留时间(过期时间) |
| --storage.tsdb.retention.size (EXPERIMENTAL) | 0      | tsdb 数据保留大小           |

| 启动参数             | 默认值 | 含义     |
|----------------------|--------|----------|
| --web.listen-address |        | 监听地址 |

| 启动参数                          | 默认值 | 含义                                                                                              |
|-----------------------------------|--------|---------------------------------------------------------------------------------------------------|
| --storage.tsdb.min-block-duration | 2h     | The timestamp range of head blocks after which they get persisted                                 |
| --storage.tsdb.max-block-duration | 36h    | The maximum timestamp range of compacted blocks,It's the minimum duration of any persisted block. |
| --storage.tsdb.no-lockfile        | false  | Do not create lockfile in data directory                                                          |

