# 1.4 网络

    netstat -s | grep -E 'overflow|drop'

    nstat -az | grep -E 'TcpExtListenOverflows|TcpExtListenDrops'

## /proc/net/dev

    $ cat /proc/net/dev
    Inter-|   Receive                                                |  Transmit
     face |bytes    packets errs drop fifo frame compressed multicast|bytes    packets errs drop fifo colls carrier compressed
      eth0: 1690625101 14339695    0    0    0     0          0         0 2959063809 14364497    0    0    0     0       0          0
        lo: 1747298251 6994514    0    0    0     0          0         0 1747298251 6994514    0    0    0     0       0          0

* `bytes`: The total number of bytes of data transmitted or received by the interface.（接口发送或接收的数据的总字节数）
* `packets`: The total number of packets of data transmitted or received by the interface.（接口发送或接收的数据包总数）
* `errs`: The total number of transmit or receive errors detected by the device driver.（由设备驱动程序检测到的发送或接收错误的总数）
* `drop`: The total number of packets dropped by the device driver.（设备驱动程序丢弃的数据包总数）
* `fifo`: The number of FIFO buffer errors.（FIFO缓冲区错误的数量）
* `compressed`: The number of compressed packets transmitted or received by the device driver. (This appears to be unused in the 2.2.15 kernel.)（设备驱动程序发送或接收的压缩数据包数）
* `frame`: The number of packet framing errors.（分组帧错误的数量）
* `colls`: The number of collisions detected on the interface.（接口上检测到的冲突数）
* `carrier`: The number of carrier losses detected by the device driver.（由设备驱动程序检测到的载波损耗的数量）
* `multicast`: The number of multicast frames transmitted or received by the device driver.（设备驱动程序发送或接收的多播帧数）

``` python
lines = open("/proc/net/dev", "r").readlines()

columnLine = lines[1]
_, receiveCols , transmitCols = columnLine.split("|")
receiveCols = map(lambda a: "recv_" + a, receiveCols.split())
transmitCols = map(lambda a: "trans_" + a, transmitCols.split())

cols = receiveCols + transmitCols

faces = {}
for line in lines[2:]:
    if line.find(":") < 0:
        continue
    face, data = line.split(":")
    faceData = dict(zip(cols, data.split()))
    faces[face] = faceData

import pprint
pprint.pprint(faces)
```

## 参考

- https://github.com/prometheus/node_exporter/blob/v1.3.1/collector/netdev_linux.go
