# 2.4.1.4 网络监控指标

网卡流入流量速率：

    rate(node_network_receive_bytes_total{device="eth0"}[5m])

网卡流出流量速率：

    rate(node_network_receive_bytes_total{device="eth0"}[5m])

网络包接收速率：

    rate(node_network_receive_packets_total{device="eth0"}[5m])

网络包发送速率：

    rate(node_network_transmit_packets_total{device="eth0"}[5m])

