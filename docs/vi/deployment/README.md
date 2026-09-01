# Triển khai và vận hành ShimoDocs Suite

Sử dụng các hướng dẫn này để lập kế hoạch, cài đặt, cấu hình, vận hành và khắc phục sự cố cho một triển khai ShimoDocs Suite riêng tư.

> [!NOTE]
> Các lệnh, tên gói, phiên bản, địa chỉ và giá trị tài nguyên hiển thị trong các hướng dẫn là các ví dụ trừ khi được nêu rõ ràng khác. Sử dụng các giá trị cung cấp cùng với phiên bản phát hành và môi trường triển khai của bạn.

## Lập kế hoạch triển khai của bạn

- [Yêu cầu hệ thống](system-requirements.md)
- [Lập kế hoạch tài nguyên](getting-started/resource-planning.md)

## Cài đặt ShimoDocs Suite

- [Bắt đầu nhanh](getting-started/quick-start.md)
- [Triển khai đơn nút Kubernetes Triển khai](getting-started/single-node-kubernetes.md)
- [Khả năng cao Kubernetes Triển khai](getting-started/high-availability-kubernetes.md)

## Kết nối phần mềm trung gian bên ngoài

- [MySQL 8 Yêu cầu](middleware/mysql/requirements.md)
- [Triển khai với MySQL 8](middleware/mysql/deployment.md)
- [Dameng V8 Yêu cầu](middleware/dameng/requirements.md)
- [Triển khai với Dameng V8](middleware/dameng/deployment.md)
- [Cấu hình lưu trữ đối tượng](middleware/object-storage/configuration.md)
- [Triển khai với lưu trữ đối tượng](middleware/object-storage/deployment.md)
- [Kafka Cấu hình](middleware/kafka/configuration.md)
- [Triển khai với Kafka](middleware/kafka/deployment.md)
- [Redis Cấu hình](middleware/redis/configuration.md)
- [Triển khai với Redis](middleware/redis/deployment.md)
- [MongoDB Cấu hình](middleware/mongodb/configuration.md)
- [Triển khai với MongoDB](middleware/mongodb/deployment.md)

## Nền tảng vận hành

- [Tổng quan nền tảng vận hành](operations-platform/README.md)

## Quản lý ShimoDocs Suite

- [Quản lý giấy phép](operations-platform/suite/license-management.md)
- [Quản lý người thuê](operations-platform/suite/tenant-management.md)
- [Cấu hình AI](operations-platform/suite/ai-configuration.md)
- [Quản lý người dùng bộ công cụ](operations-platform/suite/user-management.md)
- [Tùy chỉnh thương hiệu](operations-platform/suite/brand-customization.md)
- [Cấu hình hệ thống](operations-platform/suite/configuration/system-configuration.md)
- [Cấu hình trình soạn thảo](operations-platform/suite/configuration/editor-configuration.md)

## Vận hành các dịch vụ hệ thống

- [Quản lý cụm](operations-platform/system-services/service-operations/cluster-management.md)
- [Cấu hình phần mềm trung gian](operations-platform/system-services/service-operations/middleware-configuration.md)
- [Nhật ký dịch vụ](operations-platform/system-services/service-operations/service-logs.md)
- [Nhật ký thời gian thực](operations-platform/system-services/service-operations/real-time-logs.md)
- [Nâng cấp hệ thống](operations-platform/system-services/service-operations/system-upgrade.md)
- [Trung tâm cấu hình](operations-platform/system-services/service-operations/configuration-center.md)

## Sử dụng công cụ vận hành

- [Giám sát tài nguyên tĩnh](operations-platform/system-services/toolset/static-resource-monitoring.md)
- [Kiểm tra phần mềm trung gian](operations-platform/system-services/toolset/middleware-inspection.md)
- [Chụp gói trong container](operations-platform/system-services/toolset/container-packet-capture.md)
- [Kiểm tra Tương thích](operations-platform/system-services/toolset/compatibility-testing.md)
- [Công cụ Chung](operations-platform/system-services/toolset/general-tools.md)

## Sử dụng công cụ middleware

- [RDB Công cụ](operations-platform/system-services/middleware-tools/rdb.md)
- [Kafka Công cụ](operations-platform/system-services/middleware-tools/kafka.md)
- [gRPC Công cụ](operations-platform/system-services/middleware-tools/grpc.md)
- [Redis Công cụ](operations-platform/system-services/middleware-tools/redis.md)
- [MongoDB Công cụ](operations-platform/system-services/middleware-tools/mongodb.md)

## Cấu hình bảng điều khiển

- [Kênh Thông báo](operations-platform/system-services/control-panel/notification-channels.md)
- [Cài đặt Nâng cao](operations-platform/system-services/control-panel/advanced-settings.md)

## Kiểm soát hoạt động kinh doanh

- [Tìm kiếm Sự kiện Transcoding](operations-platform/system-services/business-control/transcoding-events.md)
- [Tìm kiếm Thông tin Tệp](operations-platform/system-services/business-control/file-information.md)
- [Chặn Hợp tác](operations-platform/system-services/business-control/collaboration-blocking.md)
- [Sửa tài liệu](operations-platform/system-services/business-control/document-repair.md)

## Quản trị nền tảng

- [Quản lý Người dùng Nền tảng](operations-platform/system-services/system-management/user-management.md)
- [Nhật ký Kiểm toán](operations-platform/system-services/system-management/audit-logs.md)

## Xử lý sự cố và bảo trì

- [Xử lý sự cố Cài đặt](troubleshooting/installation.md)
- [Sao lưu Dữ liệu](troubleshooting/data-backup.md)
- [Tham khảo Chỉ số Giám sát](troubleshooting/monitoring-metrics.md)
- [Sự cố Chỉnh sửa Hợp tác](troubleshooting/collaboration-editing-incident.md)
- [Phản hồi Sự cố SOP](troubleshooting/incident-response-sop.md)
