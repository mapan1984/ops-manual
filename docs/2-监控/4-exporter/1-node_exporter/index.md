# 2.4.1 node_exporter

node_exporter 可以采集机器的基础监控，如 cpu，内存等信息。

    $ wget https://github.com/prometheus/node_exporter/releases/download/v0.17.0/node_exporter-0.17.0.linux-amd64.tar.gz
    $ tar zxvf node_exporter-0.17.0.linux-amd64.tar.gz
    $ cd node_exporter-0.17.0.linux-amd64
    $ ./node_exporter

    $ curl http://localhost:9100/metrics


修改 `prometheus.yml`，增加 job：

``` yaml
scrape_configs:
  - job_name: 'node'
    static_configs:
    - targets: ['localhost:9100']
```

