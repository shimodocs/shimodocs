# Kafka Configuration

[← ShimoDocs Suite Deployment Documentation](../../README.md)

This document aims to guide implementers, operators, or integrators to complete the step-by-step integration of ShimoDocs with external message middleware Kafka, suitable for scenarios such as asynchronous task processing, message notification, data synchronization, and audit log transmission.

## 1. Pre-Operation Confirmation

## Kafka Instance Requirements

| Middleware | Recommended Version | Users < 3000 | Users > 3000 |
| --- | --- | --- | --- |
| Kafka | Kafka 3.5 | 2-core 4G 300G SSD | 4-core 8G 300G SSD |

## Configuration Requirements
- Broker >= 3
- Replication factor: The default number of replicas is 3. In production environments, it must be ≥ 3 to ensure high availability. 
- Message retention: 72 hours (adjustable according to business needs). 
- Maximum single message size per topic: 10 MB. 
- Authentication: Supports SASL encrypted access (PLAIN, SCRAM-SHA-256, SCRAM-SHA-512). 

## Network Connectivity

The port for accessing the Kafka instance must be open from the K8s business cluster.

```js
telnet IP 9092
```



## Other Requirements
- Intranet RTT is recommended to be < 5ms; across data center/region is recommended to be < 20ms.
- Bandwidth must meet peak throughput to avoid message backlog caused by network saturation.
- Ensure Kafka Broker and ShimoDocs application server are time synchronized (NTP), as time deviation may affect message ordering and TTL calculation.
