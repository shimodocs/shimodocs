# System Requirements

[← ShimoDocs Suite Deployment Documentation](README.md)

## 1. Prepare Resources Based on Scenarios

| Use Scenario | Recommended Deployment | Resource Preparation |
| --- | --- | --- |
| Lightweight small team, PoC demonstration, feature validation | Single-server deployment | 1 server |
| Official launch, long-term operation, requiring high availability or future expansion | High-availability cluster | 3 or more servers |

- Single-server deployment is suitable for quick validation and small-scale use.
- Cluster deployment is suitable for official launch, long-term operation, and future expansion.

## 2. Operating System Requirements

| Operating System | Supported Versions | Supported Architecture |
| --- | --- | --- |
| Ubuntu | 22.04, 24.04 | x86 |

Execute on each server:

```bash
cat /etc/os-release
uname -m
```

Confirmation Result:

- The operating system is Ubuntu 22.04 or Ubuntu 24.04.
- The CPU architecture is x86.
- The installation account is `root`, or has equivalent system administration privileges.

Note: Reasons why CentOS systems are no longer supported
- CentOS Linux 7 and 8 have reached the end of their life cycle. CentOS officially no longer provides CentOS 9 and later versions, nor does it accept new security updates, vulnerability fixes, or patches.
- The basic system components cannot receive security patches long-term, potentially leaving vulnerabilities exposed and unfixable, which does not meet the security requirements of a production environment.
- The kernel, glibc, OpenSSL, and other core components in CentOS 7/8 are relatively old and cannot meet the requirements of new runtimes and dependent libraries. Kubernetes
- The version positioning and update mechanism of CentOS Stream are different from traditional CentOS Linux. CentOS Stream environments that have not undergone special compatibility testing are also not officially supported. 

## 3. Server Configuration Requirements

### 3.1 Single Node Deployment

- Suitable for small teams, fewer than 200 people.
- PoC, demonstration, and feature verification scenarios can be prepared based on single-node resources.

| Item | Requirement |
| --- | --- |
| Number of servers | 1 |
| CPU | 16 cores or more |
| Memory | 32 GB or more |
| System Disk | Root directory `/` partition 100 GB or more |
| Data Disk | Separate installation `/data`, available space 300 GB or more, expandable |

### 3.2 Cluster Deployment

For scenarios requiring formal deployment, long-term operation, high availability, or future expansion, please prepare resources according to the cluster requirements.

| Item | Requirement |
| --- | --- |
| Number of servers | 3 or more |
| Recommended role | `3 master   N worker` |
| CPU per node | 16 cores or more |
| Memory per node | 32 GB or more |
| System disk per node | Root directory `/` partition 100 GB or more |
| Data disk per node | Separate installation `/data`, available space 300 GB or more, supports expansion |

Partition description:

- The root partition should reserve at least 100 GB. `/` partition.
- It is recommended to place it under the root partitions of `/root`, `/var`, `/tmp` for unified management.
- It is recommended to use a separate data disk for the data directory, mounted at `/data`.

## 4. Server Self-Check Command

Execute on each server:

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

Checklist:

| Check Item | Passing Criteria |
| --- | --- |
| CPU | 16 cores or more |
| Memory | 32 GB or more |
| System Disk | Root directory `/` partition available space 100 GB or more |
| Data Disk | `/data` mounted, available space 300 GB or more |
| Basic Commands | `iptables`, `gzip`, `tar` can be found |
| Time Synchronization | System time synchronized correctly |

## 5. Browser Requirements

| Browser | Version Requirement |
| --- | --- |
| Chrome | 86 or above |
| Safari | 11 or above |
| Firefox | 102 or above |
| Edge | 84 or above |

It is recommended to use the latest Chrome or Edge to access the installer and ShimoDocs Suite.

## 6. Middleware Requirements

| Component | Version Requirement |
| --- | --- |
| Elasticsearch | 8.18.x |
| MongoDB | 4.4.x |
| Redis | 6.2.x |
| MySQL | 8.0 |
| Dameng | V8 03134284194-20240920-243574-20108 |
| Kafka | 2.7 to 3.5 |
| Object Storage | Compatible with the S3 protocol<br> and ensure that its Endpoint address can be directly accessed by client browsers from the public network (because ShimoDocs application's static resource loading and document read/write operations must be completed through a direct connection between the browser and the object storage). |

Object storage can be Huawei Cloud OBS, Alibaba Cloud OSS, Tencent Cloud COS, or AWS S3. For local deployment, MinIO can be considered.

If using the middleware provided by the installer, continue using the default options on the installation page.
If using existing external middleware, prepare the address, port, account PASSWORD, DATABASE_NAME, or bucket name before installation.

## 7. Port Requirements

Before deployment, ensure that the server, security group, firewall, load balancer, and network policies have allowed the following ports.

| Port | Target | Purpose |
| --- | --- | --- |
| `18080/TCP` | Installer Web UI | Access installation page |
| `80/TCP` or `443/TCP` | ShimoDocs Service_DOMAIN | User access entry |
| `22/TCP` | All deployment nodes | SSH login and installation task assignment |
| `3306/TCP` | MySQL | Database connection |
| `6379/TCP` | Redis | Cache connection |
| `27017/TCP` | MongoDB | Document database connection |
| `9092/TCP` | Kafka | Message queue connection |
| `9200/TCP` | Elasticsearch | Search service connection |
| By Service Port | S3 / OBS / OSS / COS / MinIO | Object Storage Connection |

## 8. Disk IO Requirements

It is recommended to use SSDs for data disks. Disk performance should meet the following standards:

| Item | Requirement |
| --- | --- |
| Mixed Read/Write IOPS | Above 5000 |
| Sequential Read/Write Throughput | Above 150 MB/s |
| Average Latency | About 5 milliseconds or lower |

After installation `fio`, you can do it in `/data`.

### 8.1 Mixed Read/Write Test

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

Pay attention to the IOPS in the results; mixed read/write IOPS should reach above 5000 to continue.

### 8.2 Sequential Read Test

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

### 8.3 Sequential Write Test

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

Sequential read and sequential write throughput reaching over 150 MB/s can continue. 

Test files can be deleted after the test is completed:

```bash
rm -f /data/testfile
```

## 9. Public Network Bandwidth Requirements

Estimate the bandwidth for public network access scenarios based on the number of users:

```text
PUBLIC_NETWORK_BANDWIDTH = NUMBER_OF_USERS x 0.25 Mbps
```

Example:

| Number of Users | Recommended Public Network Bandwidth |
| --- | --- |
| 100 users | Over 25 Mbps |
| 200 users | Over 50 Mbps |
| 500 users | Over 125 Mbps |

For intranet access scenarios, it is also recommended to use the same standards to evaluate outbound, inbound, and load balancing bandwidth.

## 10. Installer and Operation & Maintenance Platform Browser Version Recommendation

It is recommended to use Google Chrome version 111 or above, preferably the latest stable version.

## 11. Pre-Deployment Checklist

Before starting the installation, please confirm each item:

- The operating system version meets the requirements.
- CPU memory, system disk, and data disk meet the requirements.
- `/data` is mounted to a separate data disk.
- `iptables`, `gzip`, and `tar` are installed.
- System time synchronization is normal. 
- The online or offline installation method has been determined. 
- Installer port `18080` is accessible. 
- Business access port `80` or `443` is open. 
- If external middleware is used, connection information is fully prepared. 
- Object storage is compatible with the S3 protocol, and bucket and account permissions are ready. 
- Data disk IO testing meets the requirements. 
- Public or internal network bandwidth meets the expected number of users.
