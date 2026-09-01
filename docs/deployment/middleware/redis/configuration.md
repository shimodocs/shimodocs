# Redis Configuration

[← ShimoDocs Suite Deployment Documentation](../../README.md)

This document aims to guide implementers, operations personnel, or integrators to complete the integration step by step. ShimoDocs uses external Redis, which is usually applied in core scenarios such as session management, distributed locks, rate-limiting counters, and message queues.

## 1. Pre-Operational Confirmation

## Redis Instance Requirements

| Middleware | Recommended Version | Suitable for Fewer than 3000 Users | Suitable for More than 3000 Users |
| --- | --- | --- | --- |
| Redis | Redis 6.2.21 | 2C 4G 100G SSD | 2C 8G 100G SSD |

## Cluster Configuration Requirements
- Support master-slave/sentinel high availability
- Data persistence
- Cluster mode not supported
- The number of databases must be >=64

## Network Connection

The ports used for connecting the K8s business cluster network to the Redis instance must be open

```js
telnet IP 6379
```

## Authentication and Authorization
- In a production environment, it is recommended to enable PASSWORD authentication (requirepass / ACL).

## Other Requirements
- Internal network P99 latency should be less than 10 milliseconds
- Clock synchronization: Redis cluster nodes and ShimoDocs application servers must be NTP synchronized
- Regular full backups
