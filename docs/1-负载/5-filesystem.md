# 1.5 文件系统

## /proc/mounts

    $ cat /proc/mounts
    sysfs /sys sysfs rw,nosuid,nodev,noexec,relatime 0 0
    proc /proc proc rw,nosuid,nodev,noexec,relatime 0 0
    devtmpfs /dev devtmpfs rw,nosuid,size=8202232k,nr_inodes=2050558,mode=755 0 0
    securityfs /sys/kernel/security securityfs rw,nosuid,nodev,noexec,relatime 0 0
    tmpfs /dev/shm tmpfs rw,nosuid,nodev 0 0
    devpts /dev/pts devpts rw,nosuid,noexec,relatime,gid=5,mode=620,ptmxmode=000 0 0
    tmpfs /run tmpfs rw,nosuid,nodev,mode=755 0 0
    tmpfs /sys/fs/cgroup tmpfs ro,nosuid,nodev,noexec,mode=755 0 0
    cgroup /sys/fs/cgroup/systemd cgroup rw,nosuid,nodev,noexec,relatime,xattr,release_agent=/usr/lib/systemd/systemd-cgroups-agent,name=systemd 0 0
    pstore /sys/fs/pstore pstore rw,nosuid,nodev,noexec,relatime 0 0
    cgroup /sys/fs/cgroup/hugetlb cgroup rw,nosuid,nodev,noexec,relatime,hugetlb 0 0
    cgroup /sys/fs/cgroup/memory cgroup rw,nosuid,nodev,noexec,relatime,memory 0 0
    cgroup /sys/fs/cgroup/net_cls,net_prio cgroup rw,nosuid,nodev,noexec,relatime,net_prio,net_cls 0 0
    cgroup /sys/fs/cgroup/perf_event cgroup rw,nosuid,nodev,noexec,relatime,perf_event 0 0
    cgroup /sys/fs/cgroup/pids cgroup rw,nosuid,nodev,noexec,relatime,pids 0 0
    cgroup /sys/fs/cgroup/blkio cgroup rw,nosuid,nodev,noexec,relatime,blkio 0 0
    cgroup /sys/fs/cgroup/cpu,cpuacct cgroup rw,nosuid,nodev,noexec,relatime,cpuacct,cpu 0 0
    cgroup /sys/fs/cgroup/devices cgroup rw,nosuid,nodev,noexec,relatime,devices 0 0
    cgroup /sys/fs/cgroup/cpuset cgroup rw,nosuid,nodev,noexec,relatime,cpuset 0 0
    cgroup /sys/fs/cgroup/freezer cgroup rw,nosuid,nodev,noexec,relatime,freezer 0 0
    configfs /sys/kernel/config configfs rw,relatime 0 0
    /dev/vda1 / ext4 rw,relatime,data=ordered 0 0
    mqueue /dev/mqueue mqueue rw,relatime 0 0
    debugfs /sys/kernel/debug debugfs rw,relatime 0 0
    systemd-1 /proc/sys/fs/binfmt_misc autofs rw,relatime,fd=34,pgrp=1,timeout=0,minproto=5,maxproto=5,direct,pipe_ino=9039 0 0
    hugetlbfs /dev/hugepages hugetlbfs rw,relatime 0 0
    /dev/vdb /data/disk1 ext4 rw,relatime,data=ordered 0 0
    binfmt_misc /proc/sys/fs/binfmt_misc binfmt_misc rw,relatime 0 0
    tmpfs /run/user/0 tmpfs rw,nosuid,nodev,relatime,size=1642524k,mode=700 0 0s

每一列字段含义为：

* device
* mount point
* file-system type
* read-only / read-write
* dummy values

## 空间大小

``` java
File file = new File("/");

// 总空间，单位 byte
long total = file.getTotalSpace();

// 可用空间，单位 byte
long usable = file.getUsableSpace();
```

## 参考

- https://github.com/prometheus/node_exporter/blob/v1.3.1/collector/filesystem_linux.go
