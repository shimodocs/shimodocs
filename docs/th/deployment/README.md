# ShimoDocs Suite การปรับใช้และการดำเนินงาน

ใช้คู่มือเหล่านี้เพื่อวางแผน ติดตั้ง กำหนดค่า ดำเนินงาน และแก้ไขปัญหาการปรับใช้ ShimoDocs Suite แบบส่วนตัว

> [!NOTE]
> คำสั่ง, ชื่อแพ็กเกจ, เวอร์ชัน, ที่อยู่, และค่าทรัพยากรที่แสดงในคู่มือเป็นตัวอย่าง เว้นแต่จะระบุชัดเจน ใช้ค่าที่จัดเตรียมไว้กับการเปิดตัวและสภาพแวดล้อมการปรับใช้ของคุณ

## วางแผนการปรับใช้ของคุณ

- [ข้อกำหนดของระบบ](system-requirements.md)
- [การวางแผนทรัพยากร](getting-started/resource-planning.md)

## การติดตั้ง ShimoDocs Suite

- [เริ่มต้นอย่างรวดเร็ว](getting-started/quick-start.md)
- [โหนดเดียว Kubernetes การปรับใช้](getting-started/single-node-kubernetes.md)
- [ความพร้อมใช้งานสูง Kubernetes การปรับใช้](getting-started/high-availability-kubernetes.md)

## เชื่อมต่อซอฟต์แวร์กลางภายนอก

- [MySQL 8 ข้อกำหนด](middleware/mysql/requirements.md)
- [ปรับใช้ด้วย MySQL 8](middleware/mysql/deployment.md)
- [Dameng V8 ข้อกำหนด](middleware/dameng/requirements.md)
- [ปรับใช้ด้วย Dameng V8](middleware/dameng/deployment.md)
- [การกำหนดค่า Object Storage](middleware/object-storage/configuration.md)
- [การปรับใช้ด้วย Object Storage](middleware/object-storage/deployment.md)
- [Kafka การกำหนดค่า](middleware/kafka/configuration.md)
- [ปรับใช้ด้วย Kafka](middleware/kafka/deployment.md)
- [Redis การกำหนดค่า](middleware/redis/configuration.md)
- [ปรับใช้ด้วย Redis](middleware/redis/deployment.md)
- [MongoDB การกำหนดค่า](middleware/mongodb/configuration.md)
- [ปรับใช้ด้วย MongoDB](middleware/mongodb/deployment.md)

## แพลตฟอร์มปฏิบัติการ

- [ภาพรวมแพลตฟอร์มปฏิบัติการ](operations-platform/README.md)

## จัดการ ShimoDocs Suite

- [การจัดการใบอนุญาต](operations-platform/suite/license-management.md)
- [การจัดการผู้เช่า](operations-platform/suite/tenant-management.md)
- [การกำหนดค่า AI](operations-platform/suite/ai-configuration.md)
- [การจัดการผู้ใช้ชุดโปรแกรม](operations-platform/suite/user-management.md)
- [การปรับแต่งแบรนด์](operations-platform/suite/brand-customization.md)
- [การกำหนดค่าระบบ](operations-platform/suite/configuration/system-configuration.md)
- [การกำหนดค่าโปรแกรมแก้ไข](operations-platform/suite/configuration/editor-configuration.md)

## การดำเนินการบริการระบบ

- [การจัดการคลัสเตอร์](operations-platform/system-services/service-operations/cluster-management.md)
- [การกำหนดค่า Middleware](operations-platform/system-services/service-operations/middleware-configuration.md)
- [บันทึกบริการ](operations-platform/system-services/service-operations/service-logs.md)
- [บันทึกเรียลไทม์](operations-platform/system-services/service-operations/real-time-logs.md)
- [การอัปเกรดระบบ](operations-platform/system-services/service-operations/system-upgrade.md)
- [ศูนย์การกำหนดค่า](operations-platform/system-services/service-operations/configuration-center.md)

## การใช้เครื่องมือปฏิบัติการ

- [การตรวจสอบทรัพยากรสถิต](operations-platform/system-services/toolset/static-resource-monitoring.md)
- [การตรวจสอบ Middleware](operations-platform/system-services/toolset/middleware-inspection.md)
- [การจับแพ็กเก็ตคอนเทนเนอร์](operations-platform/system-services/toolset/container-packet-capture.md)
- [การทดสอบความเข้ากันได้](operations-platform/system-services/toolset/compatibility-testing.md)
- [เครื่องมือทั่วไป](operations-platform/system-services/toolset/general-tools.md)

## ใช้เครื่องมือกลาง

- [RDB เครื่องมือ](operations-platform/system-services/middleware-tools/rdb.md)
- [Kafka เครื่องมือ](operations-platform/system-services/middleware-tools/kafka.md)
- [gRPC เครื่องมือ](operations-platform/system-services/middleware-tools/grpc.md)
- [Redis เครื่องมือ](operations-platform/system-services/middleware-tools/redis.md)
- [MongoDB เครื่องมือ](operations-platform/system-services/middleware-tools/mongodb.md)

## กำหนดค่าควบคุมแผงควบคุม

- [ช่องทางการแจ้งเตือน](operations-platform/system-services/control-panel/notification-channels.md)
- [การตั้งค่าขั้นสูง](operations-platform/system-services/control-panel/advanced-settings.md)

## ควบคุมการดำเนินธุรกิจ

- [การค้นหาเหตุการณ์แปลงสื่อ](operations-platform/system-services/business-control/transcoding-events.md)
- [การค้นหาข้อมูลไฟล์](operations-platform/system-services/business-control/file-information.md)
- [การบล็อกการทำงานร่วมกัน](operations-platform/system-services/business-control/collaboration-blocking.md)
- [การซ่อมแซมเอกสาร](operations-platform/system-services/business-control/document-repair.md)

## บริหารจัดการแพลตฟอร์ม

- [การจัดการผู้ใช้แพลตฟอร์ม](operations-platform/system-services/system-management/user-management.md)
- [บันทึกการตรวจสอบ](operations-platform/system-services/system-management/audit-logs.md)

## การแก้ไขปัญหาและบำรุงรักษา

- [การแก้ไขปัญหาการติดตั้ง](troubleshooting/installation.md)
- [การสำรองข้อมูล](troubleshooting/data-backup.md)
- [การอ้างอิงตัวชี้วัดการตรวจสอบ](troubleshooting/monitoring-metrics.md)
- [เหตุการณ์การแก้ไขร่วมกัน](troubleshooting/collaboration-editing-incident.md)
- [การตอบสนองต่อเหตุการณ์ SOP](troubleshooting/incident-response-sop.md)
