# ShimoDocs Suite 部署与运维

使用这些指南规划、安装、配置、运维和排查 ShimoDocs Suite 私有化部署。

> [!NOTE]
> 指南中显示的命令、软件包名称、版本、地址和资源值仅为示例，除非另有明确说明。请使用随您的发行版和部署环境提供的值。

## 规划您的部署

- [系统要求](system-requirements.md)
- [资源规划](getting-started/resource-planning.md)

## 安装 ShimoDocs Suite

- [快速开始](getting-started/quick-start.md)
- [单节点 Kubernetes 部署](getting-started/single-node-kubernetes.md)
- [高可用性 Kubernetes 部署](getting-started/high-availability-kubernetes.md)

## 连接外部中间件

- [MySQL 8 要求](middleware/mysql/requirements.md)
- [部署与 MySQL 8](middleware/mysql/deployment.md)
- [Dameng V8 要求](middleware/dameng/requirements.md)
- [部署与 Dameng V8](middleware/dameng/deployment.md)
- [对象存储配置](middleware/object-storage/configuration.md)
- [使用对象存储部署](middleware/object-storage/deployment.md)
- [Kafka 配置](middleware/kafka/configuration.md)
- [部署与 Kafka](middleware/kafka/deployment.md)
- [Redis 配置](middleware/redis/configuration.md)
- [部署与 Redis](middleware/redis/deployment.md)
- [MongoDB 配置](middleware/mongodb/configuration.md)
- [部署与 MongoDB](middleware/mongodb/deployment.md)

## 运维平台

- [运维平台概览](operations-platform/README.md)

## 管理 ShimoDocs Suite

- [许可证管理](operations-platform/suite/license-management.md)
- [租户管理](operations-platform/suite/tenant-management.md)
- [AI 配置](operations-platform/suite/ai-configuration.md)
- [套件用户管理](operations-platform/suite/user-management.md)
- [品牌定制](operations-platform/suite/brand-customization.md)
- [系统配置](operations-platform/suite/configuration/system-configuration.md)
- [编辑器配置](operations-platform/suite/configuration/editor-configuration.md)

## 操作系统服务

- [集群管理](operations-platform/system-services/service-operations/cluster-management.md)
- [中间件配置](operations-platform/system-services/service-operations/middleware-configuration.md)
- [服务日志](operations-platform/system-services/service-operations/service-logs.md)
- [实时日志](operations-platform/system-services/service-operations/real-time-logs.md)
- [系统升级](operations-platform/system-services/service-operations/system-upgrade.md)
- [配置中心](operations-platform/system-services/service-operations/configuration-center.md)

## 使用运维工具

- [静态资源监控](operations-platform/system-services/toolset/static-resource-monitoring.md)
- [中间件检测](operations-platform/system-services/toolset/middleware-inspection.md)
- [容器抓包](operations-platform/system-services/toolset/container-packet-capture.md)
- [兼容性测试](operations-platform/system-services/toolset/compatibility-testing.md)
- [通用工具](operations-platform/system-services/toolset/general-tools.md)

## 使用中间件工具

- [RDB 工具](operations-platform/system-services/middleware-tools/rdb.md)
- [Kafka 工具](operations-platform/system-services/middleware-tools/kafka.md)
- [gRPC 工具](operations-platform/system-services/middleware-tools/grpc.md)
- [Redis 工具](operations-platform/system-services/middleware-tools/redis.md)
- [MongoDB 工具](operations-platform/system-services/middleware-tools/mongodb.md)

## 配置控制面板

- [通知渠道](operations-platform/system-services/control-panel/notification-channels.md)
- [高级设置](operations-platform/system-services/control-panel/advanced-settings.md)

## 控制业务操作

- [转码事件搜索](operations-platform/system-services/business-control/transcoding-events.md)
- [文件信息搜索](operations-platform/system-services/business-control/file-information.md)
- [协作阻止](operations-platform/system-services/business-control/collaboration-blocking.md)
- [文档修复](operations-platform/system-services/business-control/document-repair.md)

## 管理平台

- [平台用户管理](operations-platform/system-services/system-management/user-management.md)
- [审计日志](operations-platform/system-services/system-management/audit-logs.md)

## 故障排查与维护

- [安装故障排查](troubleshooting/installation.md)
- [数据备份](troubleshooting/data-backup.md)
- [监控指标参考](troubleshooting/monitoring-metrics.md)
- [协作编辑事件](troubleshooting/collaboration-editing-incident.md)
- [事件响应 SOP](troubleshooting/incident-response-sop.md)
