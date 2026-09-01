# ShimoDocs Suite の導入・運用

これらのガイドでは、ShimoDocs Suite のプライベート環境への導入について、計画、インストール、設定、運用、トラブルシューティングの手順を説明します。

> [!NOTE]
> ガイドに表示されているコマンド、パッケージ名、バージョン、アドレス、およびリソース値は、明示的に別途記載されていない限り例です。リリースおよび展開環境で提供される値を使用してください。

## 展開を計画する

- [システム要件](system-requirements.md)
- [リソース計画](getting-started/resource-planning.md)

## インストール ShimoDocs Suite

- [クイックスタート](getting-started/quick-start.md)
- [シングルノード Kubernetes 展開](getting-started/single-node-kubernetes.md)
- [高可用性 Kubernetes 展開](getting-started/high-availability-kubernetes.md)

## 外部ミドルウェアに接続

- [MySQL 8 要件](middleware/mysql/requirements.md)
- [で展開 MySQL 8](middleware/mysql/deployment.md)
- [Dameng V8 要件](middleware/dameng/requirements.md)
- [で展開 Dameng V8](middleware/dameng/deployment.md)
- [オブジェクトストレージ構成](middleware/object-storage/configuration.md)
- [オブジェクトストレージで展開](middleware/object-storage/deployment.md)
- [Kafka 構成](middleware/kafka/configuration.md)
- [で展開 Kafka](middleware/kafka/deployment.md)
- [Redis 構成](middleware/redis/configuration.md)
- [で展開 Redis](middleware/redis/deployment.md)
- [MongoDB 構成](middleware/mongodb/configuration.md)
- [で展開 MongoDB](middleware/mongodb/deployment.md)

## 運用プラットフォーム

- [運用プラットフォーム概要](operations-platform/README.md)

## 管理 ShimoDocs Suite

- [ライセンス管理](operations-platform/suite/license-management.md)
- [テナント管理](operations-platform/suite/tenant-management.md)
- [AI構成](operations-platform/suite/ai-configuration.md)
- [スイートユーザー管理](operations-platform/suite/user-management.md)
- [ブランドカスタマイズ](operations-platform/suite/brand-customization.md)
- [システム構成](operations-platform/suite/configuration/system-configuration.md)
- [エディター構成](operations-platform/suite/configuration/editor-configuration.md)

## システムサービスを運用

- [クラスタ管理](operations-platform/system-services/service-operations/cluster-management.md)
- [ミドルウェア構成](operations-platform/system-services/service-operations/middleware-configuration.md)
- [サービスログ](operations-platform/system-services/service-operations/service-logs.md)
- [リアルタイムログ](operations-platform/system-services/service-operations/real-time-logs.md)
- [システムアップグレード](operations-platform/system-services/service-operations/system-upgrade.md)
- [構成センター](operations-platform/system-services/service-operations/configuration-center.md)

## 運用ツールを使用

- [静的リソース監視](operations-platform/system-services/toolset/static-resource-monitoring.md)
- [ミドルウェア検査](operations-platform/system-services/toolset/middleware-inspection.md)
- [コンテナパケットキャプチャ](operations-platform/system-services/toolset/container-packet-capture.md)
- [互換性テスト](operations-platform/system-services/toolset/compatibility-testing.md)
- [一般ツール](operations-platform/system-services/toolset/general-tools.md)

## ミドルウェアツールを使用

- [RDB ツール](operations-platform/system-services/middleware-tools/rdb.md)
- [Kafka ツール](operations-platform/system-services/middleware-tools/kafka.md)
- [gRPC ツール](operations-platform/system-services/middleware-tools/grpc.md)
- [Redis ツール](operations-platform/system-services/middleware-tools/redis.md)
- [MongoDB ツール](operations-platform/system-services/middleware-tools/mongodb.md)

## コントロールパネルの設定

- [通知チャネル](operations-platform/system-services/control-panel/notification-channels.md)
- [高度な設定](operations-platform/system-services/control-panel/advanced-settings.md)

## 業務運営を管理

- [トランスコーディングイベント検索](operations-platform/system-services/business-control/transcoding-events.md)
- [ファイル情報検索](operations-platform/system-services/business-control/file-information.md)
- [共同作業のブロック](operations-platform/system-services/business-control/collaboration-blocking.md)
- [文書修復](operations-platform/system-services/business-control/document-repair.md)

## プラットフォームを管理

- [プラットフォームユーザー管理](operations-platform/system-services/system-management/user-management.md)
- [監査ログ](operations-platform/system-services/system-management/audit-logs.md)

## トラブルシューティングと保守

- [インストール時のトラブルシューティング](troubleshooting/installation.md)
- [データバックアップ](troubleshooting/data-backup.md)
- [監視指標リファレンス](troubleshooting/monitoring-metrics.md)
- [共同編集インシデント](troubleshooting/collaboration-editing-incident.md)
- [インシデント対応 SOP](troubleshooting/incident-response-sop.md)
