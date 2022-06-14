# 2.3 Grafana

grafana 可以获取 prometheus 数据，提供监控图表。

下载安装：

    $ wget https://dl.grafana.com/oss/release/grafana-6.0.2-1.x86_64.rpm
    $ sudo yum localinstall grafana-6.0.2-1.x86_64.rpm

运行：

    $ service grafana-server start

开机自启：

    $ /sbin/chkconfig --add grafana-server

- 默认端口：3000
- default user: admin
- default password: admin

## 配置

配置文件：/etc/grafana/grafana.ini

## 管理

管理脚本：/etc/init.d/grafana-server

## 数据

granfana 数据目录：/var/lib/grafana
