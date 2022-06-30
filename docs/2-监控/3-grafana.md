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

## 图标设置

## Graph 类型

### Axes 坐标轴设置

- Unit 选择单位：
    - Data(IEC): 以 2 进制计算，即 1 GiB = 1024 MiB，1 MiB = 1024 KiB，1 KiB = 1024 Byte
    - Data(Metric): 以 10 进制计算，即 1 GB = 1000 MB，1 MB = 1000 KB，1 KB = 1000 Byte

### Legend 图例设置
