# MySQL 8 Requirements

[← ShimoDocs Suite Deployment Documentation](../../README.md)

This document is intended to guide implementation, operations, or integration personnel step by step to complete the initialization connection of ShimoDocs to the MySQL 8 database, as well as service startup and connection verification.


## 1. Pre-Operation Confirmation

## MySQL Instance Specifications Confirmation

| Recommended Version | Less than 3000 users | More than 3000 users | 
| --- | --- | --- |
| MySQL 8.0 | 4 cores 8G 200G SSD | 8 cores 16G 200G SSD | 

## MySQL Configuration and High Availability Requirements
Support master-slave high availability switching
Character set: utf8mb4
Time zone: Asia/Shanghai or UTC
Connections: max_connections ≥ 1000
Connection user: administrator privileges

> [!TIP]
>
> A separate MySQL instance must be configured;
> 1. To achieve fault isolation, security of permissions, and independent backup and recovery, ensuring the stable and efficient operation of the document system.
> 2. The system currently does not support custom database names and table prefixes, so a separate instance must be planned and prepared before deployment.



## Network Connectivity
The port connecting the k8s business cluster network to the MySQL instance needs to be open

```js
telnet IP 3306
```
## User Authentication
Users need to be authenticated as MySQL users when connecting to the MySQL server.

# Notes:
- The configurations for users in the MySQL documentation are recommended settings for capacity assessment and resource planning in the early stages of a project, not the final production configurations. Actual final configurations will be determined after pre-sales evaluations.
- When using servers with domestic CPU architectures, it is recommended to estimate overall resources at twice the standard specification.
- It is recommended to reserve expansion capacity in the formal production environment and prioritize high availability deployment.
