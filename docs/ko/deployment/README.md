# ShimoDocs Suite 배포 및 운영

이 가이드를 사용하여 ShimoDocs Suite의 프라이빗 배포를 계획, 설치, 구성, 운영하고 문제를 해결할 수 있습니다.

> [!NOTE]
> 가이드에 표시된 명령어, 패키지 이름, 버전, 주소 및 리소스 값은 명시적으로 달리 언급되지 않는 한 예시입니다. 릴리스 및 배포 환경과 함께 제공된 값을 사용하십시오.

## 배포 계획

- [시스템 요구 사항](system-requirements.md)
- [리소스 계획](getting-started/resource-planning.md)

## 설치 ShimoDocs Suite

- [빠른 시작](getting-started/quick-start.md)
- [단일 노드 Kubernetes 배포](getting-started/single-node-kubernetes.md)
- [고가용성 Kubernetes 배포](getting-started/high-availability-kubernetes.md)

## 외부 미들웨어 연결

- [MySQL 8 요구 사항](middleware/mysql/requirements.md)
- [와 함께 배포 MySQL 8](middleware/mysql/deployment.md)
- [Dameng V8 요구 사항](middleware/dameng/requirements.md)
- [와 함께 배포 Dameng V8](middleware/dameng/deployment.md)
- [객체 스토리지 구성](middleware/object-storage/configuration.md)
- [객체 스토리지로 배포](middleware/object-storage/deployment.md)
- [Kafka 구성](middleware/kafka/configuration.md)
- [와 함께 배포 Kafka](middleware/kafka/deployment.md)
- [Redis 구성](middleware/redis/configuration.md)
- [와 함께 배포 Redis](middleware/redis/deployment.md)
- [MongoDB 구성](middleware/mongodb/configuration.md)
- [와 함께 배포 MongoDB](middleware/mongodb/deployment.md)

## 운영 플랫폼

- [운영 플랫폼 개요](operations-platform/README.md)

## 관리 ShimoDocs Suite

- [라이선스 관리](operations-platform/suite/license-management.md)
- [테넌트 관리](operations-platform/suite/tenant-management.md)
- [AI 구성](operations-platform/suite/ai-configuration.md)
- [스위트 사용자 관리](operations-platform/suite/user-management.md)
- [브랜드 맞춤화](operations-platform/suite/brand-customization.md)
- [시스템 구성](operations-platform/suite/configuration/system-configuration.md)
- [편집기 구성](operations-platform/suite/configuration/editor-configuration.md)

## 시스템 서비스 운영

- [클러스터 관리](operations-platform/system-services/service-operations/cluster-management.md)
- [미들웨어 구성](operations-platform/system-services/service-operations/middleware-configuration.md)
- [서비스 로그](operations-platform/system-services/service-operations/service-logs.md)
- [실시간 로그](operations-platform/system-services/service-operations/real-time-logs.md)
- [시스템 업그레이드](operations-platform/system-services/service-operations/system-upgrade.md)
- [구성 센터](operations-platform/system-services/service-operations/configuration-center.md)

## 운영 도구 사용

- [정적 리소스 모니터링](operations-platform/system-services/toolset/static-resource-monitoring.md)
- [미들웨어 점검](operations-platform/system-services/toolset/middleware-inspection.md)
- [컨테이너 패킷 캡처](operations-platform/system-services/toolset/container-packet-capture.md)
- [호환성 테스트](operations-platform/system-services/toolset/compatibility-testing.md)
- [일반 도구](operations-platform/system-services/toolset/general-tools.md)

## 미들웨어 도구 사용

- [RDB 도구](operations-platform/system-services/middleware-tools/rdb.md)
- [Kafka 도구](operations-platform/system-services/middleware-tools/kafka.md)
- [gRPC 도구](operations-platform/system-services/middleware-tools/grpc.md)
- [Redis 도구](operations-platform/system-services/middleware-tools/redis.md)
- [MongoDB 도구](operations-platform/system-services/middleware-tools/mongodb.md)

## 제어판 구성

- [알림 채널](operations-platform/system-services/control-panel/notification-channels.md)
- [고급 설정](operations-platform/system-services/control-panel/advanced-settings.md)

## 비즈니스 운영 제어

- [트랜스코딩 이벤트 검색](operations-platform/system-services/business-control/transcoding-events.md)
- [파일 정보 검색](operations-platform/system-services/business-control/file-information.md)
- [협업 차단](operations-platform/system-services/business-control/collaboration-blocking.md)
- [문서 복구](operations-platform/system-services/business-control/document-repair.md)

## 플랫폼 관리

- [플랫폼 사용자 관리](operations-platform/system-services/system-management/user-management.md)
- [감사 로그](operations-platform/system-services/system-management/audit-logs.md)

## 문제 해결 및 유지보수

- [설치 문제 해결](troubleshooting/installation.md)
- [데이터 백업](troubleshooting/data-backup.md)
- [모니터링 지표 참조](troubleshooting/monitoring-metrics.md)
- [협업 편집 사건](troubleshooting/collaboration-editing-incident.md)
- [사고 대응 SOP](troubleshooting/incident-response-sop.md)
