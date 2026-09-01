# MongoDB 配置

[← ShimoDocs Suite 部署文档](../../README.md)

本文旨在指导实施、运营或集成人员完成集成 ShimoDocs 有外部 MongoDB 逐步

## 1. 操作前确认

## MongoDB 实例要求


| 中间件 | 推荐版本 | 用户少于3000 | 用户超过3000 |
| --- | --- | --- | --- |
| MongoDB | MongoDB 4.4 | 2C 8G 100G SSD | 4C 16G 100G SSD |


## 集群配置要求
- 支持副本集高可用集群，生产环境中至少需要 3 个节点
- 建议启用 SCRAM-SHA-256 认证





## 网络连接

端口用于 K8s 商业集群访问 MongoDB 必须打开

```js
telnet IP 27017
```

## 身份验证和授权
- 在生产环境中，建议强制执行 SCRAM-SHA-256 验证。


## 其他要求
- 内部网络 P99 读取延迟 < 5 毫秒，写入延迟 < 10 毫秒
- 磁盘 IOPS 必须满足峰值写入要求； SSD 是强制性的
- 时钟同步： MongoDB 集群节点和 ShimoDocs 应用服务器必须 NTP 保持同步
- 定期完整备份和连续 Oplog 备份
