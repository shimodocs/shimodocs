# Object Storage Configuration

[← ShimoDocs Suite Deployment Documentation](../../README.md)

This document is intended to guide implementation, operations, or integration personnel step by step to connect ShimoDocs to external third-party S3 object storage.

# 1. Pre-Operation Checklist

## S3 Object Storage Requirements

`Only object storage fully compatible with the S3 protocol is supported. Huawei Cloud OBS, Alibaba Cloud OSS, Tencent Cloud COS, and AWS S3 are recommended. For local deployment, MinIO can be considered.`



> [!TIP]
>
> Prioritize public cloud, HTTPS external access support required

## Network Connection

The required ports for the K8s business cluster network need to be open to connect to the object storage instance

```js
telnet IP 9000
```
## Access Control and Permissions
- Provide complete AK/SK authentication information
- Must fully support core interfaces, such as PutObject, GetObject, DeleteObject, ListObjects, CopyObject, InitiateMultipartUpload

# S3 Storage Requirement Specification
- Latency Requirements: In an internal network environment, the average API response time for storage should be less than 50 milliseconds; in a public network environment, it is recommended to be less than 200 milliseconds. High latency will directly affect the speed of document opening and attachment uploading experience.
- Concurrency Capability: Must support the peak QPS estimated by the enterprise. ShimoDocs will generate burst traffic during multi-user collaboration and batch import/export, so the storage side should not have overly strict rate-limiting policies.
- Availability SLA: It is recommended that storage availability in the production environment be ≥99.9%.
- Public network storage communication must be conducted through HTTPS/TLS encrypted channels.
- Time synchronization: S3 storage services and ShimoDocs application servers must be synchronized via NTP, otherwise S3 signature verification will fail.
