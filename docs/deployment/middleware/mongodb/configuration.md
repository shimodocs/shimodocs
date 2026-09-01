# MongoDB Configuration

[← ShimoDocs Suite Deployment Documentation](../../README.md)

This article is intended to guide implementation, operations, or integration personnel to complete the step-by-step integration of ShimoDocs with external MongoDB.

## 1. Pre-operation Confirmation

## MongoDB Instance Requirements

| Middleware | Recommended Version | Less than 3000 users | More than 3000 users |
| --- | --- | --- | --- |
| MongoDB | MongoDB 4.4 | 2C 8G 100G SSD | 4C 16G 100G SSD |

## Cluster Configuration Requirements
- Support replica set high-availability clusters; at least 3 nodes are required in a production environment
- It is recommended to enable SCRAM-SHA-256 authentication

## Network Connection

The port must be open for K8s commercial cluster to access MongoDB

```js
telnet IP 27017
```

## Authentication and Authorization
- In production environments, it is recommended to enforce SCRAM-SHA-256 authentication.

## Other Requirements
- Internal network P99 read latency < 5 ms, write latency < 10 ms
- Disk IOPS must meet peak write requirements; SSDs are mandatory
- Clock synchronization: MongoDB cluster nodes and ShimoDocs application servers must be NTP synchronized
- Regular full backups and continuous Oplog backups
