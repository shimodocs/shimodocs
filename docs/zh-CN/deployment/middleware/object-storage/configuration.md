# 对象存储配置

[← ShimoDocs Suite 部署文档](../../README.md)

本文件旨在指导实施、运维或集成人员逐步完成 ShimoDocs 连接到外部第三方 S3 对象存储的逐步操作。


# 1. 操作前确认

## S3 对象存储要求

`Only object storage fully compatible with the S3 protocol is supported. Huawei Cloud OBS, Alibaba Cloud OSS, Tencent Cloud COS, and AWS S3 are recommended. For local deployment, MinIO can be considered.`



> [!TIP]
>
> 优先公共云，需要支持 HTTPS 外部访问



## 网络连接

所需的端口 K8s 业务集群网络需要开放以连接到对象存储实例

```js
telnet IP 9000
```
## 访问控制和权限
- 提供完整的AK/SK认证信息
- 必须完全支持核心接口，如 PutObject、GetObject、DeleteObject、ListObjects、CopyObject、InitiateMultipartUpload



# S3 存储要求说明
- 延迟要求：在内部网络环境中，存储的平均响应时间 API 小于50毫秒；在公共网络环境下，建议小于200毫秒。高延迟会直接影响文档打开速度和附件上传体验。
- 并发能力：必须支持企业预估的峰值 QPS. ShimoDocs 在多用户协作和批量导入/导出期间会产生突发流量，因此存储端不能有过于严格的限流策略。
- 可用性 SLA：建议生产环境下存储可用性≥99.9%。
- 公网存储通信必须通过 HTTPS/TLS 加密通道进行。
- 时间同步： S3 存储服务和 ShimoDocs 应用服务器必须通过 NTP进行同步，否则 S3 签名验证将失败。
