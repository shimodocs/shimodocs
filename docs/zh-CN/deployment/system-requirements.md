# 系统要求

[← ShimoDocs Suite 部署文档](README.md)

## 1. 根据场景准备资源

| 使用场景 | 推荐部署 | 资源准备 |
| --- | --- | --- |
| 轻量小型团队， PoC演示、功能验证 | 单服务器部署 | 1 台服务器 |
| 正式上线、长期运行、需要高可用或未来扩展 | 高可用集群 | 3 台或以上服务器 |

- 单服务器部署适用于快速验证和小规模使用。
- 集群部署适用于正式上线、长期运行及未来扩展。

## 2. 操作系统要求

| 操作系统 | 支持版本 | 支持架构 |
| --- | --- | --- |
| Ubuntu | 22.04, 24.04 | x86 |

在每台服务器上执行：

```bash
cat /etc/os-release
uname -m
```

确认结果： 

- 操作系统为 Ubuntu 22.04 或 Ubuntu 24.04。 
- CPU 架构为 x86。 
- 安装账号为 `root`，或具有等效系统管理权限。 

注意：不再支持 CentOS 系统的原因 
- CentOS Linux 7 和 8 已到达生命周期终点，CentOS 官方不再提供 CentOS 9 及后续版本，也不再接收新的安全更新、漏洞修复或问题补丁。 
- 基本系统组件无法长期接收安全补丁，可能会留下暴露但无法修复的漏洞，这不符合生产环境的安全要求。 
- CentOS 7/8 中的内核、glibc、OpenSSL 及其他基本组件相对较旧，无法满足新运行时和依赖库的要求。 Kubernetes  
- CentOS Stream 的版本定位和更新机制与传统的 CentOS Linux 不同，未经特殊兼容性验证的 CentOS Stream 环境也不在官方支持范围内。 


## 3. 服务器配置要求 

### 3.1 单节点部署 

- 适合轻量型小团队，人数少于200人。 
- PoC，演示和功能验证场景可以根据单节点资源进行准备。 

| 项目 | 要求 | 
| --- | --- | 
| 服务器数量 | 1 | 
| CPU | 16 核或更多 |
| 内存 | 32 GB 或更多 |
| 系统盘 | 根目录 `/` 分区 100 GB 或更多 |
| 数据盘 | 独立安装 `/data`，可用空间300 GB或以上，支持扩展 |

### 3.2 集群部署

对于需要正式上线、长期运行、高可用或未来扩展的场景，请根据集群要求准备资源。

| 项目 | 要求 |
| --- | --- |
| 服务器数量 | 3 个或更多 |
| 推荐角色 | `3 master   N worker` |
| CPU 每个节点 | 16 核或更多 |
| 每个节点内存 | 32 GB 或更多 |
| 每节点系统盘 | 根目录 `/` 分区 100 GB 或更多 |
| 每节点数据盘 | 独立安装 `/data`，可用空间300 GB或以上，支持扩展 |

分区说明：

- 根分区至少保留100 GB。 `/` 分区。
- 建议放置在 `/root`, `/var`, `/tmp` 根分区下以便统一管理。
- 数据目录建议使用独立数据盘，挂载于 `/data`.

## 4. 服务器自检命令

在每台服务器上执行： 

```bash
# ============================================
# 1. View CPU architecture and core information
#    - Architecture type (x86_64/aarch64)
# ============================================
lscpu

# ============================================
# 2. Display memory and swap usage in GiB
# ============================================
free -g

# ============================================
# 3. File System Disk Space Usage
# ============================================
df -h

# ============================================
# 4. Find the executable file path
# ============================================
which iptables gzip tar

# ============================================
# 5. Display system time, time zone, and NTP synchronization status
#    Distributed clusters must have strict time synchronization, otherwise it will affect authentication and log sequencing.
# ============================================
timedatectl status
```

对照清单：

| 检查项 | 通过条件 |
| --- | --- |
| CPU | 16 核或以上 |
| 内存 | 32 GB 或以上 |
| 系统盘 | 根目录 `/` 分区可用空间100 GB或以上 |
| 数据盘 | `/data` 已挂载，可用空间300 GB或以上 |
| 基本命令 | `iptables`, `gzip`, `tar` 可找到 |
| 时间同步 | 系统时间同步正常 |

## 5. 浏览器要求

| 浏览器 | 版本要求 |
| --- | --- |
| Chrome | 86或以上 |
| Safari | 11或以上 |
| Firefox | 102或以上 |
| Edge | 84或以上 |

建议优先使用较新的 Chrome 或 Edge 来访问安装程序并 ShimoDocs Suite.

## 6. 中间件需求

| 组件 | 版本要求 |
| --- | --- |
| Elasticsearch | 8.18.x |
| MongoDB | 4.4.x |
| Redis | 6.2.x |
| MySQL | 8.0 |
| Dameng | V8 03134284194-20240920-243574-20108 |
| Kafka | 2.7 到 3.5 |
| 对象存储 | 兼容 S3 协议<br>并确保其 Endpoint 地址可以被客户端浏览器从公网直接访问（因为 ShimoDocs 应用的静态资源加载和文档读写操作必须通过浏览器与对象存储的直接连接完成）。 |

对象存储可以选择华为云 OBS、阿里云 OSS、腾讯云 COS, AWS S3。对于本地部署，可以考虑使用 MinIO 。

如果使用安装程序自带的中间件，请在安装页面继续使用默认选项。 
如果使用现有的外部中间件，请在安装前准备地址、端口、账号 PASSWORD, DATABASE_NAME 或存储桶名称。

## 7. 端口要求

在部署之前，确保服务器、安全组、防火墙、负载均衡器和网络策略已经允许以下端口。

| 端口 | 目标 | 目的 |
| --- | --- | --- |
| `18080/TCP` | 安装程序 Web UI | 访问安装页面 |
| `80/TCP` 或 `443/TCP` | ShimoDocs服务_DOMAIN | 用户访问入口 |
| `22/TCP` | 所有部署节点 | SSH 登录和安装任务分配 |
| `3306/TCP` | MySQL | 数据库连接 |
| `6379/TCP` | Redis | 缓存连接 |
| `27017/TCP` | MongoDB | 文档数据库连接 |
| `9092/TCP` | Kafka | 消息队列连接 |
| `9200/TCP` | Elasticsearch | 搜索服务连接 |
| 按服务端口 | S3 / OBS / OSS / COS / MinIO | 对象存储连接 |

## 8. 磁盘IO要求

建议数据盘使用SSD。磁盘性能应满足以下标准：

| 项目 | 要求 |
| --- | --- |
| 混合读/写 IOPS | 高于5000 |
| 顺序读/写吞吐量 | 高于150 MB/s |
| 平均延迟 | 约5毫秒或更低 |

安装完成后 `fio`, 可以在 `/data`.

### 8.1 混合读/写测试

```bash
fio --name=randrw-test \
  --filename=/data/testfile \
  --size=20G \
  --rw=randrw \
  --rwmixread=70 \
  --bs=4k \
  --ioengine=libaio \
  --direct=1 \
  --iodepth=32 \
  --numjobs=4 \
  --runtime=300 \
  --time_based \
  --group_reporting
```

注意结果中的 IOPS ; 混合读/写 IOPS 应达到5000以上才能继续。 

### 8.2 顺序读取测试

```bash
fio --name=seqread-test \
  --filename=/data/testfile \
  --size=20G \
  --rw=read \
  --bs=1M \
  --ioengine=libaio \
  --direct=1 \
  --iodepth=32 \
  --numjobs=1 \
  --runtime=300 \
  --time_based \
  --group_reporting
```

### 8.3 顺序写入测试

```bash
fio --name=seqwrite-test \
  --filename=/data/testfile \
  --size=20G \
  --rw=write \
  --bs=1M \
  --ioengine=libaio \
  --direct=1 \
  --iodepth=32 \
  --numjobs=1 \
  --runtime=300 \
  --time_based \
  --group_reporting
```

顺序读和顺序写吞吐量达到150 MB/s以上可以继续。 

测试完成后可以删除测试文件： 

```bash
rm -f /data/testfile
```

## 9. 公网带宽要求

根据用户数量估算公共网络访问场景的带宽：

```text
PUBLIC_NETWORK_BANDWIDTH = NUMBER_OF_USERS x 0.25 Mbps
```

示例：

| 用户数量 | 推荐公共网络带宽 |
| --- | --- |
| 100 用户 | 超过 25 Mbps |
| 200 用户 | 超过 50 Mbps |
| 500 用户 | 超过 125 Mbps |

对于内网访问场景，也建议使用相同的标准评估出站、入站和负载均衡带宽。

## 10. 安装程序和运维平台浏览器版本建议

建议使用 Google Chrome 111 版本或以上版本，最好使用最新的稳定版本。

## 11. 部署前检查清单

在开始安装前，请确认每一项：

- 操作系统版本符合要求。
- CPU内存、系统盘和数据盘符合要求。
- `/data` 已挂载到单独的数据盘。
- `iptables`, `gzip`和 `tar` 已安装。
- 系统时间同步正常。
- 已确定在线或离线安装方式。
- 安装程序端口 `18080` 可访问。
- 业务访问端口 `80` 或 `443` 已打开。
- 如果使用外部中间件，连接信息已完全准备好。
- 对象存储兼容该 S3 协议，存储桶和账户权限已就绪。 
- 数据盘 IO 测试符合要求。 
- 公共或内部网络带宽满足预期的用户数量。
