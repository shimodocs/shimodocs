# Installation Troubleshooting

[← ShimoDocs Suite Deployment Documentation](../README.md)

> [!TIP]
>
> Common issues during the installation phase usually fall into the following categories.

## 1 Time Desynchronization

Problem Symptoms:

* Login failure

* Authentication errors

* Service invocation anomalies

Handling Requirements:

* First check the time deviation of all nodes

* After fixing the /time synchronization service, continue installation or acceptance with NTP

Investigation Commands:

```plain
timedatectl status
date
```


## 2 Data Disk Path Configuration Error

Phenomena:

* After installation, the disk fills up quickly
* Data writing fails
* Persistent directory is located on the system drive

Handling Requirements:

* Persistent directory must clearly point to the data disk
* Business data should not be stored in the system disk directory

Troubleshooting Commands:

```plain
findmnt -n -o TARGET -T /data
df -Th|egrep -v "overlay|tmpfs"
```


## 3 Dependent Service Connection Failure

Phenomenon:

* Service check failed during installation

* Failed to connect to database, cache, message queue, or object storage

Handling Requirements:

* First, check that the address, port, account, and PASSWORD are entered correctly

* Then check network connection and security policies

* Finally, check whether the target service itself is available

Troubleshooting Commands:

```plain
nc -zv <MYSQL_HOST> 3306
nc -zv <REDIS_HOST> 6379
nc -zv <MONGO_HOST> 27017
nc -zv <KAFKA_HOST> 9092
```


## Offline Package Mismatch

Phenomenon:

* Image loading failed

* Service cannot start during installation and version mismatch reported

* Installation package does not correspond to offline image package

Handling Requirements:

* Ensure that the installation package, offline image package, and product version are consistent

* Ensure that the installation package matches the CPU architecture

* Ensure that materials from different projects or different dates are not mixed

## Installer Page Cannot Be Opened

Phenomenon:

* Unable to access the Web UI page

* Port 18080 is not listening

* Installer process has exited

Troubleshooting Commands:

```plain
ps -ef | grep mdp | grep -v grep
ss -lntp | grep 18080
tail -n 100 /root/nohup.out
```


## 6. Recommended Troubleshooting Sequence

Follow the sequence below to diagnose installation issues:

1. First, confirm whether it is an environment issue: system, time, disk, port, network.

2. Next, confirm whether it is a configuration issue: domain name, directory, dependency addresses, account PASSWORD.

3. Then, confirm whether it is a materials issue: installation package, offline package, architecture compatibility.

4. Finally, check the installer logs and service running status.

Notes:

* If prerequisites are not met, do not attempt reinstallation.

* Do not repeat commands with the same clear failure reason.

## 7. When to Stop Installation

If any of the following situations occur, stop the installation, resolve the root cause, and then continue:

* All nodes have unsynchronized time.

* Data disks are not mounted separately.

* External dependent services are inaccessible.

* Offline material versions are inconsistent.

* Installation services do not start properly.
