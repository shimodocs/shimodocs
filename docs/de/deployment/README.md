# Bereitstellung und Betrieb der ShimoDocs Suite

Verwenden Sie diese Anleitungen, um eine private Bereitstellung der ShimoDocs Suite zu planen, zu installieren, zu konfigurieren, zu betreiben und Fehler zu beheben.

> [!NOTE]
> Befehle, Paketnamen, Versionen, Adressen und Ressourcenzahlen, die in den Anleitungen gezeigt werden, sind Beispiele, sofern nicht ausdrücklich anders angegeben. Verwenden Sie die Werte, die mit Ihrer Version und der Bereitstellungsumgebung geliefert werden.

## Planen Sie Ihre Bereitstellung

- [Systemanforderungen](system-requirements.md)
- [Ressourcenplanung](getting-started/resource-planning.md)

## Installation ShimoDocs Suite

- [Schnellstart](getting-started/quick-start.md)
- [Einzelknoten Kubernetes Bereitstellung](getting-started/single-node-kubernetes.md)
- [Hohe Verfügbarkeit Kubernetes Bereitstellung](getting-started/high-availability-kubernetes.md)

## Externe Middleware verbinden

- [MySQL 8 Anforderungen](middleware/mysql/requirements.md)
- [Bereitstellen mit MySQL 8](middleware/mysql/deployment.md)
- [Dameng V8 Anforderungen](middleware/dameng/requirements.md)
- [Bereitstellen mit Dameng V8](middleware/dameng/deployment.md)
- [Objektspeicher-Konfiguration](middleware/object-storage/configuration.md)
- [Bereitstellen mit Objektspeicher](middleware/object-storage/deployment.md)
- [Kafka Konfiguration](middleware/kafka/configuration.md)
- [Bereitstellen mit Kafka](middleware/kafka/deployment.md)
- [Redis Konfiguration](middleware/redis/configuration.md)
- [Bereitstellen mit Redis](middleware/redis/deployment.md)
- [MongoDB Konfiguration](middleware/mongodb/configuration.md)
- [Bereitstellen mit MongoDB](middleware/mongodb/deployment.md)

## Betriebsplattform

- [Übersicht der Betriebsplattform](operations-platform/README.md)

## Verwalten ShimoDocs Suite

- [Lizenzverwaltung](operations-platform/suite/license-management.md)
- [Mandantenverwaltung](operations-platform/suite/tenant-management.md)
- [KI-Konfiguration](operations-platform/suite/ai-configuration.md)
- [Benutzerverwaltung der Suite](operations-platform/suite/user-management.md)
- [Markenanpassung](operations-platform/suite/brand-customization.md)
- [Systemkonfiguration](operations-platform/suite/configuration/system-configuration.md)
- [Editor-Konfiguration](operations-platform/suite/configuration/editor-configuration.md)

## Systemdienste betreiben

- [Clusterverwaltung](operations-platform/system-services/service-operations/cluster-management.md)
- [Middleware-Konfiguration](operations-platform/system-services/service-operations/middleware-configuration.md)
- [Service-Logs](operations-platform/system-services/service-operations/service-logs.md)
- [Echtzeit-Logs](operations-platform/system-services/service-operations/real-time-logs.md)
- [System-Upgrade](operations-platform/system-services/service-operations/system-upgrade.md)
- [Konfigurationszentrum](operations-platform/system-services/service-operations/configuration-center.md)

## Betriebstools verwenden

- [Überwachung statischer Ressourcen](operations-platform/system-services/toolset/static-resource-monitoring.md)
- [Middleware-Inspektion](operations-platform/system-services/toolset/middleware-inspection.md)
- [Container-Paket-Erfassung](operations-platform/system-services/toolset/container-packet-capture.md)
- [Kompatibilitätstest](operations-platform/system-services/toolset/compatibility-testing.md)
- [Allgemeine Werkzeuge](operations-platform/system-services/toolset/general-tools.md)

## Verwenden von Middleware-Tools

- [RDB Werkzeuge](operations-platform/system-services/middleware-tools/rdb.md)
- [Kafka Werkzeuge](operations-platform/system-services/middleware-tools/kafka.md)
- [gRPC Werkzeuge](operations-platform/system-services/middleware-tools/grpc.md)
- [Redis Werkzeuge](operations-platform/system-services/middleware-tools/redis.md)
- [MongoDB Werkzeuge](operations-platform/system-services/middleware-tools/mongodb.md)

## Konfigurieren der Systemsteuerung

- [Benachrichtigungskanäle](operations-platform/system-services/control-panel/notification-channels.md)
- [Erweiterte Einstellungen](operations-platform/system-services/control-panel/advanced-settings.md)

## Geschäftsvorgänge steuern

- [Transcodierung Ereignissuche](operations-platform/system-services/business-control/transcoding-events.md)
- [Dateiinformationssuche](operations-platform/system-services/business-control/file-information.md)
- [Zusammenarbeitsblockierung](operations-platform/system-services/business-control/collaboration-blocking.md)
- [Dokumentenreparatur](operations-platform/system-services/business-control/document-repair.md)

## Verwaltung der Plattform

- [Plattformbenutzerverwaltung](operations-platform/system-services/system-management/user-management.md)
- [Prüfprotokolle](operations-platform/system-services/system-management/audit-logs.md)

## Fehlerbehebung und Wartung

- [Installation Fehlerbehebung](troubleshooting/installation.md)
- [Datensicherung](troubleshooting/data-backup.md)
- [Überwachungsmetriken Referenz](troubleshooting/monitoring-metrics.md)
- [Zusammenarbeitsbearbeitung Vorfall](troubleshooting/collaboration-editing-incident.md)
- [Vorfallreaktion SOP](troubleshooting/incident-response-sop.md)
