# 1.1 内存

参考：https://mapan1984.github.io/manual/2019/04/28/linux-运维问题/#free-解释

### free 解释

    $ free -g
                 total       used       free     shared    buffers     cached
    Mem:           125        122          3          0          0         99
    -/+ buffers/cache:         22        102
    Swap:            0          0          0


* `total`: 总内存大小
* `used`: 已经使用的内存
* `free`: 空闲的内存
* `shared`: 多个进程共享的内存总额
* `buffers`: 块缓存(buffer cache)大小，缓存块设备的块数据（如磁盘）
* `cached`: 页缓存(page cache)大小，缓存文件的页数据
* `-buffers/cache`: 已用的内存数，不包含磁盘缓存，`used - buffers - cached`
* `+buffers/cache`: 可用的内存数，包含磁盘缓存，`free + buffers + cached`

这里的 `buffers`, `cached` 是磁盘缓存，这部分内存也可以被进程申请到，所以计算可用的内存时，应该用 `+buffers/cache`，即 `free + buffers + cached`

实际被进程占用的内存，应该去除磁盘缓存，即 `-buffers/cache`，即 `used - buffers - cached`

> 在 Linux 2.4 版本的内核之前，page cache 与 buffer cache 是完全分离的。但是，块设备大多是磁盘，磁盘上的数据又大多通过文件系统来组织，这种设计导致很多数据被缓存了两次，浪费内存。所以在 2.4 版本内核之后，两块缓存近似融合在了一起：如果一个文件的页加载到了 page cache，那么同时 buffer cache 只需要维护块指向页的指针就可以了。只有那些没有文件表示的块，或者绕过了文件系统直接操作（如dd命令）的块，才会真正放到 buffer cache 里。因此，我们现在提起 page cache，基本上都同时指 page cache 和 buffer cache 两者。

### page_cache

linux 使用 page cache 增加对磁盘访问的性能，page cache 在内存中缓存了磁盘上的部分数据。

1. 对读请求，系统首先会检查所读页面是否在 cache 中:
    1. 如果在 cache 中，那么直接从内存中读取，不需要访问磁盘。
    2. 如果不在 cache 中，那么从磁盘中读取所请求的页面，并 **预读** 几个相邻的页面（局部性原理），缓存在 cache 中。
2. 对写请求，数据直接写入 cache，磁盘内容不会直接更新（断电有丢失数据风险）写入的 page 被标记为 dirty，内核会周期的将 dirty page **写回** 磁盘。


相关参数：

    # dirty page 大小达到多少字节后开始触发刷磁盘
    vm.dirty_background_bytes = 0

    # dirty page 内存占比超过 dirty_background_ratio 后开始触发刷磁盘（默认 10%）
    vm.dirty_background_ratio = 10

    # dirty page 大小达到多少字节后停止接收写请求，开始触发刷磁盘
    vm.dirty_bytes = 0

    # dirty page 内存占比超过 dirty_ratio 后停止接收写请求，开始触发刷磁盘（默认 30%）
    vm.dirty_ratio = 30

    # 存在时间超过 dirty_expire_centisecs 的 dirty page 会被周期性刷盘操作写会磁盘（默认 30秒）
    vm.dirty_expire_centisecs = 3000

    # 多长时间唤醒一次周期性的刷盘操作（默认 5秒）
    vm.dirty_writeback_centisecs = 500

### 清除 cache

仅清除页面缓存（PageCache）

    $ sync; echo 1 > /proc/sys/vm/drop_caches

清除目录项和inode

    $ sync; echo 2 > /proc/sys/vm/drop_caches

清除页面缓存，目录项和inode

    $ sync; echo 3 > /proc/sys/vm/drop_caches

当内存小于 90G 时自动清理：

``` bash
#!/bin/bash

#
# /etc/cron.hourly/drop_caches.sh
#

free_mem=$(free -g | awk '/^Mem/ {print $4}')
echo "$(date +"%Y-%m-%d-%T") - before free_mem: $free_mem" >> /root/drop_caches.log 2>&1

if [[ free_mem -lt 90 ]]; then
  echo "$(date +"%Y-%m-%d-%T") - drop caches" >> /root/drop_caches.log 2>&1
  sync; echo 1 > /proc/sys/vm/drop_caches
else
  echo "$(date +"%Y-%m-%d-%T") - do  nothing" >> /root/drop_caches.log 2>&1
fi

free_mem=$(free -g | awk '/^Mem/ {print $4}')
echo "$(date +"%Y-%m-%d-%T") -  after free_mem: $free_mem" >> /root/drop_caches.log 2>&1
```
