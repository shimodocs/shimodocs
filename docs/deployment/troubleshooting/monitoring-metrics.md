# Monitoring Metrics Reference

[← ShimoDocs Suite Deployment Documentation](../README.md)

This document organizes commonly used metrics in monitoring systems, covering nodes, containerd containers, Kubernetes clusters, middleware, and application services, providing a unified reference for daily inspections, capacity assessment, and troubleshooting.

The metric names are based on actual exporter metrics collected by Prometheus. Different versions of exporters may have slight differences, and actual investigation should rely on online query results as the final reference.

## Scope

| Category | Covered Objects |
| --- | --- |
| Node Monitoring | Linux hosts, system resources, disks, network, processes |
| Container Monitoring | Containers running on containerd, Pod container resources |
| Kubernetes Cluster | Nodes, Pods, Deployment, StatefulSet, Job, PVC, APIServer | 
| MySQL | MySQL instance, connection, query, cache, lock, network | 
| MongoDB | MongoDB instance, connection, operation, memory, network, replication buffer | 
| Redis | Redis instance, client, commands, memory, keyspace, hit rate | 
| Kafka | Broker, topic, partition, consumer group, latency, replica | 
| MinIO | Cluster nodes, disks, bucket S3 requests, object capacity | 
| Elasticsearch | Cluster health, node, shard, index, JVM, thread pool, network |
| Application Services | General server, client calls, collaborative editing, RS service, runtime |

## Metrics Reading Rules

| Metric Type | Read Method | Common PromQL Syntax | Description |
| --- | --- | --- | --- |
| Counter | Check the growth rate or increment within a time window | `rate(x_total[5m])`, `increase(x_total[5m])` | Request count, error count, byte count, and IO count usually belong to counters |
| Gauge | Check current value, average value, maximum value | `avg(x)`, `max(x)`, `sum(x)` | Memory, number of connections, capacity, and status values usually belong to gauges |
| Histogram | Check percentile latency | `histogram_quantile(0.95, sum(rate(x_bucket[5m])) by (le))` | Request latency, processing latency, and queue latency usually use histograms |
| Ratio | View Percentage | `A / B * 100` | Utilization rate, error rate, and hit rate all belong to ratio-type indicators. |

It is recommended not to directly copy fixed numbers as thresholds. For example, metrics such as CPU, memory, disk, number of connections, QPS, and latency should be evaluated in the context of business peak periods, capacity planning, and historical baselines. The abnormal behaviors documented are used to quickly identify risks and do not equate to final alert thresholds.

## 1. Node Service Monitoring

Node monitoring is used to determine whether the host is healthy, whether resources are sufficient, and whether there are disk or network bottlenecks. Node metrics mainly come from node-exporter, combined with the system process dashboard for process-level localization.

### 1.1 Basic Status

| Monitoring Dimension | Metric | Metric Meaning | General Standard/Unit | Abnormal Behavior |
| --- | --- | --- | --- | --- |
| Node Availability | `up` | Whether the exporter or collection target is accessible | `1` indicates collectible, `0` indicates not collectible | Continuous `0` indicates issues with the node, network, or exporter |
| Start Time | `node_boot_time_seconds` | Last start time of the node | Unix timestamp | Changes in start time indicate the node has restarted |
| Node Information | `node_uname_info`, `node_os_info` | Operating system, kernel, and distribution information | Label information | Used to verify node version, not directly as an alert metric |

Troubleshooting Recommendations: First check `up`, then `node_boot_time_seconds`. If the node cannot be collected and the startup time has recently changed, priority should be given to checking the host reboot, network ACL, and node-exporter process status.

### 1.2 CPU Metrics

| Monitoring Dimension | Metric | Metric Meaning | General Standard/Unit | Abnormal Performance |
| --- | --- | --- | --- | --- |
| CPU Usage | `node_cpu_seconds_total` | Cumulative CPU time for each core in different modes | Percentage | `user` and `system` remaining high for a long time, indicating the node’s computing capacity is tight |
| Idle CPU | `node_cpu_seconds_total{mode="idle"}` | CPU idle time | Percentage | Consistently low idle time may cause increased queuing and latency |
| IO Wait | `node_cpu_seconds_total{mode="iowait"}` | Time CPU waits for disk IO | Percentage | A continuously increasing iowait usually indicates slow disks or storage links |
| System Load | `node_load1`, `node_load5`, `node_load15` | 1/5/15 minute average load | Load value | Load consistently higher than the number of cores indicates noticeable task queuing on CPU cores |
| CPU Pressure | `node_pressure_cpu_waiting_seconds_total` | Cumulative CPU PSI Wait Time | sec/sec | Severe CPU resource contention, processes are waiting for CPU scheduling |

Common queries:

```promql
100 - avg by (instance) (rate(node_cpu_seconds_total{mode="idle"}[5m])) * 100
```

```promql
avg by (instance) (rate(node_cpu_seconds_total{mode="iowait"}[5m])) * 100
```

Investigation Recommendations: When CPU usage is high, first distinguish `user`, `system`, and `iowait`. High `user` is mainly due to business computing pressure, high `system` may be related to system calls and network packet processing, while high `iowait` requires checking disk throughput, IOPS, and latency.

### 1.3 Memory Metrics

| Monitoring Dimension | Metric | Metric Meaning | Common Unit | Abnormal Performance |
| --- | --- | --- | --- | --- |
| Total Memory | `node_memory_MemTotal_bytes` | Total physical memory of the node | Bytes | Used for calculating usage |
| Available Memory | `node_memory_MemAvailable_bytes` | Memory that the system can allocate to processes | Bytes / Percentage | Persistently low available memory can easily trigger OOM or frequent reclamation |
| Free Memory | `node_memory_MemFree_bytes` | Memory that is completely unused | Bytes | Cannot be used alone to determine memory pressure in Linux |
| Memory Pressure | `node_pressure_memory_waiting_seconds_total` | Cumulative memory PSI wait time | Seconds / Seconds | Increase in memory reclamation or allocation wait |
| OOM Count | `node_vmstat_oom_kill` | Number of OOM kills by the system | Count / Increment | When it increases, identify the killed process and memory peak |

Common queries:

```promql
(1 - node_memory_MemAvailable_bytes / node_memory_MemTotal_bytes) * 100
```

```promql
increase(node_vmstat_oom_kill[10m])
```

Investigation Recommendation: Do not rely solely on `MemFree` to understand memory conditions. Actual availability should be more accurately assessed through `MemAvailable`, combined with container working set memory, process RSS, and OOM records.

### 1.4 Disk Capacity and Inodes

| Monitoring Dimension | Metric | Meaning of Metric | Common Measurement/Unit | Abnormal Behavior |
| --- | --- | --- | --- | --- |
| Total File System | `node_filesystem_size_bytes` | Total capacity of the mount point | Bytes | Used to calculate disk usage |
| Available File System | `node_filesystem_avail_bytes` | Space available to regular users | Bytes | Insufficient available space may cause write failures |
| Free File System | `node_filesystem_free_bytes` | Free space in the file system | Bytes | Includes space reserved for root; usually considered together with `avail` |
| Read-Only Status | `node_filesystem_readonly` | Whether the file system is read-only | `0/1` | When `1`, business writes will fail |
| Total Inodes | `node_filesystem_files` | Total number of inodes in the file system | Count | Needs special attention in small file scenarios |
| Remaining Inodes | `node_filesystem_files_free` | Number of remaining inodes | Number/Percentage | When inodes run out, files cannot be created even if there is still disk space |

Common queries:

```promql
(1 - node_filesystem_avail_bytes{fstype!~"tmpfs|overlay"} / node_filesystem_size_bytes{fstype!~"tmpfs|overlay"}) * 100
```

```promql
(1 - node_filesystem_files_free / node_filesystem_files) * 100
```

Investigation Recommendation: Disk capacity alerts should be checked by mount point, especially for data disks, log disks, and container runtime directories. High inode usage usually comes from a large number of small files, log slices, or uncleared temporary files.

### 1.5 Disk IOPS, Throughput, and Latency

| Monitoring Dimension | Metric | Metric Meaning | Common Measurement/Unit | Abnormal Performance |
| --- | --- | --- | --- | --- |
| Read IOPS | `node_disk_reads_completed_total` | Number of completed disk read requests | times/second | When read IOPS approaches device limit, read latency increases |
| Write IOPS | `node_disk_writes_completed_total` | Number of completed disk write requests | requests/sec | Write backlog, slow log or database commits |
| Read Throughput | `node_disk_read_bytes_total` | Total bytes read from disk | bytes/sec | High throughput and high iowait indicate storage is busy |
| Write Throughput | `node_disk_written_bytes_total` | Total bytes written to disk | bytes/sec | Long-term high write throughput may affect databases and object storage |
| Read Time | `node_disk_read_time_seconds_total` | Cumulative time of read requests | sec/sec | Increased read latency |
| Write Time | `node_disk_write_time_seconds_total` | Cumulative time of write requests | sec/sec | Increase in write latency |
| IO Busy | `node_disk_io_time_seconds_total` | Cumulative time disk spends processing IO | % | Applications wait for IO when near full load |
| Weighted IO Time | `node_disk_io_time_weighted_seconds_total` | IO time considering queue length | sec/sec | Queue backlog indicates severe device queue |
| IO Pressure | `node_pressure_io_waiting_seconds_total` | Cumulative IO PSI Wait Time | sec/sec | The process waits longer for IO |

Common queries:

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

Investigation Recommendations: When checking issues, do not just look at disk capacity. Even if the capacity is normal, business performance can degrade when IOPS throughput, IO busy, and iowait all increase simultaneously. Heavy IO services like databases, Kafka, and MinIO should pay attention to write latency and queues.

### 1.6 Network Metrics

| Monitoring Dimension | Metric | Metric Meaning | Common Unit | Signs of Abnormality |
| --- | --- | --- | --- | --- |
| Inbound Traffic | `node_network_receive_bytes_total` | Cumulative bytes received by the network card | Bytes/sec | A sudden increase in inbound traffic may be due to a surge in requests or data synchronization |
| Outbound Traffic | `node_network_transmit_bytes_total` | Cumulative bytes sent by the network card | Bytes/sec | A sudden increase in outbound traffic may be due to downloading, backup, or copying |
| Inbound Errors | `node_network_receive_errs_total` | Cumulative number of received erroneous packets | Count/sec | Network card, link, or driver issues |
| Transmission Errors | `node_network_transmit_errs_total` | Cumulative number of transmitted error packets | Count/sec | Link issues or network card queue problems |
| Received Packet Loss | `node_network_receive_drop_total` | Cumulative number of discarded received packets | Count/sec | Kernel queue or network card cannot keep up |
| Packet loss | `node_network_transmit_drop_total` | Cumulative packet loss | times/second | Egress congestion or NIC queue pressure |

Common queries:

```promql
rate(node_network_receive_bytes_total{device!~"lo|veth.*|cni.*"}[5m])
```

```promql
rate(node_network_transmit_bytes_total{device!~"lo|veth.*|cni.*"}[5m])
```

```promql
rate(node_network_receive_drop_total[5m]) + rate(node_network_transmit_drop_total[5m])
```

Investigation Recommendations: For network anomalies, traffic, error packets, and packet loss should be checked simultaneously. High traffic alone does not necessarily indicate a fault; high traffic accompanied by error packets or packet loss is more likely a problem with the link or the host network stack.

### 1.7 TCP, File Handles, and System Stress

| Monitoring Dimension | Metric | Metric Meaning | Common Unit/Measurement | Abnormal Behavior |
| --- | --- | --- | --- | --- |
| Current TCP Connections | `node_netstat_Tcp_CurrEstab` | Number of currently established TCP connections | Number | Sudden increase in connection count may indicate traffic peak or connection leakage |
| TIME_WAIT | `node_sockstat_TCP_tw` | Number of TIME_WAIT connections | Number | Too many short connections may exhaust ports or increase kernel pressure |
| TCP Allocated | `node_sockstat_TCP_alloc` | Number of allocated TCP sockets | Quantity | Continuous increase in socket numbers requires investigation of connection release situation |
| TCP In Use | `node_sockstat_TCP_inuse` | Number of TCP sockets in use | Quantity | Increased connection pressure |
| TCP Orphaned | `node_sockstat_TCP_orphan` | Number of orphaned sockets | Quantity | Abnormal increase may be related to abnormal connection closures |
| File Handle Usage | `node_filefd_allocated` | Number of system-allocated file handles | Units | Excessive usage may affect new connections and file openings |
| File Handle Limit | `node_filefd_maximum` | System File Handle Limit | pcs | Used to calculate handle usage rate |

Common Queries: 

```promql
node_filefd_allocated / node_filefd_maximum * 100
```

Investigation recommendation: File handles and TCP connections are usually considered together. When the number of server connections surges, if the system handles approach their limits, applications may experience failures in accepting connections, opening files, or relying on connections.

### 1.8 Process Monitoring

| Monitoring Dimension | Metric | Metric Meaning | Common Measurement/Unit | Abnormal Behavior |
| --- | --- | --- | --- | --- |
| Process CPU | `process_cpu_seconds_total` | Total CPU process time | sec/sec | Long-term high CPU usage by a single process |
| Physical Memory | `process_resident_memory_bytes` | Process RSS memory | bytes | Continuous growth of RSS may indicate memory leak |
| Virtual Memory | `process_virtual_memory_bytes` | Process Virtual Memory | Bytes | Abnormal growth needs to be evaluated together with RSS |
| Open Handles | `process_open_fds` | Number of file handles opened by the process | Count | Continuous growth may indicate handle leaks |
| Max Handles | `process_max_fds` | Maximum number of file handles the process can open | Count | Used to calculate process handle utilization |
| Process Start Time | `process_start_time_seconds` | Process start time | Unix Timestamp | Changes in start time indicate process restart |

Investigation Recommendations: Process metrics are used to locate specific services for node-level issues. When the node CPU is high, check process CPU; when node memory pressure is high, check RSS; when the node handle count is high, check `process_open_fds`.

## 2. containerd Container Monitoring

Container monitoring mainly comes from kubelet/cAdvisor, reflecting the resource usage of containers managed by containerd. The documentation continues to use `container_*` Prometheus metric names, but in actual operation, the underlying container runtime is containerd.

### 2.1 Container CPU

| Monitoring Dimension | Metric | Metric Meaning | General Range/Unit | Abnormal Performance |
| --- | --- | --- | --- | --- |
| CPU Usage | `container_cpu_usage_seconds_total` | Total CPU Container Usage Time | Core/Second | Usage close to the limit for a long time may lead to increased business latency |
| CPU Throttled Time | `container_cpu_cfs_throttled_seconds_total` | Total time CPU was limited by CFS | Second/Second | Significant CPU throttling indicates limits are too tight or load is too high |
| CPU Quota | `container_spec_cpu_quota` | Container CPU Quota | Quota Value | Used to identify if CPU limits have been set |

Common Queries: 

```promql
sum by (namespace, pod, container) (rate(container_cpu_usage_seconds_total{container!="",image!=""}[5m]))
```

```promql
sum by (namespace, pod, container) (rate(container_cpu_cfs_throttled_seconds_total{container!="",image!=""}[5m]))
```

Investigation Recommendations: High container CPU usage does not necessarily require scaling. First, check whether it is being throttled; next, check whether the Pod's requests/limits are set too low; and finally, consider service request latency to determine whether it truly impacts business operations.

### 2.2 Container Memory

| Monitoring Dimension | Metric | Metric Meaning | Common Unit | Abnormal Behavior |
| --- | --- | --- | --- | --- |
| RSS Memory | `container_memory_rss` | Container anonymous pages and RSS memory | Bytes | Continuous growth reflects the actual process memory pressure more closely |
| Used Memory | `container_memory_usage_bytes` | Total memory usage of the container | Bytes | Includes cache, cannot determine a leak based on this alone |
| Working Set Memory | `container_memory_working_set_bytes` | Container Active Working Set Memory | Bytes | Approaching the limit may cause OOMKilled |
| Memory Limit | `container_spec_memory_limit_bytes` | Container Memory Limit | Bytes | Used to calculate memory usage rate |

Common Queries:

```promql
container_memory_working_set_bytes{container!="",image!=""} / container_spec_memory_limit_bytes{container!="",image!=""} * 100
```

Investigation Recommendations: For memory risks in business containers, priority should be given to focusing on the working set and RSS. `usage_bytes` is mainly affected by page cache, suitable for capacity observation, but not suitable as the sole basis for OOM judgment.

### 2.3 Container Disk and Temporary Storage

| Monitoring Dimension | Metric | Metric Meaning | Common Measurement/Unit | Abnormal Performance |
| --- | --- | --- | --- | --- |
| Read Throughput | `container_fs_reads_bytes_total` | The cumulative bytes read by the container from disk | Bytes/second | A sudden spike in read traffic may indicate scanning, importing, or cache source pulling |
| Write Throughput | `container_fs_writes_bytes_total` | The cumulative number of bytes written by the container to disk | Bytes/second | Write peaks may cause node IO pressure |
| Read IOPS | `container_fs_reads_total` | The number of read requests from the container | Operations/second | Frequent small block reads may increase IO wait |
| Write IOPS | `container_fs_writes_total` | The number of write requests from the container | Operations/second | Excessive writing of logs or temporary files |
| Filesystem Usage | `container_fs_usage_bytes` | Container filesystem usage | Bytes | Accumulation of temporary files or logs |
| File System Limit | `container_fs_limit_bytes` | Container File System Limit | Bytes | Writes may fail when near the limit |

Common Queries: 

```promql
sum by (namespace, pod, container) (rate(container_fs_reads_bytes_total{container!="",image!=""}[5m]))
```

```promql
sum by (namespace, pod, container) (rate(container_fs_writes_bytes_total{container!="",image!=""}[5m]))
```

Investigation Recommendations: When there is abnormal disk writing in containers, first check the Pod log volume, temporary file directories, and batch tasks. When node disk IO is high, container FS metrics can be used to identify which Pod is writing.

### 2.4 Container Network

| Monitoring Dimension | Metric | Metric Meaning | Common Range/Unit | Abnormal Performance |
| --- | --- | --- | --- | --- |
| Inbound Traffic | `container_network_receive_bytes_total` | Total bytes received by the container | Bytes/second | Sudden increase in request traffic or replication traffic |
| Outbound Traffic | `container_network_transmit_bytes_total` | Total bytes sent by the container | Bytes/second | Increase in download, synchronization, source fetching, or export traffic |
| Inbound Packet Loss | `container_network_receive_packets_dropped_total` | Total number of packets discarded when the container receives data | times/sec | Packet loss caused by network stack or node pressure |
| Outbound Packet Loss | `container_network_transmit_packets_dropped_total` | Total number of packets discarded when the container transmits data | times/sec | Egress congestion, NIC queue, or CNI issues |

Common queries:

```promql
sum by (namespace, pod) (rate(container_network_receive_bytes_total[5m]))
```

```promql
sum by (namespace, pod) (rate(container_network_transmit_bytes_total[5m]))
```

Investigation recommendation: Container network should be analyzed together with node NIC metrics. If packet loss increases at the Pod level, but there is no anomaly at the node, continue to check CNI, iptables, and the load of the node where the Pod resides.

### 2.5 Container Threads and Lifecycle

| Monitoring Dimension | Metric | Metric Meaning | Common Range/Unit | Abnormal Behavior |
| --- | --- | --- | --- | --- |
| Number of Threads | `container_threads` | Number of threads inside the container | Quantity | Continuous growth of threads may indicate a thread leak |
| Last Seen | `container_last_seen` | The last time cAdvisor saw the container | Unix timestamp | Long time without update may indicate the container has exited or collection anomaly |
| Restart Count | `kube_pod_container_status_restarts_total` | Total number of container restarts | Count/Increment | Frequent restarts indicate crashes, probe failures, or OOM |
| Waiting Reason | `kube_pod_container_status_waiting_reason` | Reason why the container is in a waiting state | Label Value | `CrashLoopBackOff`, `ImagePullBackOff`, etc., need to be addressed |
| Running Status | `kube_pod_container_status_running` | Whether the container is running | `0/1` | Critical container not `1` indicates the service is unavailable |

Investigation Recommendations: For container anomalies, first check the status reason, then look at the number of restarts and the time of the most recent restart. If restarts are frequent, continue the investigation using application logs, OOM events, and probe configurations.

## 3. Kubernetes Cluster Monitoring

Kubernetes monitoring is used to assess cluster resource usage, control plane health, workload replica status, and storage object status. The main metrics come from kube-state-metrics, kubelet, and APIServer.

### 3.1 Node Capacity and Schedulable Resources

| Monitoring Dimension | Metric | Metric Meaning | Common Range/Unit | Abnormal Performance |
| --- | --- | --- | --- | --- |
| Node Capacity | `kube_node_status_capacity` | Total capacity of the node | CPU, memory, number of Pods, etc. | Used for capacity planning |
| Allocatable Resources | `kube_node_status_allocatable` | Schedulable resources of the node | CPU, memory, number of Pods, etc. | Insufficient schedulable resources will cause Pods to be in Pending state |
| Node Conditions | `kube_node_status_condition` | Node Ready, MemoryPressure, and other statuses | `0/1` | Abnormal Ready or occurring Pressure requires immediate attention |
| Unschedulable | `kube_node_spec_unschedulable` | Whether the node is marked as unschedulable | `0/1` | When set to '1', the node will not schedule new Pods |
| Node Information | `kube_node_info` | Node version, kernel, container runtime information | Label Information | Used to troubleshoot version differences |

Troubleshooting tip: When a Pod is Pending, first check allocatable resources and requests, then check whether the node is 'unschedulable', and finally check if node conditions indicate resource pressure. 

### 3.2 Pod Status 

| Monitoring Dimension | Metric | Metric Meaning | Common Caliber/Unit | Abnormal Behavior |
| --- | --- | --- | --- | --- |
| Pod Information | `kube_pod_info` | Information such as the Pod's namespace, node, etc. | Label Information | Used to locate Pod distribution |
| Pod Phase | `kube_pod_status_phase` | States such as Pending, Running, Succeeded, Failed | `0/1` | Pending/Failed indicates scheduling or running anomalies |
| Pod Readiness | `kube_pod_status_ready` | Whether the Pod is ready | `0/1` | Not ready can affect service availability |
| Pod Reason | `kube_pod_status_reason` | Reason for Pod abnormality | Label Value | Evicted, NodeLost, etc. need investigation |
| Container Restart | `kube_pod_container_status_restarts_total` | Number of container restarts | times/increment | Increase in restarts indicates stability issues |
| Container Waiting | `kube_pod_container_status_waiting` | Whether the container is in a waiting state | `0/1` | If the waiting state persists, the Pod cannot provide services normally |
| Reason for Waiting | `kube_pod_container_status_waiting_reason` | Reason for the waiting state | label value | Image pull failure, CrashLoop, etc. |
| Container Termination | `kube_pod_container_status_terminated` | Whether the container has terminated | `0/1` | Unexpected termination, requires checking restart and logs |

Common queries:

```promql
sum by (namespace, phase) (kube_pod_status_phase == 1)
```

```promql
increase(kube_pod_container_status_restarts_total[10m])
```

Investigation Recommendations: When a Pod anomaly occurs, do not only check the Pod phase. The ready status, reason, and container waiting reason can better explain the specific problem.

### 3.3 Resource Requests and Limits

| Monitoring Dimension | Metric | Metric Meaning | Common Measurement/Unit | Abnormal Behavior |
| --- | --- | --- | --- | --- |
| Requested Resource | `kube_pod_container_resource_requests` | Container request | CPU, Memory | Too high a request can affect scheduling, too low can affect stability |
| Resource Limit | `kube_pod_container_resource_limits` | Container limit | CPU, Memory | Too low a limit may cause CPU throttling or OOM |
| Node Allocatable Resources | `kube_node_status_allocatable` | Resources available for scheduling on the node | CPU, Memory | Used to calculate cluster resource allocation rate |
| Container Usage | `container_cpu_usage_seconds_total`, `container_memory_working_set_bytes` | Actual CPU and memory usage | Cores/sec, Bytes | Used to assess whether requests/limits are reasonable |

Common Queries:

```promql
sum(kube_pod_container_resource_requests{resource="cpu"}) / sum(kube_node_status_allocatable{resource="cpu"}) * 100
```

```promql
sum(kube_pod_container_resource_requests{resource="memory"}) / sum(kube_node_status_allocatable{resource="memory"}) * 100
```

Investigation Recommendation: Resource planning should consider both 'requested value' and 'actual usage value'. Looking at requests alone may misjudge business pressure, while focusing only on usage may overlook scheduling capacity.

### 3.4 Number of Workload Replicas

| Monitoring Dimension | Metric | Metric Meaning | Common Range/Unit | Abnormal Performance |
| --- | --- | --- | --- | --- |
| Deployment Replicas | `kube_deployment_status_replicas` | Current number of Deployment replicas | units | Inconsistent with expected replicas |
| Updated Replicas | `kube_deployment_status_replicas_updated` | Number of replicas updated to the new version | units | Does not increase for a long time during release |
| Unavailable Replicas | `kube_deployment_status_replicas_unavailable` | Number of Unavailable Replicas | Units | Will decrease when service capacity is greater than 0 |
| StatefulSet Replicas | `kube_statefulset_status_replicas` | Current Number of StatefulSet Replicas | Units | Abnormal replicas in stateful service |
| StatefulSet Ready | `kube_statefulset_status_replicas_ready` | Number of Ready StatefulSet Replicas | Units | Service is incomplete if Ready is less than desired replicas |

Investigation Recommendations: When an anomaly is reported, please check `updated` and `unavailable`. For StatefulSet anomalies, pay attention to PVC, Pod startup order, and node affinity.

### 3.5 Jobs and Batch Tasks

| Monitoring Dimension | Metric | Metric Meaning | General Standard/Unit | Anomalous Behavior |
| --- | --- | --- | --- | --- |
| Running Jobs | `kube_job_status_active` | Number of currently active jobs | Count | Long-term activity may indicate a job is stuck |
| Failed Jobs | `kube_job_status_failed` | Number of job failures | Count | An increase in failures requires checking job logs |
| Successful Tasks | `kube_job_status_succeeded` | Number of successfully completed tasks | Count | Used to determine task completion status |
| Completion Time | `kube_job_status_completion_time` | Task completion time | Unix timestamp | Missing completion time may indicate the task was not completed |

Investigation Suggestion: When batch tasks encounter anomalies, `active`, `failed`, and `succeeded` should be checked together; only looking at failures may miss tasks stuck for a long time.

### 3.6 PVC and Storage Objects

| Monitoring Dimension | Metric | Metric Meaning | General Standard/Unit | Abnormal Performance |
| --- | --- | --- | --- | --- |
| PVC Status | `kube_persistentvolumeclaim_status_phase` | PVC binding, pending, and other statuses | `0/1` | Pending will cause the Pod to be unable to mount storage |
| PVC Requested Capacity | `kube_persistentvolumeclaim_resource_requests_storage_bytes` | Capacity of storage requested by PVC | Bytes | Used for capacity planning and quota management |

Troubleshooting recommendations: When a stateful service fails to start, in addition to checking the Pod, you should also check whether the PVC is bound, whether the storage class is available, and whether the underlying storage capacity is insufficient.

### 3.7 APIServer, etcd, and Control Plane

| Monitoring Dimension | Metric | Metric Meaning | Common Unit/Measure | Abnormal Behavior |
| --- | --- | --- | --- | --- |
| APIServer Request Count | `apiserver_request_total` | APIServer Cumulative Requests | requests/sec | Sudden spikes in requests may come from controllers, kubectl, or business components |
| APIServer Latency | `apiserver_request_duration_seconds_bucket` | APIServer Request Duration Segments | P50/P95/P99 | Increased latency will affect scheduling, deployment, and controller synchronization |
| etcd Latency | `etcd_request_duration_seconds_bucket` | etcd Request Duration Buckets | P50/P95/P99 | Slow etcd can slow down the entire control plane |
| Queue Wait | `workqueue_queue_duration_seconds_bucket` | Controller Queue Wait Duration | Percentile Duration | Queue backlog, slowing resource state synchronization |
| Queue Processing | `workqueue_work_duration_seconds_bucket` | Controller Processing Duration | Percentile Duration | Controller processing slowdown |

Common Queries:

```promql
sum by (verb, resource) (rate(apiserver_request_total[5m]))
```

```promql
histogram_quantile(0.95, sum(rate(apiserver_request_duration_seconds_bucket[5m])) by (le, verb, resource))
```

Investigation Recommendations: Control plane issues typically manifest as slow deployments, slow Pod status updates, and slow responses. When both APIServer latency and etcd latency increase, kubectl should prioritize checking etcd, disk IO, and control plane node load.

## 4. MySQL Monitoring

MySQL monitoring is used to observe instance availability, connection pressure, SQL request volume, slow queries, cache hit rate, temporary tables, lock waits, file handles, and network throughput.

### 4.1 Instance Status and Request Volume

| Monitoring Dimension | Metric | Metric Meaning | General Range/Unit | Abnormal Performance |
| --- | --- | --- | --- | --- |
| Instance Alive | `up` | Whether the MySQL exporter can be collected | `0/1` | When the `0` instance, network, or exporter is abnormal |
| Runtime | `mysql_global_status_uptime` | MySQL runtime | seconds | A decrease indicates instance restart |
| Total queries | `mysql_global_status_queries` | Cumulative query count | times/second | QPS peak may indicate business peak or abnormal requests |
| Issues | `mysql_global_status_questions` | Cumulative number of client-initiated statements | times/second | Should be viewed with queries to assess request pressure |
| Command statistics | `mysql_global_status_commands_total` | Cumulative counts of various commands | times/second | Can distinguish commands such as select, insert, update, delete |

Common queries: 

```promql
rate(mysql_global_status_queries[5m])
```

```promql
sum by (command) (rate(mysql_global_status_commands_total[5m]))
```

Investigation Suggestions: When QPS rises, first check the command distribution. If `select` increases with scan-type metrics, focus on indexes and slow queries; if write commands increase, continue monitoring lock waits, disk IO, and host write latency.

### 4.2 Connections and Threads

| Monitoring Dimension | Metric | Metric Meaning | Common Unit/Dimension | Abnormal Performance |
| --- | --- | --- | --- | --- |
| Current Connections | `mysql_global_status_threads_connected` | Number of threads currently connected | Quantity | Approaching the limit may cause new connections to fail |
| Active Threads | `mysql_global_status_threads_running` | Number of threads currently executing | Quantity | Continuous increase usually indicates slow execution or lock waiting; SQL execution is slow or waiting for locks |
| Historical Maximum Connections | `mysql_global_status_max_used_connections` | Maximum number of connections historically used | Quantity | Approaching the maximum number of connections indicates that the connection pool needs to be evaluated |
| Maximum Connections | `mysql_global_variables_max_connections` | MySQL maximum connections configuration | Quantity | Used to calculate connection usage rate |
| Abnormal Client | `mysql_global_status_aborted_clients` | Total number of abnormal client disconnections | times/second | Network issues, timeouts, or client exceptions |
| Connection Failure | `mysql_global_status_aborted_connects` | Total number of connection failures | times/second | Authentication errors, connection limits, network exceptions, etc. |

Common Queries:

```promql
mysql_global_status_threads_connected / mysql_global_variables_max_connections * 100
```

Investigation Recommendation: A high number of connections does not necessarily indicate a slow database; it could also be caused by improper configuration of the application's connection pool. `Threads_running` Persistently high levels are more concerning, as they usually correspond to SQL execution or lock waiting issues.

### 4.3 Slow Queries, Scans, and Sorting

| Monitoring Dimension | Metric | Metric Meaning | Common Measurement/Unit | Abnormal Behavior |
| --- | --- | --- | --- | --- |
| Slow Queries | `mysql_global_status_slow_queries` | Cumulative count of slow queries | times/second | An increase indicates more slow queries (SQL) |
| Full Join Scans | `mysql_global_status_select_full_join` | Number of joins without indexes | times/second | Indicates a possible lack of indexed join conditions |
| Full Table Scan | `mysql_global_status_select_scan` | Number of full table scans | times/sec | Full table scans on large tables will reduce instance performance |
| Sort Merge | `mysql_global_status_sort_merge_passes` | Number of times sorting requires multiple merges | times/sec | Insufficient sort buffer or too much data to sort |

Investigation Suggestion: When slow queries increase, check if it is related to business release periods and change records. If SQL scan and sort metrics rise, it usually requires reviewing the slow log, execution plan, and index design.

### 4.4 InnoDB Buffer Pool

| Monitoring Dimension | Metric | Metric Meaning | Common Unit/Measure | Abnormal Manifestation |
| --- | --- | --- | --- | --- |
| Buffer Pool Size | `mysql_global_variables_innodb_buffer_pool_size` | InnoDB buffer pool configured size | Bytes | If too small, disk reads will increase |
| Buffer Pool Pages | `mysql_global_status_buffer_pool_pages` | Number of pages in various types of buffer pools | Pages | Used to monitor dirty pages, free pages, data pages, and other pages |
| Page Size | `mysql_global_status_innodb_page_size` | InnoDB page size | Bytes | Used to convert the number of pages into capacity |

Investigation Recommendation: When the buffer pool hit rate is low, the database will access the disk more frequently. It is necessary to evaluate it together with the node's disk read throughput, read IOPS, and iowait.

### 4.5 Temporary Tables, Table Cache, and File Handles

| Monitoring Dimension | Metric | Metric Meaning | Common Unit/Dimension | Abnormal Performance |
| --- | --- | --- | --- | --- |
| Temporary Tables | `mysql_global_status_created_tmp_tables` | Total number of temporary tables created | times/second | Increased query complexity |
| Disk Temporary Tables | `mysql_global_status_created_tmp_disk_tables` | Total number of disk temporary tables created | times/second | Increased disk IO pressure, SQL may slow down |
| Temporary Files | `mysql_global_status_created_tmp_files` | Total number of temporary files created | times/second | Increase in temporary files |
| Table Lock Immediate | `mysql_global_status_table_locks_immediate` | Number of Immediate Table Lock Acquisitions | times/second | Normal Reference Indicator |
| Table Lock Wait | `mysql_global_status_table_locks_waited` | Number of Table Lock Waits | times/second | Lock Contention Increase |
| Table Cache Hit | `mysql_global_status_table_open_cache_hits` | Number of Table Cache Hits | times/second | Low hit rate may indicate frequent table opens |
| Table Cache Miss | `mysql_global_status_table_open_cache_misses` | Number of Table Cache Misses | times/second | Table cache needs evaluation |
| Table Cache Overflow | `mysql_global_status_table_open_cache_overflows` | Number of times table cache overflow occurred | times/second | Insufficient configuration or too many tables |
| Open Tables | `mysql_global_status_open_tables` | Current number of open tables | count | Risk increases when approaching cache limit |
| Table Cache Configuration | `mysql_global_variables_table_open_cache` | Configured cache value for tables | count | Used to calculate usage rate |
| Open Files | `mysql_global_status_open_files` | Current number of open files | count | May affect SQL execution when approaching file limit |
| File Limit | `mysql_global_variables_open_files_limit` | MySQL File Handle Limit | Count | Used to calculate file handle usage rate |

Troubleshooting Suggestions: Temporary tables, lock waits, and table cache misses often occur alongside slow queries. When disk temporary tables increase, attention should be paid to node write IO, disk latency, and SQL sorting/grouping.

### 4.6 Network Throughput

| Monitoring Dimension | Metric | Metric Meaning | Common Unit | Abnormal Performance |
| --- | --- | --- | --- | --- |
| Inbound Traffic | `mysql_global_status_bytes_received` | Cumulative bytes received by MySQL | Bytes/second | Increase in request body or write traffic |
| Outbound Traffic | `mysql_global_status_bytes_sent` | Total Bytes Sent MySQL | Bytes/Second | Large queries, full table scans, and bulk exports will increase outbound traffic |

Common Queries:

```promql
rate(mysql_global_status_bytes_received[5m])
```

```promql
rate(mysql_global_status_bytes_sent[5m])
```

Investigation Recommendations: When MySQL outbound traffic suddenly increases, attention should usually be paid to large result sets, export tasks, and unpaginated queries.

## 5. MongoDB Monitoring

MongoDB monitoring is used to observe instance status, number of connections, operation volume, query scans, memory usage, network throughput, and replication buffer conditions.

### 5.1 Instances and Connections

| Monitoring Dimension | Metric | Metric Meaning | Common Measurement/Unit | Abnormal Performance |
| --- | --- | --- | --- | --- |
| Instance Alive | `up` | Whether the Mongo exporter can collect data | `0/1` | If `0`, the instance or exporter is abnormal |
| Uptime | `mongodb_ss_uptime` | MongoDB runtime | seconds | A small value indicates instance restart |
| Connection Count | `mongodb_ss_connections` | Current connection-related statistics | Quantity | An unusually high number of connections may indicate connection pool or business peak issues |

Investigation Suggestion: When the number of connections rises, first confirm whether there is a business peak, changes in connection pool configuration, or abnormal client reconnections.

### 5.2 Operations and Document Handling

| Monitoring Dimension | Metric | Metric Meaning | Common Measurement/Unit | Abnormal Manifestation |
| --- | --- | --- | --- | --- |
| Operation Count | `mongodb_ss_opcounters` | Cumulative number of operations such as insert, query, update, delete | times/second | A sudden increase in a certain type of operation indicates a change in business access patterns |
| Document Processing | `mongodb_ss_metrics_document` | Cumulative count of documents inserted, updated, deleted, returned, etc. | times/sec | If the returned quantity is significantly higher than actual demand, the result set may be too large |
| Index Entry Scans | `mongodb_ss_metrics_queryExecutor_scanned` | Number of index entries scanned during queries | times/sec | Excessive scanning may indicate improper indexing |
| Document Scans | `mongodb_ss_metrics_queryExecutor_scannedObjects` | Number of documents scanned during queries | times/sec | A large number of document scans indicates low query efficiency |

Common queries: 

```promql
sum by (type) (rate(mongodb_ss_opcounters[5m]))
```

Investigation Recommendations: A common manifestation is that MongoDB slow queries involve an increase in the number of scans/scan objects. Analysis needs to be combined with slow logs and index hits.

### 5.3 Memory, Network, and Disk

| Monitoring Dimension | Metric | Metric Meaning | Common Unit/Measurement | Abnormal Performance |
| --- | --- | --- | --- | --- |
| Resident Memory | `mongodb_ss_mem_resident` | MongoDB resident memory | MB or bytes | Continuous growth requires checking host memory |
| Virtual Memory | `mongodb_ss_mem_virtual` | MongoDB virtual memory | MB or bytes | Increase alone does not necessarily indicate real pressure |
| Inbound Traffic | `mongodb_ss_network_bytesIn` | MongoDB Cumulative Received Bytes | Bytes/Second | Increase in write or request traffic |
| Outbound Traffic | `mongodb_ss_network_bytesOut` | MongoDB Cumulative Sent Bytes | Bytes/Second | Large queries or export tasks cause an increase in outbound traffic |
| Host Read IO | `node_disk_reads_completed_total` | Read IOPS on the MongoDB residing on its node | Operations/Second | Query scans cause an increase in read IO |
| Host Write IO | `node_disk_writes_completed_total` | Write IOPS on the MongoDB located on its node | Operations/Second | Increase in write or log pressure |

Troubleshooting Recommendations: MongoDB memory and disk performance should be considered together with the memory and disk IO of the nodes. Viewing instance metrics together with host disk read/write makes it easier to determine whether MongoDB itself is slow or if the underlying resources are slow. 

### 5.4 Replication Buffer 

| Monitoring Dimension | Metric | Metric Meaning | Common Measurement/Unit | Abnormal Performance | 
| --- | --- | --- | --- | 
| Replication Buffer Size | `mongodb_ss_metrics_repl_buffer_sizeBytes` | Size of the replication buffer | Bytes | Continuous growth of the buffer indicates replication is not consuming in a timely manner | 

Troubleshooting Recommendations: Abnormal replication buffers are usually related to the processing capability of the secondary node, network, or disk writes, and require analysis in conjunction with replication lag, node network, and disk write metrics. 

## 6. Redis Monitoring 

Redis monitoring is used to observe instance availability, number of connections, command processing, memory levels, keyspace, hit rate, eviction, and network throughput.

### 6.1 Instances and Clients

| Monitoring Dimension | Metric | Metric Meaning | Common Measurement/Unit | Abnormal Performance |
| --- | --- | --- | --- | --- |
| Instance Alive | `up` | Whether Redis can collect exporter | `0/1` | When `0`, the instance or exporter is abnormal |
| Uptime | `redis_uptime_in_seconds` | Redis running time | seconds | A decrease indicates instance restart |
| Client Connections | `redis_connected_clients` | Current number of client connections | quantity | A sudden increase may indicate a connection pool or reconnection storm |

### 6.2 Commands, Memory, and Keyspace

| Monitoring Dimension | Metric | Metric Meaning | Common Unit | Abnormal Behavior |
| --- | --- | --- | --- | --- |
| Processed Commands | `redis_commands_processed_total` | Total number of commands processed by Redis | times/second | Sudden QPS spikes may increase instance CPU load |
| Command Classification | `redis_commands_total` | Total number of commands statistics by type | times/second | Can identify changes in commands such as get, set, del, etc. |
| Memory Usage | `redis_memory_used_bytes` | Current memory usage of Redis | bytes | Approaching maxmemory may trigger eviction |
| Maximum Memory | `redis_memory_max_bytes` | Redis maxmemory configuration | Bytes | Used to calculate memory usage rate |
| Number of Keys | `redis_db_keys` | Number of keys in each database | Count | Abnormal growth of keys may indicate that the cache has no expiration set or there are write anomalies |
| Keys About to Expire | `redis_db_keys_expiring` | Number of keys with expiration set | Count | A very low ratio requires attention to the cache lifecycle |

Common Queries:

```promql
rate(redis_commands_processed_total[5m])
```

```promql
redis_memory_used_bytes / redis_memory_max_bytes * 100
```

### 6.3 Hit Rate, Evictions, and Network

| Monitoring Dimension | Metric | Metric Meaning | Common Unit/Dimension | Abnormal Performance |
| --- | --- | --- | --- | --- |
| Hit Count | `redis_keyspace_hits_total` | Total number of key hits | times/second | Hit rate is calculated together with misses |
| Miss Count | `redis_keyspace_misses_total` | Total number of key misses | times/second | An increase in misses may lead to increased origin server pressure |
| Expired Keys | `redis_expired_keys_total` | Total number of expired keys | times/second | Expiration storms may cause CPU jitter |
| Evicted Keys | `redis_evicted_keys_total` | Total Number of Evicted Keys | times/second | Growth indicates memory pressure or insufficient maxmemory |
| Incoming Traffic | `redis_net_input_bytes_total` | Total Bytes Received by Redis | bytes/second | Increase in write or request traffic |
| Outgoing Traffic | `redis_net_output_bytes_total` | Total Bytes Sent by Redis | bytes/second | High outgoing traffic may be caused by large values or batch reads |

Common Queries:

```promql
rate(redis_keyspace_hits_total[5m]) / (rate(redis_keyspace_hits_total[5m]) + rate(redis_keyspace_misses_total[5m])) * 100
```

```promql
rate(redis_evicted_keys_total[5m])
```

Investigation Recommendations: For Redis, pay attention to memory and eviction risks. A decrease in hit rate will shift pressure to the backend database. An increase in eviction count indicates the need to evaluate cache capacity or eviction policy.

## 7. Kafka Monitoring

Kafka monitoring is used to observe the number of brokers, the status of topics/partitions, production and consumption offsets, consumer group latency, the number of members, and replica synchronization status.

### 7.1 Brokers, Topics, and Partitions

| Monitoring Dimension | Metric | Metric Meaning | Common Unit/Dimension | Abnormal Performance |
| --- | --- | --- | --- | --- |
| Number of Brokers | `kafka_brokers` | Number of currently visible brokers | count | A decrease in number indicates the broker is unavailable or the exporter is inaccessible |
| Topic Partition | `kafka_topic_partitions` | Number of topic partitions | units | Changes in partitions will affect concurrency and consumption capacity |
| Current Partition Offset | `kafka_topic_partition_current_offset` | Latest offset of the partition | Offset / Growth Rate | Should continue to increase during continuous production writes |
| Oldest Partition Offset | `kafka_topic_partition_oldest_offset` | Oldest offset of the partition | Offset | Used to observe the range of retained data |

Common queries: 

```promql
sum by (topic) (rate(kafka_topic_partition_current_offset[5m]))
```

Investigation Recommendations: When the production rate is abnormal, first check the current offset growth of the topic. If the business confirms that there are writes but the offset has not increased, please check for producer-side errors, Broker status, and topic configuration.

### 7.2 Consumer Groups and Lag

| Monitoring Dimension | Metric | Metric Meaning | Common Measurement/Unit | Abnormal Performance |
| --- | --- | --- | --- | --- |
| Consumption Offset | `kafka_consumergroup_current_offset` | The offset currently consumed by the consumer group | Offset / Growth Rate | No growth indicates consumption has stopped or is blocked |
| Partition Lag | `kafka_consumergroup_lag` | The backlog of the consumer group in this partition | Quantity | Increasing lag indicates consumption is falling behind production |
| Group Total Lag | `kafka_consumergroup_lag_sum` | Total backlog of the consumer group | Quantity | Continuous increase in total lag indicates expanding business delay |
| Group Members | `kafka_consumergroup_members` | Number of members in the consumer group | Quantity | A decrease in the number of members may lead to reduced consumption capacity |

Common queries:

```promql
sum by (consumergroup, topic) (kafka_consumergroup_lag)
```

```promql
sum by (consumergroup, topic) (rate(kafka_consumergroup_current_offset[5m]))
```

Troubleshooting recommendations: The Kafka of core business indicators is latency. When latency increases, first check whether the number of consumer members has decreased, then see if the consumption rate has declined, and finally check application processing time, downstream dependencies, and Broker IO.

### 7.3 Replicas and ISR

| Monitoring Dimension | Metric | Metric Meaning | Common Measurement/Unit | Abnormal Performance |
| --- | --- | --- | --- | --- |
| Number of Replicas | `kafka_topic_partition_replicas` | Number of partition replicas | Quantity | Fewer replicas than expected reduce reliability |
| ISR Replicas | `kafka_topic_partition_in_sync_replica` | Number of partition-synchronized replicas | Quantity | A decrease in ISR indicates lagging replicas or Broker issues |
| Preferred Leader | `kafka_topic_partition_leader_is_preferred` | Whether the leader is the preferred replica | `0/1` | Long-term imbalance may cause excessive load on certain Brokers |

Troubleshooting suggestion: A decreasing ISR indicates a higher reliability risk than ordinary latency. Check Broker status, network, disk write latency, and replica synchronization.

## 8. MinIO Object Storage Monitoring

MinIO monitoring is used to observe the availability of the object storage cluster, node and disk status, bucket capacity, S3 requests, errors, traffic, process handles, and repair task activities.

### 8.1 Cluster Nodes and Disks

| Monitoring Dimension | Metric | Metric Meaning | Common Unit/Dimension | Abnormal Performance |
| --- | --- | --- | --- | --- |
| Online Nodes | `minio_cluster_nodes_online_total` | Number of online MinIO nodes | pcs | A decrease in number indicates nodes are unavailable |
| Offline Nodes | `minio_cluster_nodes_offline_total` | Number of offline MinIO nodes | pcs | Greater than 0 requires attention to cluster availability |
| Online Disks | `minio_cluster_disk_online_total` | Number of online disks | pcs | A decrease in disk number affects redundancy and write capability |
| Offline Disks | `minio_cluster_disk_offline_total` | Number of Offline Disks | units | Greater than 0 requires troubleshooting of the disk or mount point |
| Available Capacity | `minio_cluster_capacity_usable_free_bytes` | Available Cluster Capacity | bytes | Continuous decrease indicates a risk of insufficient capacity |

Troubleshooting Recommendations: For object storage, first check the online status of nodes and disks. Do not assess offline disks only by quantity; the risk should be judged in combination with the erasure coding redundancy strategy.

### 8.2 Bucket Capacity and Number of Objects

| Monitoring Dimension | Metric | Metric Meaning | Common Unit/Measurement | Abnormal Performance |
| --- | --- | --- | --- | --- |
| Bucket Capacity | `bucket_usage_size` | Used Bucket Capacity | Bytes | Capacity is growing rapidly and needs to be evaluated for expansion |
| Number of Objects | `bucket_objects_count` | Number of Objects in Bucket | Count | Too many small objects increase metadata and scan pressure |
| Object Size Distribution | `minio_bucket_objects_size_distribution` | Distribution of Object Sizes in Bucket | Bucketed Statistics | Changes in object distribution affect storage and request performance |

Common Queries:

```promql
sum by (bucket) (bucket_usage_size)
```

```promql
sum by (bucket) (bucket_objects_count)
```

Investigation Recommendations: Capacity growth should be analyzed separately by bucket. When the number of objects increases rapidly but capacity growth is not obvious, it is usually due to the increase of small objects. Attention should be paid to lifecycle cleanup and business write patterns.

### 8.3 S3 Requests, Errors, and Traffic

| Monitoring Dimension | Metric | Metric Meaning | Common Measurement/Unit | Abnormal Performance |
| --- | --- | --- | --- | --- |
| Number of S3 Requests | `minio_s3_requests_total` | Cumulative number of S3 API requests | times/second | Sudden increase in requests may indicate business peak or retries |
| Number of S3 Errors | `minio_s3_requests_errors_total` | Cumulative number of S3 API errors | times/second | Rising error rate affects object read and write |
| Incoming Traffic | `minio_s3_traffic_received_bytes` | Total S3 Received Bytes | Bytes/Second | Upload Traffic Increase |
| Outgoing Traffic | `minio_s3_traffic_sent_bytes` | Total S3 Sent Bytes | Bytes/Second | Download or Source Retrieval Traffic Increase |

Common Queries:

```promql
sum by (api) (rate(minio_s3_requests_total[5m]))
```

```promql
sum(rate(minio_s3_requests_errors_total[5m])) / sum(rate(minio_s3_requests_total[5m])) * 100
```

Investigation Recommendation: When the S3 error rate increases, first break it down by type, then check the corresponding Bucket, node disk status, and network traffic. API

### 8.4 Node Processes, File Handles, and IO

| Monitoring Dimension | Metric | Metric Meaning | Common Unit/Dimension | Abnormal Performance |
| --- | --- | --- | --- | --- |
| Node Disk Usage | `minio_node_disk_used_bytes` | Disk usage of MinIO node | Bytes | Imbalanced single-node capacity |
| Open File Handles | `minio_node_file_descriptor_open_total` | Number of file handles opened by MinIO process | Count | Requests may fail when approaching system limit |
| Read system calls | `minio_node_syscall_read_total` | Cumulative number of read system calls | times/sec | Increase in read call exceptions |
| Write system calls | `minio_node_syscall_write_total` | Cumulative number of write system calls | times/sec | Increase in write call exceptions |
| Bytes read by process | `minio_node_io_rchar_bytes` | Cumulative bytes read by process | bytes/sec | Increase in read load |
| Bytes written by process | `minio_node_io_wchar_bytes` | Cumulative bytes written by process | bytes/sec | Increase in write load |
| Number of Goroutines | `minio_node_go_routine_total` | Number of goroutines in the MinIO process | Count | Continuous growth may indicate request backlog or leak |
| Start Time | `minio_node_process_starttime_seconds` | MinIO process start time | Unix timestamp | Changes indicate process restart |

Investigation Suggestions: For MinIO performance issues, consider S3 requests, node disks, process I/O, and goroutines. A high request volume alone is not necessarily abnormal; error rates, I/O latency, and disk offline status are clearer risk indicators.

### 8.5 Fixes and Usage Activity

| Monitoring Dimension | Metric | Metric Meaning | General Standard/Unit | Abnormal Behavior |
| --- | --- | --- | --- | --- |
| Repair Activity | `minio_heal_time_last_activity_nano_seconds` | Last Repair Activity Time | Nanosecond Timestamp | Long or frequent repairs require attention to disk health |
| Usage Activity | `minio_usage_last_activity_nano_seconds` | Last Usage Scan Activity Time | Nanosecond Timestamp | Abnormal usage scans may affect the accuracy of capacity statistics |

Investigation Recommendation: After node or disk anomaly recovery, monitor whether repair activities are proceeding normally to prevent object redundancy from being at risk for a long time.

## 9. Elasticsearch Monitoring

Elasticsearch monitoring is used to observe the health of the search cluster, the number of nodes, shard distribution, index read/write operations, caches, JVM thread pools, disk, and network. ES failures usually cannot be determined by a single metric; more commonly, "shard anomalies, JVM pressure, thread pool rejections, disk watermark" occur simultaneously.

### 9.1 Cluster Health and Nodes

| Monitoring Dimension | Metric | Metric Meaning | Common Measurement/Unit | Abnormal Behavior |
| --- | --- | --- | --- | --- |
| Cluster Health Status | `elasticsearch_cluster_health_status` | ES cluster health status | Status Value | Yellow/Red indicates replica or primary shard issues |
| Number of Nodes | `elasticsearch_cluster_health_number_of_nodes` | Number of Cluster Nodes | Count | A decrease in the number of nodes may indicate that nodes are offline |
| Number of Data Nodes | `elasticsearch_cluster_health_number_of_data_nodes` | Number of Data Nodes in the Cluster | Count | A reduction in data nodes can affect shard capacity and read/write capability |
| Pending Tasks | `elasticsearch_cluster_health_number_of_pending_tasks` | Number of Pending Tasks in the Cluster | Count | Continuous growth indicates that the master node or cluster state updates are slow |
| Active Primary Shard | `elasticsearch_cluster_health_active_primary_shards` | Number of Active Primary Shards | pcs | High risk if reduced, may affect index availability |
| Active Shard | `elasticsearch_cluster_health_active_shards` | Total Number of Active Shards | pcs | No reduction indicates shards have not fully recovered |
| Initializing Shard | `elasticsearch_cluster_health_initializing_shards` | Number of Initializing Shards | pcs | No reduction for a long time indicates slow recovery |
| Relocating Shards | `elasticsearch_cluster_health_relocating_shards` | Number of relocating shards | pcs | Excessive relocation increases network and disk pressure |
| Unassigned Shards | `elasticsearch_cluster_health_unassigned_shards` | Number of unassigned shards | pcs | Greater than 0 indicates shards are not assigned to nodes |
| Delayed Unassigned | `elasticsearch_cluster_health_delayed_unassigned_shards` | Number of delayed unassigned shards | pcs | Waiting for reassignment after node goes offline |

Common Queries: 

```promql
elasticsearch_cluster_health_status
```

```promql
elasticsearch_cluster_health_unassigned_shards
```

Investigation Recommendations: ES should first check the health status and unassigned shards. Red status should prioritize handling primary shards; yellow status is mostly caused by unassigned replicas and should not be neglected for long. 

### 9.2 Disk Capacity and File System

| Monitoring Dimension | Metric | Metric Meaning | Common Measurement / Unit | Abnormal Performance |
| --- | --- | --- | --- | --- |
| Total Data Disk | `elasticsearch_filesystem_data_size_bytes` | Total capacity of the ES data directory | Bytes | Used to calculate disk usage |
| Available Data Disk | `elasticsearch_filesystem_data_available_bytes` | Available capacity of the ES data directory | Bytes | Insufficient available space may trigger shard migration or write restrictions |

Common queries:

```promql
(1 - elasticsearch_filesystem_data_available_bytes / elasticsearch_filesystem_data_size_bytes) * 100
```

Investigation Recommendations: ES is very sensitive to disk usage. When disk usage is too high, shard migration, read-only indices, or write failures may occur. It is necessary to monitor index growth, retention policies, and node disk distribution.

### 9.3 Documents, Indices, and Deletions

| Monitoring Dimension | Metric | Metric Meaning | Common Unit | Abnormal Behavior |
| --- | --- | --- | --- | --- |
| Number of Documents | `elasticsearch_indices_docs` | Current number of documents | Count | Rapid continuous growth of documents requires capacity assessment |
| Deleted Documents | `elasticsearch_indices_docs_deleted` | Number of deleted documents | Count | High deletion rate can lead to merge pressure |
| Index Write Count | `elasticsearch_indices_indexing_index_total` | Cumulative Number of Index Operations | times/sec | A sudden increase in writes can increase CPU, disk, and refresh pressure |
| Index Write Time | `elasticsearch_indices_indexing_index_time_seconds_total` | Cumulative Time of Index Operations | sec/sec | An increase in write time can slow down the write path |
| Delete Operation Count | `elasticsearch_indices_indexing_delete_total` | Cumulative Number of Delete Operations | times/sec | A sudden increase in delete operations may lead to segment merge pressure |
| Delete Operation Duration | `elasticsearch_indices_indexing_delete_time_seconds_total` | Cumulative duration of delete operations | sec/sec | Delete duration increase |

Common queries:

```promql
sum by (cluster) (rate(elasticsearch_indices_indexing_index_total[5m]))
```

```promql
rate(elasticsearch_indices_indexing_index_time_seconds_total[5m]) / rate(elasticsearch_indices_indexing_index_total[5m])
```

Troubleshooting Recommendations: When write speed slows down, do not only look at write QPS. You should also consider refresh, merge, transaction logs, thread pool rejections, and disk IO.

### 9.4 Query and Get Requests

| Monitoring Dimension | Metric | Metric Meaning | Common Measurement/Unit | Abnormal Behavior |
| --- | --- | --- | --- | --- |
| Number of Query Requests | `elasticsearch_indices_search_query_total` | Cumulative number of search queries | times/second | Sudden increase in queries |
| Query Latency | `elasticsearch_indices_search_query_time_seconds` | Cumulative time of search queries | seconds/second | Increase in average query latency |
| Fetch Requests | `elasticsearch_indices_search_fetch_total` | Cumulative count during the search fetch phase | times/sec | Large result sets will increase the number of fetches |
| Fetch Latency | `elasticsearch_indices_search_fetch_time_seconds` | Cumulative time of search fetches | sec/sec | Slow fetches are usually related to large result sets, disk, or network |
| Number of Fetch Requests | `elasticsearch_indices_get_exists_total`, `elasticsearch_indices_get_missing_total` | Cumulative count of fetch hits and misses | times/sec | An increase in misses may indicate that the business accessed non-existent documents |
| Get Duration | `elasticsearch_indices_get_time_seconds`, `elasticsearch_indices_get_exists_time_seconds`, `elasticsearch_indices_get_missing_time_seconds` | Get Cumulative Request Time | seconds/second | Slow retrieval indicates increased pressure on the read path |

Common queries: 

```promql
rate(elasticsearch_indices_search_query_time_seconds[5m]) / rate(elasticsearch_indices_search_query_total[5m])
```

```promql
rate(elasticsearch_indices_search_fetch_time_seconds[5m]) / rate(elasticsearch_indices_search_fetch_total[5m])
```

Troubleshooting Recommendations: Slow queries should distinguish between searching and fetching. Slow searches are more related to query conditions, index structure, and shard pressure; slow fetching is more common when there are many returned fields, large result sets, or slow disk reads.

### 9.5 Segments, Merges, Refresh, and Transaction Logs

| Monitoring Dimension | Metric | Metric Meaning | Common Unit/Scale | Abnormal Symptoms |
| --- | --- | --- | --- | --- |
| Number of Segments | `elasticsearch_indices_segments_count` | Current number of segments | Quantity | Too many segments can affect queries and memory |
| Segment Memory | `elasticsearch_indices_segments_memory_bytes` | Memory occupied by segments | Bytes | Continuous increase may squeeze the JVM |
| Number of Merges | `elasticsearch_indices_merges_total` | Cumulative Number of Merge Operations | times/sec | Frequent merges indicate high write or delete pressure |
| Number of Documents Merged | `elasticsearch_indices_merges_docs_total` | Cumulative Number of Documents Processed in Merges | count/sec | Continuously increasing merge workload |
| Amount of Data Merged | `elasticsearch_indices_merges_total_size_bytes_total` | Cumulative Data Processed in Merges | bytes/sec | Large merges may cause disk IO saturation |
| Merge Duration | `elasticsearch_indices_merges_total_time_seconds_total` | Cumulative Time Spent on Merges | sec/sec | Slow merges can affect write and query performance |
| Number of Refreshes | `elasticsearch_indices_refresh_total` | Cumulative Number of Refreshes | times/sec | Frequent refreshes increase overhead |
| Refresh Duration | `elasticsearch_indices_refresh_time_seconds_total` | Cumulative Refresh Time | sec/sec | Slow refresh affects near real-time visibility |
| Refresh Operation Count | `elasticsearch_indices_flush_total` | Cumulative Refresh Count | Count/Second | Frequent refreshing may be related to transaction logs and write pressure |
| Refresh Duration | `elasticsearch_indices_flush_time_seconds` | Cumulative Refresh Time | Seconds/Second | Slow refreshing may affect system stability |
| Transaction Log Operations | `elasticsearch_indices_translog_operations` | Current Transaction Log Operation Count | Quantity | Continuous accumulation requires attention to refresh |
| Transaction Log Size | `elasticsearch_indices_translog_size_in_bytes` | Current Transaction Log Size | Bytes | Excessive size may affect recovery time |
| Storage Throttling | `elasticsearch_indices_store_throttle_time_seconds_total` | Cumulative time of index storage throttling | sec/sec | Throttling increases, writes are affected by disk |

Investigation recommendation: When write pressure is high, combine merges, refreshes, flushes, and transaction log changes. Increased merge time and storage throttling usually indicate that the disk has begun to impact ES.

### 9.6 Cache and Circuit Breakers

| Monitoring Dimension | Metric | Metric Meaning | Common Unit/Measurement | Abnormal Behavior |
| --- | --- | --- | --- | --- |
| Query Cache Memory | `elasticsearch_indices_query_cache_memory_size_bytes` | Memory used by the query cache | Bytes | Excessive usage may compress the JVM |
| Query Cache Evictions | `elasticsearch_indices_query_cache_evictions` | Cumulative number of query cache evictions | times/sec | Frequent evictions indicate cache instability |
| Field Data Memory | `elasticsearch_indices_fielddata_memory_size_bytes` | Memory used by field data | bytes | High field data usage may easily trigger memory pressure |
| Field Data Evictions | `elasticsearch_indices_fielddata_evictions` | Cumulative number of field data evictions | times/sec | High query or aggregation pressure |
| Filter Cache Evictions | `elasticsearch_indices_filter_cache_evictions` | Cumulative number of filter cache evictions | times/second | Frequent filter cache invalidations |
| Circuit Breaker Estimated Size | `elasticsearch_breakers_estimated_size_bytes` | Estimated memory of the circuit breaker | bytes | Queries may be rejected when close to the limit |
| Circuit Breaker Limit | `elasticsearch_breakers_limit_size_bytes` | Circuit breaker limit | bytes | Used to calculate circuit breaker utilization |
| Circuit Breaker Tripped | `elasticsearch_breakers_tripped` | Number of times the circuit breaker was triggered | times/increment | Growth description: requests blocked due to memory risk |

Common queries: 

```promql
elasticsearch_breakers_estimated_size_bytes / elasticsearch_breakers_limit_size_bytes * 100
```

```promql
increase(elasticsearch_breakers_tripped[10m])
```

Investigation recommendations: Aggregation queries, sorting, and scripted queries tend to increase fielddata and breaker usage. When a breaker is triggered, it is usually necessary to limit query size, optimize index mapping, or adjust the query method.

### 9.7 JVM, CPU, and Load

| Monitoring Dimension | Metric | Metric Meaning | Common Measurement/Unit | Abnormal Manifestation |
| --- | --- | --- | --- | --- |
| JVM Used Memory | `elasticsearch_jvm_memory_used_bytes` | Currently used JVM memory | Bytes | Persistently close to the limit, increased GC pressure |
| JVM Max Memory | `elasticsearch_jvm_memory_max_bytes` | Maximum available JVM memory | Bytes | Used to calculate JVM usage rate |
| JVM Committed Memory | `elasticsearch_jvm_memory_committed_bytes` | JVM Committed Memory | Bytes | Observe JVM memory allocation |
| JVM Memory Pool Peak | `elasticsearch_jvm_memory_pool_peak_used_bytes` | Peak usage of each memory pool | Bytes | High old generation peak requires attention |
| GC Count | `elasticsearch_jvm_gc_collection_seconds_count` | Number of GC occurrences | times/sec | Frequent GC may cause latency fluctuations |
| GC Time | `elasticsearch_jvm_gc_collection_seconds_sum` | Total GC Time | sec/sec | Long GC time may affect queries and writes |
| Process CPU | `elasticsearch_process_cpu_percent` | ES Process CPU Usage | % | Prolonged high CPU load may indicate heavy query or write load |
| System Load | `elasticsearch_os_load1`, `elasticsearch_os_load5`, `elasticsearch_os_load15` | Node 1/5/15 Minute Load | Load Value | Load higher than CPU cores indicates noticeable task queuing |
| Number of open files | `elasticsearch_process_open_files_count` | Number of files opened by the ES process | Quantity | Approaching the system limit may affect index file access |

Common queries: 

```promql
elasticsearch_jvm_memory_used_bytes / elasticsearch_jvm_memory_max_bytes * 100
```

```promql
rate(elasticsearch_jvm_gc_collection_seconds_sum[5m])
```

Investigation Recommendation: A larger ES JVM memory is not always better. JVM usage, GC time, fielddata, query cache, and breakers should be monitored together to determine whether memory pressure is caused by queries or if the heap size does not match the data scale.

### 9.8 Thread Pool and Network

| Monitoring Dimension | Metric | Metric Meaning | Common Measurement/Unit | Abnormal Behavior |
| --- | --- | --- | --- | --- |
| Active Threads | `elasticsearch_thread_pool_active_count` | Number of active threads in the thread pool | Count | Prolonged high active thread count indicates high processing pressure |
| Completed Tasks | `elasticsearch_thread_pool_completed_count` | Cumulative number of tasks completed by the thread pool | times/sec | Used to observe processing throughput |
| Rejected Tasks | `elasticsearch_thread_pool_rejected_count` | Cumulative number of tasks rejected by the thread pool | times/sec | An increase indicates the thread pool or queue is full |
| Inbound Traffic | `elasticsearch_transport_rx_size_bytes_total` | Cumulative number of bytes received | bytes/sec | Increased inter-node communication or request traffic |
| Outbound Traffic | `elasticsearch_transport_tx_size_bytes_total` | Cumulative bytes sent | Bytes/sec | Traffic increase due to fragment relocation, query, or replication |

Common queries: 

```promql
sum by (type) (rate(elasticsearch_thread_pool_rejected_count[5m]))
```

```promql
rate(elasticsearch_transport_rx_size_bytes_total[5m]) + rate(elasticsearch_transport_tx_size_bytes_total[5m])
```

Investigation Recommendations: Thread pool rejections are a very direct signal of business risk. For write operation rejections, check the bulk/index thread pool; for search rejections, check the search thread pool, and then determine the bottleneck in combination with CPU, JVM, and disk IO.

## 10. Application Service Monitoring

Application monitoring covers common server-side requests, client dependency calls, runtime resources, collaborative editing business chains, and RS service tasks. The focus of application metrics is not on single resource thresholds, but on request volume, errors, latency, and dependency health.

### 10.1 Common Server-Side Metrics

| Monitoring Dimension | Metric | Metric Meaning | Common Range/Unit | Abnormal Performance |
| --- | --- | --- | --- | --- |
| Service Uptime | `up` | Whether the application exporter or metric endpoint can be collected | `0/1` | `0` means the metric is inaccessible or the service is abnormal |
| Build Information | `ego_build_info` | Application build version, branch, and other information | Tag information | Used to verify the release version |
| Startup Count | `ego_server_started_total` | Cumulative number of server startups | Count/Increment | Increase indicates process restart or release |
| Server Requests | `ego_server_handle_total` | Cumulative number of server requests | Count/Second | Sudden increase or decrease in request volume needs to be judged in the business context |
| Server Duration | `ego_server_handle_seconds_count`, `ego_server_handle_seconds_bucket` | Server request time statistics | P50/P95/P99 | Increased latency affects user experience |

Common queries:

```promql
sum by (service, method) (rate(ego_server_handle_total[5m]))
```

```promql
histogram_quantile(0.95, sum(rate(ego_server_handle_seconds_bucket[5m])) by (le, service, method))
```

Investigation Suggestions: For server-side exceptions, first check whether the request volume has changed, then look at errors and latency. If latency increases but resources are not high, continue to check downstream dependency calls and queues.

### 10.2 Client-Side Dependency Calls

| Monitoring Dimension | Metric | Metric Meaning | Common Granularity/Unit | Abnormal Behavior |
| --- | --- | --- | --- | --- |
| Client Call Volume | `ego_client_handle_total` | Number of times the application calls downstream as a client | times/second | Sudden increase in downstream calls may increase dependency pressure |
| Client Latency | `ego_client_handle_seconds_count`, `ego_client_handle_seconds_bucket` | Downstream Call Latency Statistics | P50/P95/P99 | Slow downstream may cause the current service to slow down |
| Client Status | `ego_client_stats_gauge` | Client connection pool or status metrics | Current Value | Connection pool exhaustion, abnormal idle connections, etc. |
| Kafka Production Latency | `kafka_produce_duration_seconds_bucket` | Time used by the application to produce Kafka messages | P50/P95/P99 | Increased production latency may be due to broker or network issues |

Common queries:

```promql
histogram_quantile(0.95, sum(rate(ego_client_handle_seconds_bucket[5m])) by (le, service, target, method))
```

Investigation Recommendations: When a business interface responds slowly, compare the server-side latency with the client-side dependency latency. If the client-side latency accounts for a high proportion, priority should be given to checking the corresponding downstream services, middleware, or network.

### 10.3 Runtime and Processes

| Monitoring Dimension | Metric | Metric Meaning | Common Standard/Unit | Abnormal Manifestation |
| --- | --- | --- | --- | --- |
| Go goroutine | `go_goroutines` | Number of goroutines in the Go process | Count | Continuous growth may indicate blocking or memory leak |
| Go GC Duration | `go_gc_duration_seconds` | Go GC duration | Seconds/Percentile | Increased GC time may affect latency |
| Go Heap Memory | `go_memstats_alloc_bytes`, `go_memstats_heap_inuse_bytes` | Go Heap Allocation and Usage | Bytes | Continuous growth requires checking for memory leaks |
| Go System Memory | `go_memstats_sys_bytes` | Memory requested by Go runtime from the system | Bytes | Observe together with RSS |
| Go Stack Memory | `go_memstats_stack_inuse_bytes` | Goroutine stack usage | Bytes | Increases with goroutine growth |
| Node.js GC Count | `nodejs_gc_duration_seconds_count` | Node.js GC Count | times/sec | Frequent GC may indicate heap pressure |
| Node.js GC Duration | `nodejs_gc_duration_seconds_sum` | Node.js GC Total Duration | sec/sec | Increased GC duration may affect response |
| Node.js Heap Space | `nodejs_heap_space_size_used_bytes` | Usage of each heap space Node.js Heap Space | bytes | If approaching limit or continuously growing, attention is needed |
| Process CPU | `process_cpu_seconds_total` | Process CPU Time | cores/seconds | High CPU Usage |
| Process RSS | `process_resident_memory_bytes` | Process Physical Memory | bytes | Sustained RSS Growth |
| Process Handles | `process_open_fds` | Number of Open File Descriptors in the Process | count | Handle Leak, Connection Leak |

Investigation Recommendation: Go runtime metrics and Node.js are mainly used to explain application latency and resource increases. When application P95 rises, if GC duration also increases simultaneously, memory allocation and object lifecycle should be prioritized for inspection.

### 10.4 Collaborative Editing Service

| Monitoring Dimension | Metric | Metric Meaning | Common Unit | Abnormal Indication |
| --- | --- | --- | --- | --- |
| Kafka consumer lag | `kafka_consumergroup_lag` | Backlog of relevant consumer groups in collaborative editing | Count | Increased lag may lead to delayed event processing |
| Processing duration | `process_flow_duration_seconds_bucket` | Duration of the collaborative editing process | P50/P95/P99 | Slower document collaboration |
| Number of processes | `process_total` | Total number of processed tasks | Times/second | Abnormal changes in processing volume |
| File content size | `file_content_size_bytes_bucket` | Distribution of file content sizes | Bucket statistics | Increased proportion of large files may affect processing time |
| Change Set Duration | `handle_changeset_cost_seconds_bucket` | Time required to process the change set | P50/P95/P99 | Increase in edit synchronization delay |
| Modoc Calculation Count | `modocComputeCount` | Number of Modoc calculations | times/second | Abnormal increase in calculation volume |
| Serverless Invocation Count | `serverless_invocations` | Number of serverless invocations | times/second | Failures or spikes in invocations may impact links |

Common Queries:

```promql
histogram_quantile(0.95, sum(rate(handle_changeset_cost_seconds_bucket[5m])) by (le))
```

Investigation Recommendations: For collaborative editing links, Kafka should simultaneously monitor latency, processing time, changeset duration, and file size. When the proportion of large files increases, an increase in duration may be a normal capacity stress rather than a single point of failure.

### 10.5 RS Service

| Monitoring Dimension | Metric | Metric Meaning | General Range/Unit | Abnormal Performance |
| --- | --- | --- | --- | --- |
| Number of HTTP Requests | `http_requests_total` | Cumulative number of HTTP requests | times/second | Sudden increase or decrease in requests |
| HTTP Duration | `http_requests_duration_seconds_bucket`, `http_requests_duration_seconds_sum`, `http_requests_duration_seconds_count` | HTTP Request Duration | P50/P95/P99 | Increased interface latency |
| gRPC Request Count | `grpc_requests_total` | Total gRPC Request Count | times/second | gRPC Call Exception |
| gRPC Duration | `grpc_requests_duration_seconds` | gRPC Request Duration | P50/P95/P99 | Downstream or internal processing slowdown |
| Export Task Duration | `export_task_duration_milliseconds_count` | Number and duration of export task processing | ms/task | Export tasks slow down or backlog |
| Import Task Duration | `import_task_duration_milliseconds_count` | Number and duration of import task processing | ms/task | Slowed down or backlogged import tasks |
| Ongoing Export Tasks | `export_task_in_progress` | Currently executing export tasks | Number | Indicates tasks are stuck if it does not decrease for a long time |
| Ongoing Import Tasks | `import_task_in_progress` | Currently executing import tasks | Number | Indicates tasks are stuck if it does not decrease for a long time |
| Tokio Metrics | `tokio_metrics` | Rust Tokio runtime metrics | Current value / Rate | Runtime queue or task scheduling anomalies |
| jemalloc Metrics | `jemalloc` | Memory allocator metrics | Bytes / Count | Memory fragmentation or allocation anomalies |
| TCP Metrics | `tcp` | RS service TCP connection related metrics | Count / Rate | Connection pressure or network anomalies |

Investigation Recommendation: The RS service should check both online requests and long-running tasks, such as import/export. Task duration not decreasing is usually a more reliable indicator of "stuck tasks" than average duration.

## 11. Metrics Reading and Investigation Recommendations

### 11.1 General Investigation Order

| Step | Observed Items | Purpose | 
| --- | --- | --- |
| 1 | `up`, startup time, Pod readiness | Check whether the service is still running and whether it has recently restarted |
| 2 | Request volume, error rate, P95/P99 latency | Determine whether it actually affects the business |
| 3 | CPU, memory, disk, network | Determine whether there is a resource bottleneck |
| 4 | Downstream dependency latency, Kafka lag, slow database queries | Determine whether the slowdown is caused by dependencies |
| 5 | Release version, configuration, traffic changes | Determine whether it is related to changes |

When troubleshooting, don't rush to look at all the charts first. First confirm "whether there is a business impact," and then identify "where the impact is coming from." For example, if an interface is slow, first check the application's P95, then check the client dependency latency; if the dependencies are normal, then look back at the service's CPU, GC, memory, and container throttling.

### 11.2 Common Abnormal Combinations

| Symptom | Common Indicator Performance | Priority Investigation Directions |
| --- | --- | --- |
| Slow interface | Application P95/P99 rising, CPU not high | Downstream dependencies, slow database queries, Kafka latency |
| CPU fully utilized | `container_cpu_usage_seconds_total` high, throttling high | CPU limits, hotspot interfaces, batch tasks |
| Memory OOM | Working set near limit, increasing restart count | Memory leaks, limit too low, large object processing |
| Slow disk | iowait, busy IO, read/write latency rising | Database, Kafka, MinIO, log writing |
| Network issues | Traffic surge with packet loss/errors | Node NIC, CNI, links, connection count |
| Kafka Delay | `kafka_consumergroup_lag` continuously increasing | Consumer instances, consumption time, downstream dependencies |
| Redis Backpressure | Hit rate decreasing, misses increasing | Key expiration strategy, cache penetration, capacity |
| MySQL Slowness | Slow queries, scans, lock waits increasing | SQL, indexes, locks, disk IO |
| MinIO Risk | Offline disks, error rates, capacity levels rising | Disks, nodes, Bucket growth, repair status |
| Elasticsearch Slow Queries | Search query/fetch time increasing, thread pool rejections rising | Query conditions, index structure, JVM, disk IO |
| Elasticsearch Slow Writes | Indexing time, merge time, storage throttling increasing | Write peaks, refresh, merge, disk level |
