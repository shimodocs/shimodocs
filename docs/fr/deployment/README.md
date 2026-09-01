# Déploiement et exploitation de ShimoDocs Suite

Utilisez ces guides pour planifier, installer, configurer, exploiter et dépanner un déploiement privé de ShimoDocs Suite.

> [!NOTE]
> Les commandes, noms de packages, versions, adresses et valeurs de ressources présentés dans les guides sont des exemples sauf indication contraire explicite. Utilisez les valeurs fournies avec votre version et votre environnement de déploiement.

## Planifiez votre déploiement

- [Exigences du système](system-requirements.md)
- [Planification des ressources](getting-started/resource-planning.md)

## Installation ShimoDocs Suite

- [Démarrage rapide](getting-started/quick-start.md)
- [Déploiement sur un seul nœud Kubernetes Déploiement](getting-started/single-node-kubernetes.md)
- [Haute disponibilité Kubernetes Déploiement](getting-started/high-availability-kubernetes.md)

## Connecter les middlewares externes

- [MySQL 8 Exigences](middleware/mysql/requirements.md)
- [Déployer avec MySQL 8](middleware/mysql/deployment.md)
- [Dameng Exigences V8](middleware/dameng/requirements.md)
- [Déployer avec Dameng V8](middleware/dameng/deployment.md)
- [Configuration du stockage d'objets](middleware/object-storage/configuration.md)
- [Déployer avec le stockage d'objets](middleware/object-storage/deployment.md)
- [Kafka Configuration](middleware/kafka/configuration.md)
- [Déployer avec Kafka](middleware/kafka/deployment.md)
- [Redis Configuration](middleware/redis/configuration.md)
- [Déployer avec Redis](middleware/redis/deployment.md)
- [MongoDB Configuration](middleware/mongodb/configuration.md)
- [Déployer avec MongoDB](middleware/mongodb/deployment.md)

## Plateforme d'opérations

- [Aperçu de la plateforme d'opérations](operations-platform/README.md)

## Gérer ShimoDocs Suite

- [Gestion des licences](operations-platform/suite/license-management.md)
- [Gestion des locataires](operations-platform/suite/tenant-management.md)
- [Configuration de l'IA](operations-platform/suite/ai-configuration.md)
- [Gestion des utilisateurs de la suite](operations-platform/suite/user-management.md)
- [Personnalisation de la marque](operations-platform/suite/brand-customization.md)
- [Configuration du système](operations-platform/suite/configuration/system-configuration.md)
- [Configuration de l'éditeur](operations-platform/suite/configuration/editor-configuration.md)

## Exploiter les services système

- [Gestion du cluster](operations-platform/system-services/service-operations/cluster-management.md)
- [Configuration des middlewares](operations-platform/system-services/service-operations/middleware-configuration.md)
- [Journaux des services](operations-platform/system-services/service-operations/service-logs.md)
- [Journaux en temps réel](operations-platform/system-services/service-operations/real-time-logs.md)
- [Mise à niveau du système](operations-platform/system-services/service-operations/system-upgrade.md)
- [Centre de configuration](operations-platform/system-services/service-operations/configuration-center.md)

## Utiliser les outils d'opérations

- [Surveillance des ressources statiques](operations-platform/system-services/toolset/static-resource-monitoring.md)
- [Inspection des middlewares](operations-platform/system-services/toolset/middleware-inspection.md)
- [Capture de paquets du conteneur](operations-platform/system-services/toolset/container-packet-capture.md)
- [Tests de compatibilité](operations-platform/system-services/toolset/compatibility-testing.md)
- [Outils généraux](operations-platform/system-services/toolset/general-tools.md)

## Utiliser des outils intermédiaires

- [RDB Outils](operations-platform/system-services/middleware-tools/rdb.md)
- [Kafka Outils](operations-platform/system-services/middleware-tools/kafka.md)
- [gRPC Outils](operations-platform/system-services/middleware-tools/grpc.md)
- [Redis Outils](operations-platform/system-services/middleware-tools/redis.md)
- [MongoDB Outils](operations-platform/system-services/middleware-tools/mongodb.md)

## Configurer le panneau de contrôle

- [Canaux de notification](operations-platform/system-services/control-panel/notification-channels.md)
- [Paramètres avancés](operations-platform/system-services/control-panel/advanced-settings.md)

## Contrôler les opérations commerciales

- [Recherche d'événements de transcodage](operations-platform/system-services/business-control/transcoding-events.md)
- [Recherche d'informations sur les fichiers](operations-platform/system-services/business-control/file-information.md)
- [Blocage de collaboration](operations-platform/system-services/business-control/collaboration-blocking.md)
- [Réparation de documents](operations-platform/system-services/business-control/document-repair.md)

## Administrer la plateforme

- [Gestion des utilisateurs de la plateforme](operations-platform/system-services/system-management/user-management.md)
- [Journaux d'audit](operations-platform/system-services/system-management/audit-logs.md)

## Dépannage et maintenance

- [Dépannage d'installation](troubleshooting/installation.md)
- [Sauvegarde des données](troubleshooting/data-backup.md)
- [Référence des métriques de surveillance](troubleshooting/monitoring-metrics.md)
- [Incident de modification collaborative](troubleshooting/collaboration-editing-incident.md)
- [Réponse aux incidents SOP](troubleshooting/incident-response-sop.md)
