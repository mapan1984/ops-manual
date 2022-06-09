# 2.4 exporter

prometheus 配置的 `scrape_configs` 中的 `targets` 的每一项都是由 exporter 暴露的地址，prometheus 会通过这个地址从 exporter 拉取监控数据

