# 监控指标参考

[← ShimoDocs Suite 部署文档](../README.md)

本文档整理了监控系统中常用的指标，涵盖节点、containerd 容器、 Kubernetes 集群、中间件和应用服务，为日常巡检、容量评估和故障排查提供统一参考。 

指标名称基于 Prometheus 收集的实际 exporter 指标。不同版本的 exporter 可能存在细微差异，实际排查应以线上查询结果为最终参考。 

## 范围 

| 类别 | 覆盖对象 | 
| --- | --- | 
| 节点监控 | Linux 主机、系统资源、磁盘、网络、进程 | 
| 容器监控 | 运行在 containerd 上的容器、Pod 容器资源 | 
| Kubernetes 集群 | 节点、Pod、Deployment、StatefulSet、Job、 PVC、APIServer | 
| MySQL | MySQL 实例、连接、查询、缓存、锁、网络 | 
| MongoDB | MongoDB 实例、连接、操作、内存、网络、复制缓冲 | 
| Redis | Redis 实例，客户端，命令，内存，键空间，命中率 | 
| Kafka | 代理，主题，分区，消费者组，延迟，副本 | 
| MinIO | 集群节点，磁盘，桶 S3 请求，对象容量 | 
| Elasticsearch | 集群健康，节点，分片，索引 JVM，线程池，网络 |
| 应用服务 | 通用服务器，客户端调用，协作编辑，RS服务，运行时 |

## 指标读取规则

| 指标类型 | 读取方法 | 常用PromQL语法 | 说明 |
| --- | --- | --- | --- |
| 计数器 | 查看时间窗口内的增长率或增量 | `rate(x_total[5m])`, `increase(x_total[5m])` | 请求次数，错误次数，字节数，IO次数通常属于计数器 |
| 仪表盘 | 查看当前值，平均值，最大值 | `avg(x)`, `max(x)`, `sum(x)` | 内存，连接数，容量，状态值通常属于仪表盘 |
| 直方图 | 查看百分位延迟 | `histogram_quantile(0.95, sum(rate(x_bucket[5m])) by (le))` | 请求延迟，处理延迟，队列延迟通常使用直方图 |
| 比率 | 查看百分比 | `A / B * 100` | 利用率，错误率，命中率都属于比率型指标 |

建议不要直接复制固定数字作为阈值。例如指标如 CPU，内存、磁盘、连接数、 QPS，以及延迟应在业务高峰期、容量规划和历史基线的背景下进行评估。文档中的异常行为用于快速识别风险，并不等同于最终告警阈值。

## 1. 节点服务监控

节点监控用于确定主机是否健康、资源是否充足，以及是否存在磁盘或网络瓶颈。节点指标主要来自 node-exporter，并结合系统进程仪表盘进行进程级定位。

### 1.1 基本状态

| 监控维度 | 指标 | 指标含义 | 通用标准/单位 | 异常表现 |
| --- | --- | --- | --- | --- |
| 节点存活 | `up` | exporter 或采集目标是否可访问 | `1` 表示可采集， `0` 表示不可采集 | 持续 `0` 表示节点、网络或 exporter 存在问题 |
| 启动时间 | `node_boot_time_seconds` | 节点的上次启动时间 | Unix 时间戳 | 启动时间的变化表示节点已重启 |
| 节点信息 | `node_uname_info`, `node_os_info` | 操作系统、内核及发行版信息 | 标签信息 | 用于验证节点版本，不直接作为告警指标 |

故障排除建议：先检查 `up` ，然后 `node_boot_time_seconds`。如果节点不可采集且启动时间最近发生变化，应优先确认主机重启、网络 ACL和 node-exporter 进程状态。

### 1.2 CPU 指标

| 监控维度 | 指标 | 指标含义 | 通用标准/单位 | 异常表现 |
| --- | --- | --- | --- | --- |
| CPU 使用情况 | `node_cpu_seconds_total` | 每个核心在不同模式下累计时间 CPU 每个核心在不同模式下的累计时间 | 百分比 | `user` 以及 `system` 长期保持高位，节点计算能力紧张 |
| 空闲 CPU | `node_cpu_seconds_total{mode="idle"}` | CPU 空闲时间 | 百分比 | 空闲时间持续偏低，可能导致排队和延迟增加 |
| IO 等待 | `node_cpu_seconds_total{mode="iowait"}` | 时间 CPU 等待磁盘 IO | 百分比 | iowait 持续增加通常表示磁盘或存储链路较慢 |
| 系统负载 | `node_load1`, `node_load5`, `node_load15` | 1/5/15 分钟平均负载 | 负载值 | 负载持续高于核心数表示任务排队明显 CPU 核心 |
| CPU 压力 | `node_pressure_cpu_waiting_seconds_total` | 累计 CPU PSI 等待时间 | 秒/秒 | CPU 资源争用严重，进程在等待 CPU 调度 |

常用查询:

```promql
100 - avg by (instance) (rate(node_cpu_seconds_total{mode="idle"}[5m])) * 100
```

```promql
avg by (instance) (rate(node_cpu_seconds_total{mode="iowait"}[5m])) * 100
```

调查建议：当 CPU 使用率高时，首先区分 `user`, `system`和 `iowait`。高 `user` 主要由于业务计算压力，高 `system` 可能与系统调用和网络数据包处理相关，而高 `iowait` 需要检查磁盘吞吐量， IOPS和延迟。

### 1.3 内存指标

| 监控维度 | 指标 | 指标含义 | 常用单位 | 异常表现 |
| --- | --- | --- | --- | --- |
| 总内存 | `node_memory_MemTotal_bytes` | 节点的总物理内存 | 字节 | 用于计算使用率 |
| 可用内存 | `node_memory_MemAvailable_bytes` | 系统可分配给进程的内存 | 字节 / 百分比 | 可用内存持续低容易触发 OOM 或频繁回收 |
| 空闲内存 | `node_memory_MemFree_bytes` | 完全未使用的内存 | 字节 | 在 Linux 中不能单独用来判断内存压力 |
| 内存压力 | `node_pressure_memory_waiting_seconds_total` | 累计内存 PSI 等待时间 | 秒 / 秒 | 内存回收或分配等待增加 |
| OOM 计数 | `node_vmstat_oom_kill` | 系统的数量 OOM 杀死 | 计数 / 增量 | 当其增加时，识别被杀的进程和内存峰值 |

常用查询:

```promql
(1 - node_memory_MemAvailable_bytes / node_memory_MemTotal_bytes) * 100
```

```promql
increase(node_vmstat_oom_kill[10m])
```

调查建议：不要仅仅查看 `MemFree` 以了解内存情况。实际可用性应更多地通过 `MemAvailable`评估，结合容器工作集内存、进程 RSS和 OOM 记录。

### 1.4 磁盘容量和Inodes

| 监控维度 | 指标 | 指标含义 | 常用测量/单位 | 异常表现 |
| --- | --- | --- | --- | --- |
| 文件系统总量 | `node_filesystem_size_bytes` | 挂载点的总容量 | 字节 | 用于计算磁盘使用率 |
| 可用文件系统 | `node_filesystem_avail_bytes` | 普通用户可用空间 | 字节 | 可用空间不足可能导致写入失败 |
| 空闲文件系统 | `node_filesystem_free_bytes` | 文件系统中的空闲空间 | 字节 | 包括保留给root的空间；通常与 `avail` |
| 只读状态一起考虑 | `node_filesystem_readonly` | 文件系统是否为只读 | `0/1` | 当 `1`，业务写入将会失败 |
| Inodes总数 | `node_filesystem_files` | 文件系统中Inodes的总数 | 计数 | 在小文件场景中需要特别关注 |
| 剩余Inodes | `node_filesystem_files_free` | 剩余Inodes的数量 | 数量/百分比 | 当Inodes耗尽时，即使仍有磁盘空间也无法创建文件 |

常用查询:

```promql
(1 - node_filesystem_avail_bytes{fstype!~"tmpfs|overlay"} / node_filesystem_size_bytes{fstype!~"tmpfs|overlay"}) * 100
```

```promql
(1 - node_filesystem_files_free / node_filesystem_files) * 100
```

调查建议：磁盘容量警报应按挂载点检查，特别是数据磁盘、日志磁盘和容器运行时目录。高 inode 使用率通常来自大量小文件、日志切片或未清理的临时文件。

### 1.5 磁盘 IOPS，吞吐量和延迟

| 监控维度 | 指标 | 指标含义 | 常用测量/单位 | 异常表现 |
| --- | --- | --- | --- | --- |
| 读取 IOPS | `node_disk_reads_completed_total` | 已完成的磁盘读取请求次数 | 次/秒 | 读取 IOPS 接近设备限制时，读取延迟增加 |
| 写入 IOPS | `node_disk_writes_completed_total` | 已完成的磁盘写入请求次数 | 次/秒 | 写入积压，日志或数据库提交变慢 |
| 读取吞吐量 | `node_disk_read_bytes_total` | 从磁盘读取的累计字节数 | 字节/秒 | 高吞吐量和高 iowait 表明存储繁忙 |
| 写入吞吐量 | `node_disk_written_bytes_total` | 写入磁盘的累计字节数 | 字节/秒 | 长期高写入吞吐量可能影响数据库和对象存储 |
| 读取时间 | `node_disk_read_time_seconds_total` | 读取请求的累计时间 | 秒/秒 | 读取延迟增加 |
| 写入时间 | `node_disk_write_time_seconds_total` | 写入请求的累计时间 | 秒/秒 | 写入延迟增加 |
| IO 繁忙 | `node_disk_io_time_seconds_total` | 磁盘处理 IO 的累计时间 | 百分比 | 在接近满负载时，应用程序会等待 IO |
| 加权 IO 时间 | `node_disk_io_time_weighted_seconds_total` | 考虑队列长度的 IO 时间 | 秒/秒 | 队列积压表明设备队列严重 |
| IO 压力 | `node_pressure_io_waiting_seconds_total` | 累计 IO PSI 等待时间 | 秒/秒 | 进程等待 IO 的时间更长 |

常见查询：

```promql
rate(node_disk_reads_completed_total[5m])
```

```promql
rate(node_disk_writes_completed_total[5m])
```

```promql
rate(node_disk_read_bytes_total[5m])
```

```promql
rate(node_disk_written_bytes_total[5m])
```

```promql
rate(node_disk_io_time_seconds_total[5m]) * 100
```

```promql
rate(node_disk_read_time_seconds_total[5m]) / rate(node_disk_reads_completed_total[5m])
```

```promql
rate(node_disk_write_time_seconds_total[5m]) / rate(node_disk_writes_completed_total[5m])
```

调查建议：检查问题时不要只看磁盘容量。即使容量正常，当 IOPS吞吐量、IO 繁忙和 iowait 同时增加时，业务性能也会下降。像数据库这样的重 IO 服务， Kafka和 MinIO 应关注写入延迟和队列。

### 1.6 网络指标

| 监控维度 | 指标 | 指标含义 | 常用单位 | 异常迹象 |
| --- | --- | --- | --- | --- |
| 入站流量 | `node_network_receive_bytes_total` | 网卡接收的累计字节数 | 字节/秒 | 入站流量突然增加，可能是由于请求激增或数据同步所致 |
| 出站流量 | `node_network_transmit_bytes_total` | 网卡发送的累计字节数 | 字节/秒 | 出站流量突然增加，可能是由于下载、备份或复制所致 |
| 入站错误 | `node_network_receive_errs_total` | 接收错误数据包的累计数量 | 计数/秒 | 网卡、链路或驱动问题 |
| 发送错误 | `node_network_transmit_errs_total` | 累计发送错误包数量 | 计数/秒 | 链路问题或网卡队列问题 |
| 接收包丢失 | `node_network_receive_drop_total` | 累计接收丢弃包数量 | 计数/秒 | 内核队列或网卡无法跟上 |
| 发送包丢失 | `node_network_transmit_drop_total` | 发送包丢失累计值 | 次/秒 | 出口拥塞或 NIC 队列压力 |

常见查询：

```promql
rate(node_network_receive_bytes_total{device!~"lo|veth.*|cni.*"}[5m])
```

```promql
rate(node_network_transmit_bytes_total{device!~"lo|veth.*|cni.*"}[5m])
```

```promql
rate(node_network_receive_drop_total[5m]) + rate(node_network_transmit_drop_total[5m])
```

调查建议：对于网络异常，应同时查看流量、错误包和丢包情况。单独的高流量不一定表示故障；高流量伴随错误包或丢包更可能是链路或主机网络栈问题。

### 1.7 TCP，文件句柄和系统压力

| 监控维度 | 指标 | 指标含义 | 常用单位/测量 | 异常行为 |
| --- | --- | --- | --- | --- |
| 当前 TCP 连接 | `node_netstat_Tcp_CurrEstab` | 当前已建立的连接数 TCP 连接 | 数量 | 连接数量突然增加可能表示流量高峰或连接泄漏 |
| TIME_WAIT | `node_sockstat_TCP_tw` | 数量 TIME_WAIT 连接 | 数量 | 过多短连接可能耗尽端口或增加内核压力 |
| TCP 分配的 | `node_sockstat_TCP_alloc` | 分配数量 TCP 套接字 | 数量 | 套接字数量持续增加需要调查连接释放情况 |
| TCP 使用中 | `node_sockstat_TCP_inuse` | 数量 TCP 使用中的套接字 | 数量 | 增加的连接压力 |
| TCP 孤立 | `node_sockstat_TCP_orphan` | 孤立套接字数量 | 数量 | 异常增加可能与异常连接关闭有关 |
| 文件句柄使用情况 | `node_filefd_allocated` | 系统分配的文件句柄数量 | 个 | 过高可能影响新连接和文件打开 |
| 文件句柄限制 | `node_filefd_maximum` | 系统文件句柄限制 | 个 | 用于计算句柄使用率 |

常见查询： 

```promql
node_filefd_allocated / node_filefd_maximum * 100
```

调查建议：文件句柄和 TCP 连接通常一起考虑。当服务器连接数量激增，如果系统句柄接近其限制，应用程序可能会出现接受失败、文件打开失败或依赖连接失败的情况。

### 1.8 进程监控

| 监控维度 | 指标 | 指标含义 | 常用测量/单位 | 异常行为 |
| --- | --- | --- | --- | --- |
| 进程 CPU | `process_cpu_seconds_total` | 总计 CPU 进程时间 | 秒/秒 | 长期高占用 CPU 单个进程使用 |
| 物理内存 | `process_resident_memory_bytes` | 进程 RSS 内存 | 字节 | 持续增长 RSS 可能表明内存泄漏 |
| 虚拟内存 | `process_virtual_memory_bytes` | 进程虚拟内存 | 字节 | 异常增长需要与以下内容一同评估 RSS |
| 打开的句柄 | `process_open_fds` | 进程打开的文件句柄数量 | 数量 | 持续增长可能表示句柄泄漏 |
| 最大句柄数 | `process_max_fds` | 进程可以打开的最大文件句柄数量 | 数量 | 用于计算进程句柄使用率 |
| 进程启动时间 | `process_start_time_seconds` | 进程启动时间 | Unix 时间戳 | 启动时间变化表示进程重启 |

调查建议：进程指标用于定位节点级别问题的具体服务。当节点 CPU 较高时，请检查进程 CPU；当节点内存压力较高时，请检查 RSS；当节点句柄数量较高时，请检查 `process_open_fds`. 

## 2. containerd 容器监控

容器监控主要来自 kubelet/cAdvisor，反映 containerd 管理的容器资源使用情况。文档继续使用 `container_*` Prometheus 指标的命名，但在实际运行中底层容器运行时是 containerd。 

### 2.1 容器 CPU

| 监控维度 | 指标 | 指标含义 | 通用范围/单位 | 异常表现 |
| --- | --- | --- | --- | --- |
| CPU 使用情况 | `container_cpu_usage_seconds_total` | 总计 CPU 容器使用时间 | 核/秒 | 使用率长时间接近上限，可能导致业务延迟增加 |
| CPU 受限时间 | `container_cpu_cfs_throttled_seconds_total` | 总时间 CPU 被限制于 CFS | 秒/秒 | 显著 CPU 限制表明上限过紧或负载过高 |
| CPU 配额 | `container_spec_cpu_quota` | 容器 CPU 配额 | 配额值 | 用于识别是否有 CPU 设置了限制 |

常见查询： 

```promql
sum by (namespace, pod, container) (rate(container_cpu_usage_seconds_total{container!="",image!=""}[5m]))
```

```promql
sum by (namespace, pod, container) (rate(container_cpu_cfs_throttled_seconds_total{container!="",image!=""}[5m]))
```

调查建议：高容器 CPU 不一定需要扩容。首先检查是否被限制，其次检查 Pod 的请求/限制是否过低，最后考虑服务请求延迟，以确定是否真的影响业务。

### 2.2 容器内存

| 监控维度 | 指标 | 指标含义 | 常用单位 | 异常行为 |
| --- | --- | --- | --- | --- |
| RSS 内存 | `container_memory_rss` | 容器匿名页和 RSS 内存 | 字节 | 持续增长更接近实际进程内存压力 |
| 已使用内存 | `container_memory_usage_bytes` | 容器总内存使用量 | 字节 | 包括缓存，仅凭此无法判断泄漏 |
| 工作集内存 | `container_memory_working_set_bytes` | 容器活跃工作集内存 | 字节 | 接近上限可能导致 OOMKilled |
| 内存限制 | `container_spec_memory_limit_bytes` | 容器内存限制 | 字节 | 用于计算内存使用率 |

常见查询：

```promql
container_memory_working_set_bytes{container!="",image!=""} / container_spec_memory_limit_bytes{container!="",image!=""} * 100
```

调查建议：对于业务容器的内存风险，应优先关注工作集，并且 RSS. `usage_bytes` 主要受页面缓存影响，适合进行容量观察，但不适合作为唯一的 OOM 判断依据。

### 2.3 容器磁盘与临时存储

| 监控维度 | 指标 | 指标含义 | 常用测量/单位 | 异常表现 |
| --- | --- | --- | --- | --- |
| 读取吞吐量 | `container_fs_reads_bytes_total` | 容器从磁盘累计读取的字节数 | 字节/秒 | 读取流量突然飙升可能表示扫描、导入或缓存源拉取 |
| 写入吞吐量 | `container_fs_writes_bytes_total` | 容器向磁盘累计写入的字节数 | 字节/秒 | 写入峰值可能导致节点 IO 压力 |
| 读取 IOPS | `container_fs_reads_total` | 容器读取请求的数量 | 操作次数/秒 | 小块高频读取可能增加 IO 等待 |
| 写入 IOPS | `container_fs_writes_total` | 容器写入请求的数量 | 操作次数/秒 | 日志或临时文件的过度写入 |
| 文件系统使用情况 | `container_fs_usage_bytes` | 容器文件系统使用情况 | 字节 | 临时文件或日志的累计 |
| 文件系统限制 | `container_fs_limit_bytes` | 容器文件系统限制 | 字节 | 接近限制时写入可能失败 |

常见查询： 

```promql
sum by (namespace, pod, container) (rate(container_fs_reads_bytes_total{container!="",image!=""}[5m]))
```

```promql
sum by (namespace, pod, container) (rate(container_fs_writes_bytes_total{container!="",image!=""}[5m]))
```

调查建议：当容器磁盘写入异常时，首先检查 Pod 日志卷、临时文件目录和批处理任务。当节点磁盘 IO 较高时，可使用容器 FS 指标定位是哪个 Pod 在写入。

### 2.4 容器网络

| 监控维度 | 指标 | 指标含义 | 通用范围/单位 | 异常表现 |
| --- | --- | --- | --- | --- |
| 入站流量 | `container_network_receive_bytes_total` | 容器接收的总字节数 | 字节/秒 | 请求流量或复制流量突然增加 |
| 出站流量 | `container_network_transmit_bytes_total` | 容器发送的总字节数 | 字节/秒 | 下载、同步、源获取或导出流量增加 |
| 入站丢包 | `container_network_receive_packets_dropped_total` | 容器接收时丢弃的总数据包数 | 次/秒 | 由网络堆栈或节点压力引起的数据包丢失 |
| 出站数据包丢失 | `container_network_transmit_packets_dropped_total` | 容器在传输时丢弃的数据包总数 | 次/秒 | 出口拥塞 NIC 队列，或 CNI 问题 |

常见查询：

```promql
sum by (namespace, pod) (rate(container_network_receive_bytes_total[5m]))
```

```promql
sum by (namespace, pod) (rate(container_network_transmit_bytes_total[5m]))
```

调查建议：容器网络应与节点一起分析 NIC 指标。如果在 Pod 级别丢包增加，但节点没有异常，继续检查 CNI、iptables 以及 Pod 所在节点的负载。 

### 2.5 容器线程和生命周期

| 监控维度 | 指标 | 指标含义 | 通用范围/单位 | 异常行为 |
| --- | --- | --- | --- | --- |
| 线程数 | `container_threads` | 容器内的线程数 | 数量 | 线程持续增长可能表示线程泄漏 |
| 最后一次看到 | `container_last_seen` | cAdvisor 上次看到容器的时间 | Unix 时间戳 | 长时间未更新可能表示容器已退出或采集异常 |
| 重启次数 | `kube_pod_container_status_restarts_total` | 容器重启总次数 | 计数/递增 | 频繁重启表示崩溃、探针失败或 OOM |
| 等待原因 | `kube_pod_container_status_waiting_reason` | 容器处于等待状态的原因 | 标签值 | `CrashLoopBackOff`, `ImagePullBackOff`等，需要处理 |
| 运行状态 | `kube_pod_container_status_running` | 容器是否在运行 | `0/1` | 关键容器未 `1` 表示服务不可用 |

调查建议：对于容器异常，首先检查状态原因，然后查看重启次数和最近一次重启时间。如果重启频繁，继续使用应用日志、 OOM 事件和探针配置进行调查。 

## 3. Kubernetes 集群监控

Kubernetes 监控用于评估集群资源使用情况、控制平面健康状况、工作负载副本状态以及存储对象状态。主要指标来自 kube-state-metrics、kubelet 和 APIServer。 

### 3.1 节点容量和可调度资源

| 监控维度 | 指标 | 指标含义 | 通用范围/单位 | 异常表现 |
| --- | --- | --- | --- | --- |
| 节点容量 | `kube_node_status_capacity` | 节点的总容量 | CPUCPU、内存、Pod 数量等 | 用于容量规划 |
| 可分配资源 | `kube_node_status_allocatable` | 节点的可调度资源 | CPUCPU、内存、Pod 数量等 | 可调度资源不足会导致 Pod 处于 Pending 状态 |
| 节点条件 | `kube_node_status_condition` | 节点 Ready、MemoryPressure 及其他状态 | `0/1` | Ready 异常或发生 Pressure 需要立即关注 |
| 禁止调度 | `kube_node_spec_unschedulable` | 节点是否被标记为不可调度 | `0/1` | 设置为 '1' 时，节点不会调度新的 Pod |
| 节点信息 | `kube_node_info` | 节点版本、内核、容器运行时信息 | 标签信息 | 用于排查版本差异 |

排障建议：Pod Pending 时，先检查可分配资源和请求，然后检查节点是否为 '不可调度'，最后检查节点条件是否存在资源压力。 

### 3.2 Pod 状态 

| 监控维度 | 指标 | 指标含义 | 常用口径/单位 | 异常行为 |
| --- | --- | --- | --- | --- |
| Pod 信息 | `kube_pod_info` | Pod 的命名空间、节点等信息 | 标签信息 | 用于定位 Pod 分布 |
| Pod 阶段 | `kube_pod_status_phase` | Pending、Running、Succeeded、Failed 状态等 | `0/1` | Pending/Failed 增加表示调度或运行异常 |
| Pod 就绪 | `kube_pod_status_ready` | Pod 是否就绪 | `0/1` | 未就绪会影响服务可用性 |
| Pod 原因 | `kube_pod_status_reason` | Pod 异常原因 | 标签值 | Evicted、NodeLost 等需要排查 |
| 容器重启 | `kube_pod_container_status_restarts_total` | 容器重启次数 | 次/增量 | 重启增长表示稳定性问题 |
| 容器等待 | `kube_pod_container_status_waiting` | 容器是否处于等待状态 | `0/1` | 如果等待状态持续，Pod 无法正常提供服务 |
| 等待原因 | `kube_pod_container_status_waiting_reason` | 等待状态原因 | 标签值 | 镜像拉取失败、CrashLoop 等 |
| 容器终止 | `kube_pod_container_status_terminated` | 容器是否已终止 | `0/1` | 意外终止，需要结合重启和日志检查 |

常用查询:

```promql
sum by (namespace, phase) (kube_pod_status_phase == 1)
```

```promql
increase(kube_pod_container_status_restarts_total[10m])
```

调查建议：当出现 Pod 异常时，不仅要查看 Pod 阶段。就绪状态、原因以及容器等待原因能更好地说明具体问题。

### 3.3 资源请求和限制

| 监控维度 | 指标 | 指标含义 | 常用测量/单位 | 异常行为 |
| --- | --- | --- | --- | --- |
| 请求的资源 | `kube_pod_container_resource_requests` | 容器请求 | CPU，内存 | 请求过高会影响调度，过低会影响稳定性 |
| 资源限制 | `kube_pod_container_resource_limits` | 容器限制 | CPU，内存 | 限制过低可能导致 CPU 节流或 OOM |
| 节点可分配资源 | `kube_node_status_allocatable` | 节点上可用于调度的资源 | CPU，内存 | 用于计算集群资源分配率 |
| 容器使用情况 | `container_cpu_usage_seconds_total`, `container_memory_working_set_bytes` | 实际 CPU 和内存使用情况 | 核/秒，字节 | 用于判断请求/限制是否合理 |

常见查询：

```promql
sum(kube_pod_container_resource_requests{resource="cpu"}) / sum(kube_node_status_allocatable{resource="cpu"}) * 100
```

```promql
sum(kube_pod_container_resource_requests{resource="memory"}) / sum(kube_node_status_allocatable{resource="memory"}) * 100
```

调查建议：资源规划应同时考虑“请求值”和“实际使用值”。仅看请求可能误判业务压力，而仅看使用可能忽视调度容量。

### 3.4 工作负载副本数

| 监控维度 | 指标 | 指标含义 | 通用范围/单位 | 异常表现 |
| --- | --- | --- | --- | --- |
| Deployment 副本 | `kube_deployment_status_replicas` | Deployment 当前副本数 | 单元 | 与预期副本不一致 |
| 已更新副本 | `kube_deployment_status_replicas_updated` | 副本数量已更新至新版本 | 单元 | 发布期间长时间没有增长 |
| 不可用副本 | `kube_deployment_status_replicas_unavailable` | 不可用副本数量 | 单元 | 服务容量大于0时会下降 |
| StatefulSet 副本 | `kube_statefulset_status_replicas` | 当前 StatefulSet 副本数量 | 单元 | 有状态服务中异常副本 |
| StatefulSet 就绪 | `kube_statefulset_status_replicas_ready` | 就绪 StatefulSet 副本数量 | 单元 | 如果 Ready 小于预期副本数，服务不完整 |

调查建议：当发布异常时，请检查 `updated` 以及 `unavailable`对于 StatefulSet 异常，请注意 PVC、Pod 启动顺序和节点亲和性。

### 3.5 作业和批处理任务

| 监控维度 | 指标 | 指标含义 | 通用标准/单位 | 异常表现 |
| --- | --- | --- | --- | --- |
| 运行作业 | `kube_job_status_active` | 当前活动作业数量 | 计数 | 长期活动可能表示作业卡住 |
| 失败作业 | `kube_job_status_failed` | 作业失败数量 | 计数 | 失败增加时需要检查作业日志 |
| 成功作业 | `kube_job_status_succeeded` | 成功完成作业的数量 | 计数 | 用于判断任务完成情况 |
| 完成时间 | `kube_job_status_completion_time` | 作业完成时间 | Unix 时间戳 | 缺少完成时间可能表示作业未完成 |

调查建议：当批处理任务出现异常时，应一起检查 `active`, `failed`和 `succeeded` 仅查看失败可能会遗漏长时间卡住的任务。

### 3.6 PVC 和存储对象

| 监控维度 | 指标 | 指标含义 | 通用标准/单位 | 异常表现 |
| --- | --- | --- | --- | --- |
| PVC 状态 | `kube_persistentvolumeclaim_status_phase` | PVC 绑定、挂起及其他状态 | `0/1` | 挂起将导致 Pod 无法挂载存储 |
| PVC 请求容量 | `kube_persistentvolumeclaim_resource_requests_storage_bytes` | 存储请求的容量 PVC | 字节 | 用于容量规划和配额管理 |

故障排除建议：当有状态服务无法启动时，除了检查 Pod，还应检查 PVC 是否已绑定、存储类是否可用以及底层存储容量是否不足。

### 3.7 APIServer、etcd 与控制平面

| 监控维度 | 指标 | 指标含义 | 常用单位/量纲 | 异常表现 |
| --- | --- | --- | --- | --- |
| APIServer 请求数量 | `apiserver_request_total` | APIServer 请求累计数量 | 请求/秒 | 请求突然激增可能来自控制器、 kubectl或业务组件 |
| APIServer 延迟 | `apiserver_request_duration_seconds_bucket` | APIServer 请求时长分段 | P50/P95/P99 | 延迟增加将影响调度、部署及控制器同步 |
| etcd 延迟 | `etcd_request_duration_seconds_bucket` | etcd 请求持续时间分桶 | P50/P95/P99 | etcd 慢可能会拖慢整个控制平面 |
| 队列等待 | `workqueue_queue_duration_seconds_bucket` | 控制器队列等待持续时间 | 百分位持续时间 | 队列积压，资源状态同步变慢 |
| 队列处理 | `workqueue_work_duration_seconds_bucket` | 控制器处理持续时间 | 百分位持续时间 | 控制器处理变慢 |

常用查询:

```promql
sum by (verb, resource) (rate(apiserver_request_total[5m]))
```

```promql
histogram_quantile(0.95, sum(rate(apiserver_request_duration_seconds_bucket[5m])) by (le, verb, resource))
```

调查建议：控制平面问题通常表现为部署缓慢、Pod 状态更新慢以及响应慢。 kubectl 当 APIServer 延迟和 etcd 延迟同时增加时，应优先检查 etcd、磁盘 IO 以及控制平面节点负载。

## 4. MySQL 监控

MySQL 监控用于观察实例可用性、连接压力、 SQL 请求量、慢查询、缓存命中率、临时表、锁等待、文件句柄和网络吞吐量。

### 4.1 实例状态和请求量

| 监控维度 | 指标 | 指标含义 | 通用范围/单位 | 异常表现 |
| --- | --- | --- | --- | --- |
| 实例存活 | `up` | mysql exporter 是否能被采集 | `0/1` | 当 `0`实例、网络或 exporter 异常 |
| 运行时长 | `mysql_global_status_uptime` | MySQL 运行时间 | 秒 | 减少表示实例重启 |
| 总查询次数 | `mysql_global_status_queries` | 查询累计次数 | 次/秒 | QPS 峰值可能表示业务高峰或异常请求 |
| 问题 | `mysql_global_status_questions` | 客户端发起的语句累计次数 | 次/秒 | 需与查询一起查看以评估请求压力 |
| 命令统计 | `mysql_global_status_commands_total` | 各种命令的累计次数 | 次/秒 | 可区分命令如 select、insert、update、delete |

常见查询： 

```promql
rate(mysql_global_status_queries[5m])
```

```promql
sum by (command) (rate(mysql_global_status_commands_total[5m]))
```

调查建议：当 QPS 上升时，首先检查命令分布。如果 `select` 随着扫描型指标增加而上升，则关注索引和慢查询；如果写入命令增加，则继续监控锁等待、磁盘 IO 和主机写入延迟。

### 4.2 连接和线程

| 监控维度 | 指标 | 指标含义 | 常用单位/量纲 | 异常表现 |
| --- | --- | --- | --- | --- |
| 当前连接数 | `mysql_global_status_threads_connected` | 当前连接的线程数 | 数量 | 接近上限可能导致新连接失败 |
| 活跃线程 | `mysql_global_status_threads_running` | 当前正在执行的线程数 | 数量 | 持续增加通常表示执行缓慢或锁等待 SQL 执行缓慢或锁等待 |
| 历史最大连接数 | `mysql_global_status_max_used_connections` | 历史使用的最大连接数 | 数量 | 接近最大值_连接数表明连接池需要评估 |
| 最大连接数 | `mysql_global_variables_max_connections` | MySQL 最大连接配置 | 数量 | 用于计算连接使用率 |
| 异常客户端 | `mysql_global_status_aborted_clients` | 客户端异常断开累计次数 | 次/秒 | 网络问题、超时或客户端异常 |
| 连接失败 | `mysql_global_status_aborted_connects` | 连接失败总次数 | 次/秒 | 认证错误、连接限制、网络异常等 |

常见查询：

```promql
mysql_global_status_threads_connected / mysql_global_variables_max_connections * 100
```

调查建议：连接数高不一定表示数据库慢，也可能是应用程序连接池配置不当引起的。 `Threads_running` 长期过高更令人关注，因为它通常对应 SQL 执行或锁等待问题。

### 4.3 慢查询、扫描和排序

| 监控维度 | 指标 | 指标含义 | 常用测量/单位 | 异常行为 |
| --- | --- | --- | --- | --- |
| 慢查询 | `mysql_global_status_slow_queries` | 慢查询累计次数 | 次/秒 | 增加表示慢查询增多 SQL |
| 全连接扫描 | `mysql_global_status_select_full_join` | 没有索引的连接次数 | 次/秒 | 表明可能缺少连接条件的索引 |
| 全表扫描 | `mysql_global_status_select_scan` | 全表扫描次数 | 次/秒 | 在大表上的全表扫描会降低实例速度 |
| 排序合并 | `mysql_global_status_sort_merge_passes` | 排序需要多次合并的次数 | 次/秒 | 排序缓冲区不足或要排序的数据过多 |

调查建议：当慢查询增加时，检查是否与业务发布时段和变更记录有关。 SQL 如果扫描和排序指标上升，通常需要回顾慢日志、执行计划和索引设计。

### 4.4 InnoDB 缓冲池

| 监控维度 | 指标 | 指标含义 | 常用单位/量纲 | 异常表现 |
| --- | --- | --- | --- | --- |
| 缓冲池大小 | `mysql_global_variables_innodb_buffer_pool_size` | InnoDB 缓冲池配置大小 | 字节 | 过小会增加磁盘读取 |
| 缓冲池页面 | `mysql_global_status_buffer_pool_pages` | 各种类型缓冲池页面的数量 | 页面 | 用于监控脏页、空闲页、数据页和其他页面 |
| 页面大小 | `mysql_global_status_innodb_page_size` | InnoDB 页面大小 | 字节 | 用于将页面数量转换为容量 |

调查建议：当缓冲池命中率较低时，数据库将更多地访问磁盘，有必要结合节点的磁盘读取吞吐量、读取 IOPS和 iowait 一起评估。

### 4.5 临时表、表缓存和文件句柄

| 监控维度 | 指标 | 指标含义 | 常用单位/量纲 | 异常表现 |
| --- | --- | --- | --- | --- |
| 临时表 | `mysql_global_status_created_tmp_tables` | 创建的临时表总数 | 次/秒 | 查询复杂度增加 |
| 磁盘临时表 | `mysql_global_status_created_tmp_disk_tables` | 创建的磁盘临时表总数 | 次/秒 | 增加的磁盘IO压力， SQL 可能导致变慢 |
| 临时文件 | `mysql_global_status_created_tmp_files` | 创建的临时文件总数 | 次/秒 | 临时文件增加 |
| 表锁即时 | `mysql_global_status_table_locks_immediate` | 表锁即时获取次数 | 次/秒 | 正常参考指标 |
| 表锁等待 | `mysql_global_status_table_locks_waited` | 表锁等待次数 | 次/秒 | 锁争用增加 |
| 表缓存命中 | `mysql_global_status_table_open_cache_hits` | 表开启缓存命中次数 | 次/秒 | 命中率低可能表示频繁开启表 |
| 表缓存未命中 | `mysql_global_status_table_open_cache_misses` | 表开启缓存未命中次数 | 次/秒 | 需要评估表缓存 |
| 表缓存溢出 | `mysql_global_status_table_open_cache_overflows` | 表开启缓存溢出次数 | 次/秒 | 配置不足或表太多 |
| 已打开的表 | `mysql_global_status_open_tables` | 当前打开的表数 | 个 | 接近缓存限制时风险增加 |
| 表缓存配置 | `mysql_global_variables_table_open_cache` | 表格_开启_配置的缓存值 | 个 | 用于计算使用率 |
| 已打开的文件 | `mysql_global_status_open_files` | 当前已打开的文件数 | 个 | 接近文件限制时可能影响 SQL 执行 |
| 文件限制 | `mysql_global_variables_open_files_limit` | MySQL 文件句柄限制 | 个 | 用于计算文件句柄使用率 |

故障排除建议：临时表、锁等待和表缓存未命中通常会与慢查询同时出现。当磁盘临时表增加时，应关注节点写入 IO、磁盘延迟以及 SQL 排序/分组。

### 4.6 网络吞吐量

| 监控维度 | 指标 | 指标含义 | 常用单位 | 异常表现 |
| --- | --- | --- | --- | --- |
| 入站流量 | `mysql_global_status_bytes_received` | 累计 MySQL 接收的字节数 | 字节/秒 | 请求体或写入流量增加 |
| 出站流量 | `mysql_global_status_bytes_sent` | 累计发送字节数 MySQL | 字节/秒 | 大型查询、全表扫描和批量导出会增加出站流量 |

常用查询:

```promql
rate(mysql_global_status_bytes_received[5m])
```

```promql
rate(mysql_global_status_bytes_sent[5m])
```

调查建议：当 MySQL 出站流量突然增加，通常应注意大型结果集、导出任务和未分页的查询。

## 5. MongoDB 监控

MongoDB 监控用于观察实例状态、连接数、操作量、查询扫描、内存使用、网络吞吐量以及复制缓冲区状况。

### 5.1 实例和连接

| 监控维度 | 指标 | 指标含义 | 常用测量/单位 | 异常表现 |
| --- | --- | --- | --- | --- |
| 实例存活 | `up` | Mongo 导出器是否能够收集数据 | `0/1` | 如果 `0`，则实例或导出器异常 |
| 运行时长 | `mongodb_ss_uptime` | MongoDB 运行时间 | 秒 | 较小的值表示实例重启 |
| 连接数 | `mongodb_ss_connections` | 当前连接相关统计 | 数量 | 异常高的连接数可能表明连接池或业务高峰 |

调查建议：当连接数上升时，首先确认是否存在业务高峰、连接池配置的变化或客户端异常重连。

### 5.2 操作和文档处理

| 监控维度 | 指标 | 指标含义 | 常用测量/单位 | 异常表现 |
| --- | --- | --- | --- | --- |
| 操作计数 | `mongodb_ss_opcounters` | 插入、查询、更新、删除等操作的累积数量 | 次/秒 | 某类型操作的突然增加表明业务访问模式发生变化 |
| 文档处理 | `mongodb_ss_metrics_document` | 插入、更新、删除、返回等文档的累积计数 | 次/秒 | 如果返回数量明显高于实际需求，可能是结果集过大 |
| 索引条目扫描 | `mongodb_ss_metrics_queryExecutor_scanned` | 查询期间扫描的索引条目数量 | 次/秒 | 过度扫描可能表明索引不当 |
| 文档扫描 | `mongodb_ss_metrics_queryExecutor_scannedObjects` | 查询期间扫描的文档数量 | 次/秒 | 大量文档扫描表明查询效率低 |

常见查询： 

```promql
sum by (type) (rate(mongodb_ss_opcounters[5m]))
```

调查建议：一种常见表现是 MongoDB 慢查询是扫描数量/扫描对象增加。需要结合慢日志和索引命中进行分析。

### 5.3 内存、网络和磁盘

| 监控维度 | 指标 | 指标含义 | 常用单位/测量 | 异常表现 |
| --- | --- | --- | --- | --- |
| 常驻内存 | `mongodb_ss_mem_resident` | MongoDB 常驻内存 | MB或字节 | 持续增长需要检查主机内存 |
| 虚拟内存 | `mongodb_ss_mem_virtual` | MongoDB 虚拟内存 | MB或字节 | 单独增加不一定表示真实压力 |
| 入站流量 | `mongodb_ss_network_bytesIn` | MongoDB 累计接收字节 | 字节/秒 | 写入或请求流量增加 |
| 出站流量 | `mongodb_ss_network_bytesOut` | MongoDB 累计发送字节 | 字节/秒 | 大查询或导出任务导致出站流量增加 |
| 主机读IO | `node_disk_reads_completed_total` | 读取 IOPS 在其所在节点上 MongoDB 驻留 | 次/秒 | 查询扫描导致读IO增加 |
| 主机写IO | `node_disk_writes_completed_total` | 写入 IOPS 在其所在节点上 MongoDB 位于 | 次/秒 | 写入或日志压力增加 | 

故障排除建议： MongoDB 内存和磁盘性能应结合节点的内存和磁盘IO一起考虑。将实例指标与主机磁盘读/写结合查看，更容易判断是否 MongoDB 自身慢还是底层资源慢。 

### 5.4 复制缓冲 

| 监控维度 | 指标 | 指标含义 | 常用测量/单位 | 异常表现 | 
| --- | --- | --- | --- | 
| 复制缓冲区大小 | `mongodb_ss_metrics_repl_buffer_sizeBytes` | 复制缓冲区的大小 | 字节 | 缓冲区持续增长表明复制消耗不及时 | 

故障排除建议：异常的复制缓冲区通常与从节点的处理能力、网络或磁盘写入有关，需要结合复制延迟、节点网络和磁盘写入指标进行分析。 

## 6. Redis 监控 

Redis 监控用于观察实例可用性、连接数、命令处理、内存级别、键空间、命中率、驱逐和网络吞吐量。 

### 6.1 实例与客户端 

| 监控维度 | 指标 | 指标含义 | 常用测量/单位 | 异常表现 | 
| --- | --- | --- | --- | --- |
| 实例存活 | `up` | 是否 Redis 可以收集 exporter | `0/1` | 当 `0`，则实例或导出器异常 |
| 运行时长 | `redis_uptime_in_seconds` | Redis 运行时间 | 秒 | 下降表明实例重启 |
| 客户端连接 | `redis_connected_clients` | 当前客户端连接数 | 数量 | 突然增加可能表明连接池或重连风暴 |

### 6.2 命令、内存与键空间

| 监控维度 | 指标 | 指标含义 | 常用单位 | 异常行为 |
| --- | --- | --- | --- | --- |
| 处理的命令 | `redis_commands_processed_total` | 命令总数 Redis 处理的命令总数 | 次/秒 | 突然 QPS 峰值可能增加实例负载 CPU |
| 命令分类 | `redis_commands_total` | 按类型统计的命令总数 | 次/秒 | 可识别 get、set、del 等命令的变化 |
| 内存使用 | `redis_memory_used_bytes` | 当前 Redis 内存使用情况 | 字节 | 接近 maxmemory 可能会触发驱逐 |
| 最大内存 | `redis_memory_max_bytes` | Redis maxmemory 配置 | 字节 | 用于计算内存使用率 |
| 键的数量 | `redis_db_keys` | 每个数据库中的键数 | 数量 | 键的异常增长可能表示缓存没有设置过期或存在写入异常 |
| 即将过期的键 | `redis_db_keys_expiring` | 已设置过期的键数 | 数量 | 比例过低需要关注缓存生命周期 |

常见查询：

```promql
rate(redis_commands_processed_total[5m])
```

```promql
redis_memory_used_bytes / redis_memory_max_bytes * 100
```

### 6.3 命中率、驱逐和网络

| 监控维度 | 指标 | 指标含义 | 常用单位/量纲 | 异常表现 |
| --- | --- | --- | --- | --- |
| 命中次数 | `redis_keyspace_hits_total` | 键命中的总次数 | 次/秒 | 与未命中一起计算命中率 |
| 未命中次数 | `redis_keyspace_misses_total` | 键未命中的总次数 | 次/秒 | 未命中次数增加可能导致回源压力增大 |
| 已过期的键 | `redis_expired_keys_total` | 已过期的键总数 | 次/秒 | 过期风暴可能导致 CPU 抖动 |
| 被驱逐的键 | `redis_evicted_keys_total` | 被驱逐键的总数 | 次/秒 | 增长表明内存压力或 maxmemory 不足 |
| 入站流量 | `redis_net_input_bytes_total` | 收到的总字节数 Redis | 字节/秒 | 写入或请求流量增加 |
| 出站流量 | `redis_net_output_bytes_total` | 发送的总字节数 Redis | 字节/秒 | 高出站流量可能由大值或批量读取引起 |

常见查询：

```promql
rate(redis_keyspace_hits_total[5m]) / (rate(redis_keyspace_hits_total[5m]) + rate(redis_keyspace_misses_total[5m])) * 100
```

```promql
rate(redis_evicted_keys_total[5m])
```

调查建议: 对 Redis，关注内存和驱逐风险。命中率下降会将压力转移到后端数据库。驱逐次数增加表明需要评估缓存容量或驱逐策略。

## 7. Kafka 监控

Kafka 监控用于观察Broker数量、主题/分区状态、生产和消费位移、消费者组延迟、成员数量及副本同步状态。

### 7.1 Broker、主题和分区

| 监控维度 | 指标 | 指标含义 | 常用单位/量纲 | 异常表现 |
| --- | --- | --- | --- | --- |
| Broker数量 | `kafka_brokers` | 当前可见的Broker数量 | 个 | 数量减少表示Broker不可用或无法访问导出器 |
| 主题分区 | `kafka_topic_partitions` | 主题的分区数量 | 个 | 分区变化会影响并发和消费能力 |
| 当前分区位移 | `kafka_topic_partition_current_offset` | 分区的最新位移 | 位移 / 增长率 | 在持续生产写入期间应持续增加 |
| 分区最旧位移 | `kafka_topic_partition_oldest_offset` | 分区最旧位移 | 位移 | 用于观察保留数据的范围 |

常见查询： 

```promql
sum by (topic) (rate(kafka_topic_partition_current_offset[5m]))
```

调查建议：当生产速率异常时，首先检查主题的当前偏移量增长。如果业务确认有写入但偏移量未增加，请检查生产者端错误、Broker 状态以及主题配置。

### 7.2 消费者组与滞后

| 监控维度 | 指标 | 指标含义 | 常用测量/单位 | 异常表现 |
| --- | --- | --- | --- | --- |
| 消费偏移量 | `kafka_consumergroup_current_offset` | 消费者组当前消费的偏移量 | 位移 / 增长率 | 没有增长表明消费已停止或被阻塞 |
| 分区滞后 | `kafka_consumergroup_lag` | 消费者组在该分区的积压 | 数量 | 滞后增加表明消费落后于生产 |
| 组总滞后 | `kafka_consumergroup_lag_sum` | 消费者组的总积压 | 数量 | 总滞后持续增加表明业务延迟在扩大 |
| 组成员 | `kafka_consumergroup_members` | 消费者组中的成员数量 | 数量 | 成员数量减少可能导致消费能力下降 |

常见查询：

```promql
sum by (consumergroup, topic) (kafka_consumergroup_lag)
```

```promql
sum by (consumergroup, topic) (rate(kafka_consumergroup_current_offset[5m]))
```

故障排除建议：核心业务指标的 Kafka 是延迟。当延迟增加时，首先检查消费者成员数量是否减少，然后查看消费速率是否下降，最后检查应用处理时间、下游依赖项和 Broker IO。

### 7.3 副本和 ISR

| 监控维度 | 指标 | 指标含义 | 常用测量/单位 | 异常表现 |
| --- | --- | --- | --- | --- |
| 副本数量 | `kafka_topic_partition_replicas` | 分区副本数量 | 数量 | 副本数量少于预期会降低可靠性 |
| ISR 副本 | `kafka_topic_partition_in_sync_replica` | 分区同步副本数量 | 数量 | 下降 ISR 表示滞后副本或 Broker 出现问题 |
| 首选领导者 | `kafka_topic_partition_leader_is_preferred` | 领导者是否为首选副本 | `0/1` | 长期不平衡可能导致某些 Broker 压力过大 |

故障排除建议：下降 ISR 表示可靠性风险高于普通延迟。检查 Broker 状态、网络、磁盘写入延迟和副本同步情况。

## 8. MinIO 对象存储监控

MinIO 监控用于观察对象存储集群的可用性、节点和磁盘状态、Bucket 容量、 S3 请求、错误、流量、进程句柄和修复任务活动。 

### 8.1 集群节点和磁盘 

| 监控维度 | 指标 | 指标含义 | 常用单位/量纲 | 异常表现 |
| --- | --- | --- | --- | --- |
| 在线节点 | `minio_cluster_nodes_online_total` | 在线节点数量 MinIO 节点 | 个 | 数量减少表示节点不可用 |
| 离线节点 | `minio_cluster_nodes_offline_total` | 离线数量 MinIO 节点 | 个 | 大于 0 需要关注集群可用性 |
| 在线磁盘 | `minio_cluster_disk_online_total` | 在线磁盘数量 | 个 | 磁盘数量减少会影响冗余和写入能力 |
| 离线磁盘 | `minio_cluster_disk_offline_total` | 离线磁盘数量 | 个 | 大于 0 需要对磁盘或挂载点进行排查 |
| 可用容量 | `minio_cluster_capacity_usable_free_bytes` | 集群可用容量 | 字节 | 持续下降表示存在容量不足风险 |

故障排查建议：对于对象存储，首先检查节点和磁盘的在线状态。不要仅以数量评估离线磁盘，应结合纠删码冗余策略判断风险。 

### 8.2 桶容量和对象数量

| 监控维度 | 指标 | 指标含义 | 常用单位/量纲 | 异常表现 |
| --- | --- | --- | --- | --- |
| 桶容量 | `bucket_usage_size` | 桶已用容量 | 字节 | 容量增长迅速，需要评估扩容 |
| 对象数量 | `bucket_objects_count` | 桶内对象数量 | 计数 | 过多的小对象会增加元数据和扫描压力 |
| 对象大小分布 | `minio_bucket_objects_size_distribution` | 桶内对象大小分布 | 分桶统计 | 对象分布的变化会影响存储和请求性能 |

常见查询：

```promql
sum by (bucket) (bucket_usage_size)
```

```promql
sum by (bucket) (bucket_objects_count)
```

调查建议：容量增长应按桶分别分析。当对象数量快速增加但容量增长不明显时，通常是由于小对象增加所致。应注意生命周期清理和业务写入模式。

### 8.3 S3 请求、错误和流量

| 监控维度 | 指标 | 指标含义 | 常用测量/单位 | 异常表现 |
| --- | --- | --- | --- | --- |
| S3 请求数量 | `minio_s3_requests_total` | 累计请求数量 S3 API 请求 | 次/秒 | 请求突然增加，可能是业务高峰或重试 |
| S3 错误数量 | `minio_s3_requests_errors_total` | 累计请求数量 S3 API 错误 | 次/秒 | 错误率上升影响对象读写 |
| 接收流量 | `minio_s3_traffic_received_bytes` | 累计 S3 已接收字节 | 字节/秒 | 上传流量增加 |
| 发送流量 | `minio_s3_traffic_sent_bytes` | 累计 S3 已发送字节 | 字节/秒 | 下载或源检索流量增加 |

常见查询：

```promql
sum by (api) (rate(minio_s3_requests_total[5m]))
```

```promql
sum(rate(minio_s3_requests_errors_total[5m])) / sum(rate(minio_s3_requests_total[5m])) * 100
```

调查建议：当 S3 错误率增加时，首先按类型进行分解，然后检查相应的 Bucket、节点磁盘状态和网络流量。 API 

### 8.4 节点进程、文件句柄和 IO

| 监控维度 | 指标 | 指标含义 | 常用单位/量纲 | 异常表现 |
| --- | --- | --- | --- | --- |
| 节点磁盘使用情况 | `minio_node_disk_used_bytes` | 磁盘使用情况 MinIO 节点 | 字节 | 单节点容量不平衡 |
| 打开的文件句柄 | `minio_node_file_descriptor_open_total` | 进程打开的文件句柄数量 MinIO 进程 | 计数 | 接近系统限制时请求可能失败 |
| 读取系统调用 | `minio_node_syscall_read_total` | 读取系统调用的累计次数 | 次数/秒 | 读取调用异常增加 |
| 写入系统调用 | `minio_node_syscall_write_total` | 写入系统调用的累计次数 | 次数/秒 | 写入调用异常增加 |
| 进程读取字节数 | `minio_node_io_rchar_bytes` | 进程累计读取的字节数 | 字节/秒 | 读取负载增加 |
| 进程写入字节数 | `minio_node_io_wchar_bytes` | 进程写入的累计字节数 | 字节/秒 | 写入负载增加 |
| 协程数量 | `minio_node_go_routine_total` | 中的协程数量 MinIO 进程 | 计数 | 持续增长可能表示请求积压或泄漏 |
| 开始时间 | `minio_node_process_starttime_seconds` | MinIO 进程启动时间 | Unix 时间戳 | 变化表示进程重启 |

调查建议：对于 MinIO 性能问题，请考虑 S3 请求、节点磁盘、进程IO和协程。单独的高请求量不一定异常；错误率、IO延迟和磁盘离线状态是更明确的风险信号。

### 8.5 修复和使用活动

| 监控维度 | 指标 | 指标含义 | 通用标准/单位 | 异常行为 |
| --- | --- | --- | --- | --- |
| 修复活动 | `minio_heal_time_last_activity_nano_seconds` | 上次修复活动时间 | 纳秒时间戳 | 长时间或频繁的修复需要关注磁盘健康 |
| 使用活动 | `minio_usage_last_activity_nano_seconds` | 上次使用扫描活动时间 | 纳秒时间戳 | 异常的使用扫描可能影响容量统计的准确性 |

调查建议：在节点或磁盘异常恢复后，监控修复活动是否正常进行，以防对象冗余长时间处于风险状态。

## 9. Elasticsearch 监控

Elasticsearch 监控用于观察搜索集群的健康状况、节点数量、分片分布、索引读写操作、缓存、 JVM线程池、磁盘和网络。ES故障通常不能通过单一指标判定，更常见的是“分片异常   JVM 压力  线程池拒绝  磁盘水位”同时出现。

### 9.1 集群健康与节点

| 监控维度 | 指标 | 指标含义 | 常用测量/单位 | 异常行为 |
| --- | --- | --- | --- | --- |
| 集群健康状况 | `elasticsearch_cluster_health_status` | ES集群健康状态 | 状态值 | 黄色/红色表示副本或主分片异常 |
| 节点数量 | `elasticsearch_cluster_health_number_of_nodes` | 集群节点数量 | 计数 | 节点数量下降可能表示节点已离线 |
| 数据节点数量 | `elasticsearch_cluster_health_number_of_data_nodes` | 集群中的数据节点数量 | 计数 | 数据节点减少会影响分片容量和读/写能力 |
| 待处理任务 | `elasticsearch_cluster_health_number_of_pending_tasks` | 集群中待处理任务的数量 | 计数 | 持续增长表明主节点或集群状态更新缓慢 |
| 活动主分片 | `elasticsearch_cluster_health_active_primary_shards` | 活动主分片数量 | 个 | 减少风险高，可能影响索引可用性 |
| 活动分片 | `elasticsearch_cluster_health_active_shards` | 活动分片总数 | 个 | 减少表明分片未完全恢复 |
| 初始化分片 | `elasticsearch_cluster_health_initializing_shards` | 初始化分片数量 | 个 | 长时间没有减少表明恢复缓慢 |
| 迁移分片 | `elasticsearch_cluster_health_relocating_shards` | 迁移分片数量 | 个 | 迁移过多会增加网络和磁盘压力 |
| 未分配分片 | `elasticsearch_cluster_health_unassigned_shards` | 未分配分片数量 | 个 | 大于0表示分片未分配到节点 |
| 延迟未分配 | `elasticsearch_cluster_health_delayed_unassigned_shards` | 延迟未分配分片数量 | 个 | 节点离线后等待重新分配 |

常见查询： 

```promql
elasticsearch_cluster_health_status
```

```promql
elasticsearch_cluster_health_unassigned_shards
```

调查建议：ES 应首先检查健康状态和未分配分片。红色状态应优先处理主分片；黄色状态大多由未分配的副本引起，也不能长时间被忽视。 

### 9.2 磁盘容量和文件系统

| 监控维度 | 指标 | 指标含义 | 常用测量 / 单位 | 异常表现 |
| --- | --- | --- | --- | --- |
| 数据磁盘总量 | `elasticsearch_filesystem_data_size_bytes` | ES 数据目录总容量 | 字节 | 用于计算磁盘使用率 |
| 可用数据磁盘 | `elasticsearch_filesystem_data_available_bytes` | ES 数据目录可用容量 | 字节 | 可用空间不足可能触发分片迁移或写入限制 |

常用查询:

```promql
(1 - elasticsearch_filesystem_data_available_bytes / elasticsearch_filesystem_data_size_bytes) * 100
```

调查建议：ES 对磁盘使用非常敏感。当磁盘使用率过高时，可能发生分片迁移、只读索引或写入失败。需要监控索引增长、保留策略和节点磁盘分布。

### 9.3 文档、索引和删除

| 监控维度 | 指标 | 指标含义 | 常用单位 | 异常行为 |
| --- | --- | --- | --- | --- |
| 文档数量 | `elasticsearch_indices_docs` | 当前文档数量 | 数量 | 文档快速连续增长需要进行容量评估 |
| 已删除文档 | `elasticsearch_indices_docs_deleted` | 已删除文档数量 | 数量 | 高删除率会导致合并压力 |
| 索引写入次数 | `elasticsearch_indices_indexing_index_total` | 索引操作累计次数 | 次/秒 | 写入的突然增加会增加 CPU磁盘和刷新压力 |
| 索引写入时间 | `elasticsearch_indices_indexing_index_time_seconds_total` | 索引操作的累计时间 | 秒/秒 | 写入时间增加会减慢写入路径 |
| 删除操作次数 | `elasticsearch_indices_indexing_delete_total` | 删除操作的累计次数 | 次/秒 | 删除操作的突然增加可能导致分段合并压力 |
| 删除操作持续时间 | `elasticsearch_indices_indexing_delete_time_seconds_total` | 删除操作的累计持续时间 | 秒/秒 | 删除持续时间增加 |

常用查询:

```promql
sum by (cluster) (rate(elasticsearch_indices_indexing_index_total[5m]))
```

```promql
rate(elasticsearch_indices_indexing_index_time_seconds_total[5m]) / rate(elasticsearch_indices_indexing_index_total[5m])
```

故障排除建议：当写入速度变慢时，不要只看写入 QPS。你还应该考虑刷新、合并、事务日志、线程池拒绝和磁盘 IO。

### 9.4 查询和获取请求

| 监控维度 | 指标 | 指标含义 | 常用测量/单位 | 异常行为 |
| --- | --- | --- | --- | --- |
| 查询请求数 | `elasticsearch_indices_search_query_total` | 搜索查询的累计数量 | 次/秒 | 查询的突然增加 |
| 查询延迟 | `elasticsearch_indices_search_query_time_seconds` | 搜索查询的累计时间 | 秒/秒 | 平均查询延迟增加 |
| 获取请求数 | `elasticsearch_indices_search_fetch_total` | 搜索获取阶段的累计数量 | 次/秒 | 大型结果集会增加获取次数 |
| 获取延迟 | `elasticsearch_indices_search_fetch_time_seconds` | 搜索获取的累计时间 | 秒/秒 | 慢速获取通常与大量结果集、磁盘或网络有关 |
| 获取请求数量 | `elasticsearch_indices_get_exists_total`, `elasticsearch_indices_get_missing_total` | 获取命中与未命中累计计数 | 次/秒 | 未命中数量增加可能表明业务访问了不存在的文档 |
| 获取持续时间 | `elasticsearch_indices_get_time_seconds`, `elasticsearch_indices_get_exists_time_seconds`, `elasticsearch_indices_get_missing_time_seconds` | 获取请求累计时间 | 秒/秒 | 慢速获取表示读取路径上的压力增加 |

常见查询： 

```promql
rate(elasticsearch_indices_search_query_time_seconds[5m]) / rate(elasticsearch_indices_search_query_total[5m])
```

```promql
rate(elasticsearch_indices_search_fetch_time_seconds[5m]) / rate(elasticsearch_indices_search_fetch_total[5m])
```

故障排除建议：慢速查询应区分查询与获取。慢速查询更多与查询条件、索引结构和分片压力相关；慢速获取更常见于返回字段多、结果集大或磁盘读取慢的情况。

### 9.5 段、合并、刷新与事务日志

| 监控维度 | 指标 | 指标含义 | 常用单位/量纲 | 异常症状 |
| --- | --- | --- | --- | --- |
| 段数量 | `elasticsearch_indices_segments_count` | 当前段数量 | 数量 | 段过多会影响查询和内存 |
| 段内存 | `elasticsearch_indices_segments_memory_bytes` | 段占用的内存 | 字节 | 持续增加可能会挤压 JVM |
| 合并次数 | `elasticsearch_indices_merges_total` | 合并操作累计次数 | 次/秒 | 频繁合并表示写入或删除压力高 |
| 合并中文档数量 | `elasticsearch_indices_merges_docs_total` | 合并处理的文档累计数量 | 计数/秒 | 不断上升的合并工作量 |
| 合并数据量 | `elasticsearch_indices_merges_total_size_bytes_total` | 合并处理的累计数据 | 字节/秒 | 大型合并可能导致磁盘IO饱和 |
| 合并持续时间 | `elasticsearch_indices_merges_total_time_seconds_total` | 合并耗费的累计时间 | 秒/秒 | 缓慢的合并可能影响写入和查询性能 |
| 刷新次数 | `elasticsearch_indices_refresh_total` | 刷新累计次数 | 次数/秒 | 频繁刷新会增加开销 |
| 刷新持续时间 | `elasticsearch_indices_refresh_time_seconds_total` | 刷新累计时间 | 秒/秒 | 缓慢刷新影响近实时可见性 |
| 刷新操作次数 | `elasticsearch_indices_flush_total` | 刷新累计次数 | 次数/秒 | 频繁刷新可能与事务日志及写入压力相关 |
| 刷新持续时间 | `elasticsearch_indices_flush_time_seconds` | 刷新累计时间 | 秒/秒 | 缓慢刷新可能影响系统稳定性 |
| 事务日志操作 | `elasticsearch_indices_translog_operations` | 当前事务日志操作次数 | 数量 | 连续积累需要注意刷新 |
| 事务日志大小 | `elasticsearch_indices_translog_size_in_bytes` | 当前事务日志大小 | 字节 | 过大可能影响恢复时间 |
| 存储限流 | `elasticsearch_indices_store_throttle_time_seconds_total` | 索引存储限流累计时间 | 秒/秒 | 限流增加，写入受磁盘影响 |

调查建议：当写入压力高时，将合并、刷新、刷新和事务日志更改在一起。合并时间和存储限流增加通常表示磁盘已开始影响ES。

### 9.6 缓存和断路器

| 监控维度 | 指标 | 指标含义 | 常用单位/测量 | 异常行为 |
| --- | --- | --- | --- | --- |
| 查询缓存内存 | `elasticsearch_indices_query_cache_memory_size_bytes` | 查询缓存使用的内存 | 字节 | 过度使用可能会压缩 JVM |
| 查询缓存驱逐 | `elasticsearch_indices_query_cache_evictions` | 查询缓存驱逐的累计次数 | 次/秒 | 频繁驱逐表示缓存不稳定 |
| 字段数据内存 | `elasticsearch_indices_fielddata_memory_size_bytes` | 字段数据使用的内存 | 字节 | 字段数据高使用可能容易触发内存压力 |
| 字段数据驱逐 | `elasticsearch_indices_fielddata_evictions` | 字段数据驱逐的累计次数 | 次/秒 | 高查询或聚合压力 |
| 过滤器缓存驱逐 | `elasticsearch_indices_filter_cache_evictions` | 过滤器缓存驱逐的累计次数 | 次/秒 | 频繁的过滤器缓存失效 |
| 断路器估计大小 | `elasticsearch_breakers_estimated_size_bytes` | 断路器的估计内存 | 字节 | 接近限制时查询可能被拒绝 |
| 断路器限制 | `elasticsearch_breakers_limit_size_bytes` | 断路器限制 | 字节 | 用于计算断路器使用率 |
| 断路器触发 | `elasticsearch_breakers_tripped` | 断路器触发次数 | 次/增量 | 增长描述：因内存风险而被阻止的请求 |

常见查询： 

```promql
elasticsearch_breakers_estimated_size_bytes / elasticsearch_breakers_limit_size_bytes * 100
```

```promql
increase(elasticsearch_breakers_tripped[10m])
```

调查建议：聚合查询、排序和脚本查询容易增加 fielddata 和断路器使用。当断路器触发时，通常需要限制查询大小、优化索引映射或调整查询方法。

### 9.7 JVM, CPU，以及负载

| 监控维度 | 指标 | 指标含义 | 常用测量/单位 | 异常表现 |
| --- | --- | --- | --- | --- |
| JVM 已使用内存 | `elasticsearch_jvm_memory_used_bytes` | 当前 JVM 已用内存 | 字节 | 持续接近上限，GC 压力增加 |
| JVM 最大内存 | `elasticsearch_jvm_memory_max_bytes` | 可用最大值 JVM 内存 | 字节 | 用于计算 JVM 使用率 |
| JVM 已提交内存 | `elasticsearch_jvm_memory_committed_bytes` | JVM 已提交内存 | 字节 | 观察 JVM 内存分配 |
| JVM 内存池峰值 | `elasticsearch_jvm_memory_pool_peak_used_bytes` | 各内存池的峰值使用 | 字节 | 老年代峰值较高需要注意 |
| GC 次数 | `elasticsearch_jvm_gc_collection_seconds_count` | GC 发生次数 | 次/秒 | GC 频繁，延迟可能波动 |
| GC 时间 | `elasticsearch_jvm_gc_collection_seconds_sum` | GC 总时间 | 秒/秒 | GC 时间长可能影响查询和写入 |
| 进程 CPU | `elasticsearch_process_cpu_percent` | ES 进程 CPU 使用率 | 百分比 | 长时间高负载 CPU 可能表示查询或写入负载较大 |
| 系统负载 | `elasticsearch_os_load1`, `elasticsearch_os_load5`, `elasticsearch_os_load15` | 节点 1/5/15 分钟负载 | 负载值 | 负载高于 CPU cores 表示明显的任务排队 |
| 打开文件数量 | `elasticsearch_process_open_files_count` | ES 进程打开的文件数量 | 数量 | 接近系统限制可能影响索引文件访问 |

常见查询： 

```promql
elasticsearch_jvm_memory_used_bytes / elasticsearch_jvm_memory_max_bytes * 100
```

```promql
rate(elasticsearch_jvm_gc_collection_seconds_sum[5m])
```

调查建议：更大的 ES JVM 内存并不总是越大越好。 JVM 使用情况、GC 时间、fielddata、查询缓存和 breakers 应一起监控，以确定内存压力是由查询引起，还是堆大小与数据规模不匹配。

### 9.8 线程池与网络

| 监控维度 | 指标 | 指标含义 | 常用测量/单位 | 异常表现 |
| --- | --- | --- | --- | --- |
| 活跃线程 | `elasticsearch_thread_pool_active_count` | 线程池中活跃线程数 | 计数 | 长期高活跃线程数表示处理压力大 |
| 已完成的任务 | `elasticsearch_thread_pool_completed_count` | 线程池完成任务的累计数量 | 次/秒 | 用于观察处理吞吐量 |
| 被拒绝的任务 | `elasticsearch_thread_pool_rejected_count` | 线程池拒绝任务的累计数量 | 次/秒 | 增长表示线程池或队列已满 |
| 入站流量 | `elasticsearch_transport_rx_size_bytes_total` | 传输接收的累计字节数 | 字节/秒 | 节点间通信或请求流量增加 |
| 出站流量 | `elasticsearch_transport_tx_size_bytes_total` | 传输发送的累计字节数 | 字节/秒 | 由于分片重定位、查询或复制导致流量增加 |

常见查询： 

```promql
sum by (type) (rate(elasticsearch_thread_pool_rejected_count[5m]))
```

```promql
rate(elasticsearch_transport_rx_size_bytes_total[5m]) + rate(elasticsearch_transport_tx_size_bytes_total[5m])
```

调查建议：线程池拒绝是一个非常直接的业务风险信号。对于写操作拒绝，请检查批量/索引线程池；对于搜索拒绝，请检查搜索线程池，然后结合 CPU, JVM和磁盘IO确定瓶颈。

## 10. 应用服务监控

应用监控涵盖常见的服务器端请求、客户端依赖调用、运行时资源、协作编辑业务链路以及RS服务任务。应用指标的重点不是单个资源阈值，而是请求量、错误、延迟和依赖健康。

### 10.1 常见服务器端指标

| 监控维度 | 指标 | 指标含义 | 通用范围/单位 | 异常表现 |
| --- | --- | --- | --- | --- |
| 服务正常运行时间 | `up` | 应用导出器或指标端点是否可采集 | `0/1` | `0` 意味着指标不可访问或服务异常 |
| 构建信息 | `ego_build_info` | 应用构建版本、分支及其他信息 | 标签信息 | 用于验证发布版本 |
| 启动次数 | `ego_server_started_total` | 服务器启动的累计次数 | 次/增量 | 增加表示进程重启或发布 |
| 服务器请求 | `ego_server_handle_total` | 服务器请求的累计次数 | 次/秒 | 请求量的突然增加或减少需要结合业务背景进行判断 |
| 服务端耗时 | `ego_server_handle_seconds_count`, `ego_server_handle_seconds_bucket` | 服务端请求时间统计 | P50/P95/P99 | 延迟增加影响用户体验 | 

常见查询： 

```promql
sum by (service, method) (rate(ego_server_handle_total[5m]))
```

```promql
histogram_quantile(0.95, sum(rate(ego_server_handle_seconds_bucket[5m])) by (le, service, method))
```

排查建议：对于服务端异常，先检查请求量是否发生变化，再查看错误和延迟。如果延迟增加但资源不高，继续检查下游依赖调用和队列。

### 10.2 客户端依赖调用

| 监控维度 | 指标 | 指标含义 | 常用粒度/单位 | 异常行为 |
| --- | --- | --- | --- | --- |
| 客户端调用量 | `ego_client_handle_total` | 应用作为客户端调用下游的次数 | 次/秒 | 下游调用量突然增加，可能加大依赖压力 |
| 客户端延迟 | `ego_client_handle_seconds_count`, `ego_client_handle_seconds_bucket` | 下游调用延迟统计 | P50/P95/P99 | 下游慢可能导致当前服务变慢 |
| 客户端状态 | `ego_client_stats_gauge` | 客户端连接池或状态类指标 | 当前值 | 连接池耗尽、异常空闲连接等 |
| Kafka 生产延迟 | `kafka_produce_duration_seconds_bucket` | 应用生成所用时间 Kafka 消息 | P50/P95/P99 | 生产延迟增加，可能由于代理或网络问题 |

常用查询:

```promql
histogram_quantile(0.95, sum(rate(ego_client_handle_seconds_bucket[5m])) by (le, service, target, method))
```

调查建议：当某个业务接口响应缓慢时，将服务器端耗时与客户端依赖耗时进行对比。如果客户端耗时占比较高，应优先检查相应的下游服务、中间件或网络。

### 10.3 运行时与进程

| 监控维度 | 指标 | 指标含义 | 通用标准/单位 | 异常表现 |
| --- | --- | --- | --- | --- |
| Go goroutine | `go_goroutines` | Go 进程中的 goroutine 数量 | 计数 | 持续增长可能表示阻塞或内存泄漏 |
| Go GC 时长 | `go_gc_duration_seconds` | Go GC 时长 | 秒/百分位 | GC 时间增加可能影响延迟 |
| Go 堆内存 | `go_memstats_alloc_bytes`, `go_memstats_heap_inuse_bytes` | Go 堆分配与使用 | 字节 | 持续增长需要检查内存泄漏 |
| Go 系统内存 | `go_memstats_sys_bytes` | Go 运行时向系统申请的内存 | 字节 | 与以下内容一起观察 RSS |
| Go 栈内存 | `go_memstats_stack_inuse_bytes` | Goroutine 栈使用情况 | 字节 | 随 goroutine 增长而增加 |
| Node.js GC 次数 | `nodejs_gc_duration_seconds_count` | Node.js GC 次数 | 次/秒 | 频繁 GC 可能表示堆压力 |
| Node.js GC 时长 | `nodejs_gc_duration_seconds_sum` | Node.js GC 总时长 | 秒/秒 | GC 时长增加可能影响响应 |
| Node.js 堆空间 | `nodejs_heap_space_size_used_bytes` | 各堆空间的使用情况 Node.js 堆空间 | 字节 | 如果接近上限或持续增长需要注意 |
| 进程 CPU | `process_cpu_seconds_total` | 进程 CPU 时间 | 核心/秒 | 高 CPU 使用率 |
| 进程 RSS | `process_resident_memory_bytes` | 进程物理内存 | 字节 | 持续 RSS 增长 |
| 进程句柄 | `process_open_fds` | 进程中打开的文件描述符数量 | 数量 | 句柄泄漏，连接泄漏 |

调查建议：Go 的运行时指标和 Node.js 主要用于解释应用延迟和资源增加。当应用 P95 上升时，如果 GC 持续时间同时上升，应优先检查内存分配和对象生命周期。

### 10.4 协作编辑服务

| 监控维度 | 指标 | 指标含义 | 常用单位 | 异常指示 |
| --- | --- | --- | --- | --- |
| Kafka 消费者滞后 | `kafka_consumergroup_lag` | 协作编辑中相关消费者组的积压 | 计数 | 滞后增加可能导致事件处理延迟 |
| 处理时长 | `process_flow_duration_seconds_bucket` | 协作编辑过程的持续时间 | P50/P95/P99 | 文档协作环节减慢 |
| 处理数量 | `process_total` | 处理的总进程数量 | 次/秒 | 处理量异常变化 |
| 文件内容大小 | `file_content_size_bytes_bucket` | 文件内容大小分布 | 分桶统计 | 大文件比例增加可能影响处理时间 |
| 变更集持续时间 | `handle_changeset_cost_seconds_bucket` | 处理变更集所需时间 | P50/P95/P99 | 编辑同步延迟增加 |
| Modoc 计算次数 | `modocComputeCount` | Modoc 计算的数量 | 次/秒 | 计算量异常增加 |
| 无服务器调用次数 | `serverless_invocations` | 无服务器调用数量 | 次/秒 | 调用失败或激增可能影响链接 |

常见查询：

```promql
histogram_quantile(0.95, sum(rate(handle_changeset_cost_seconds_bucket[5m])) by (le))
```

调查建议：对于协作编辑链接， Kafka 应同时检查延迟、处理时长、变更集持续时间和文件大小。当大文件比例增加时，持续时间上升可能是正常的容量压力，而非单点故障。

### 10.5 RS 服务

| 监控维度 | 指标 | 指标含义 | 通用范围/单位 | 异常表现 |
| --- | --- | --- | --- | --- |
| HTTP 请求数量 | `http_requests_total` | 累计数量 HTTP 请求 | 次/秒 | 请求突然增加或减少 |
| HTTP 持续时间 | `http_requests_duration_seconds_bucket`, `http_requests_duration_seconds_sum`, `http_requests_duration_seconds_count` | HTTP 请求持续时间 | P50/P95/P99 | 接口延迟增加 |
| gRPC 请求数量 | `grpc_requests_total` | 累计数量 gRPC 请求 | 次/秒 | gRPC 调用异常 |
| gRPC 持续时间 | `grpc_requests_duration_seconds` | gRPC 请求持续时间 | P50/P95/P99 | 下游或内部处理变慢 |
| 导出任务持续时间 | `export_task_duration_milliseconds_count` | 导出任务处理数量和持续时间 | 毫秒/次 | 导出任务变慢或积压 |
| 导入任务持续时间 | `import_task_duration_milliseconds_count` | 导入任务进程数量和持续时间 | 毫秒 / 每个任务 | 减慢或堆积的导入任务 |
| 正在进行的导出任务 | `export_task_in_progress` | 当前正在执行的导出任务 | 数量 | 如果长时间不下降，表示任务卡住 |
| 正在进行的导入任务 | `import_task_in_progress` | 当前正在执行的导入任务 | 数量 | 如果长时间不下降，表示任务卡住 |
| Tokio 指标 | `tokio_metrics` | Rust Tokio 运行时指标 | 当前值 / 速率 | 运行时队列或任务调度异常 |
| jemalloc 指标 | `jemalloc` | 内存分配器指标 | 字节 / 数量 | 内存碎片或分配异常 |
| TCP 指标 | `tcp` | RS 服务 TCP 连接相关指标 | 数量 / 速率 | 连接压力或网络异常 |

调查建议：RS 服务应同时检查在线请求和长时间运行的任务，例如导入/导出。任务持续不减少通常比平均持续时间更可靠地表示“任务卡住”。

## 11. 指标读取和调查建议

### 11.1 一般调查顺序

| 步骤 | 观察项 | 目的 |
| --- | --- | --- |
| 1 | `up`，启动时间，Pod 准备就绪 | 确认服务是否仍在运行以及是否最近重启过 |
| 2 | 请求量，错误率， P95/P99 延迟 | 确定是否真正影响业务 |
| 3 | CPU，内存，磁盘，网络 | 确定是否存在资源瓶颈 |
| 4 | 下游依赖延迟， Kafka 滞后，慢数据库查询 | 确定是否因依赖造成变慢 |
| 5 | 发布版本，配置，流量变化 | 确定是否与变更相关 |

实际排查时，不要急着先看所有图表。先确认“是否有业务影响”，再找出“影响来自哪里”。例如，如果接口变慢，先查看应用的 P95，然后检查客户端依赖延迟；如果依赖正常，再回过头看服务的 CPU，GC，内存，以及容器节流情况。

### 11.2 常见异常组合

| 症状 | 常见指标表现 | 优先排查方向 |
| --- | --- | --- |
| 接口慢 | 应用 P95/P99 上升， CPU 不高 | 下游依赖，数据库查询缓慢， Kafka 延迟 |
| CPU 完全使用 | `container_cpu_usage_seconds_total` 高，限流高 | CPU 限制，热点接口，批处理任务 |
| 内存 OOM | 工作集接近极限，重启次数增加 | 内存泄漏，限制太小，大对象处理 |
| 磁盘慢 | iowait，IO繁忙，读/写延迟均上升 | 数据库， Kafka, MinIO，日志写入 |
| 网络异常 | 流量激增伴随丢包/错误 | 节点 NIC, CNI，链路，连接数 |
| Kafka 延迟 | `kafka_consumergroup_lag` 持续增加 | 消费者实例，消费时间，下游依赖 |
| Redis 背压 | 命中率下降，未命中上升 | 键过期策略，缓存穿透，容量 |
| MySQL 缓慢 | 慢查询，扫描，锁等待增加 | SQL，索引，锁，磁盘IO |
| MinIO 风险 | 离线磁盘，错误率，容量水平上升 | 磁盘，节点，Bucket增长，修复状态 |
| Elasticsearch 慢查询 | 搜索查询/获取时间增加，线程池拒绝上升 | 查询条件，索引结构， JVM，磁盘IO |
| Elasticsearch 慢写入 | 索引时间，合并时间，存储节流增加 | 写入峰值，刷新，合并，磁盘级别 |
