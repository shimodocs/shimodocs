# Référence des métriques de surveillance

[← ShimoDocs Suite documentation de déploiement](../README.md)

Ce document organise les métriques couramment utilisées dans le système de surveillance, couvrant les nœuds, les conteneurs containerd, Kubernetes clusters, middleware et services applicatifs, fournissant une référence unifiée pour les inspections quotidiennes, l'évaluation de la capacité et le dépannage. 

Les noms des métriques sont basés sur les métriques réelles de l'exportateur collectées dans Prometheus. Différentes versions de l'exportateur peuvent présenter de légères différences, et le dépannage réel doit se baser sur les résultats des requêtes en ligne comme référence finale. 

## Portée 

| Catégorie | Objets couverts | 
| --- | --- | 
| Surveillance des nœuds | Hôtes Linux, ressources système, disques, réseau, processus | 
| Surveillance des conteneurs | Conteneurs s'exécutant sur containerd, ressources des conteneurs Pod | 
| Kubernetes Cluster | Nœud, Pod, Déploiement, StatefulSet, Job, PVC, APIServer | 
| MySQL | MySQL instances, connexions, requêtes, cache, verrous, réseau | 
| MongoDB | MongoDB instances, connexions, opérations, mémoire, réseau, tampon de réplication | 
| Redis | Redis instances, clients, commandes, mémoire, Keyspace, taux de succès | 
| Kafka | Broker, Sujet, Partition, Groupe de consommateurs, Retard, Réplica | 
| MinIO | Nœuds du cluster, disques, Bucket, S3 requêtes, capacité de l'objet | 
| Elasticsearch | Santé du cluster, nœuds, fragments, indices, JVM, pools de threads, réseau |
| Services d'application | Serveur général, appels clients, édition collaborative, services RS, temps d'exécution |

## Règles de lecture de métriques

| Type de métrique | Méthode de lecture | Syntaxe PromQL courante | Description |
| --- | --- | --- | --- |
| Compteur | Regarder le taux de croissance ou l'incrément dans une fenêtre temporelle | `rate(x_total[5m])`, `increase(x_total[5m])` | Le nombre de requêtes, le nombre d'erreurs, le nombre d'octets, les temps d'E/S appartiennent généralement au compteur |
| Jauge | Regarder la valeur actuelle, la moyenne, le maximum | `avg(x)`, `max(x)`, `sum(x)` | Mémoire, nombre de connexions, capacité, valeurs d'état appartiennent généralement à la jauge |
| Histogramme | Regarder la latence percentile | `histogram_quantile(0.95, sum(rate(x_bucket[5m])) by (le))` | La latence des requêtes, la latence de traitement, la latence de la file d'attente utilisent généralement l'Histogramme |
| Ratio | Regardez le pourcentage | `A / B * 100` | L'utilisation, le taux d'erreur, le taux de réussite appartiennent tous aux métriques de type ratio |

Il est recommandé de ne pas copier directement des nombres fixes pour les seuils. Les métriques telles que CPU, la mémoire, le disque, le nombre de connexions, QPS, et le retard doivent être évalués dans le contexte du pic d'activité, de la planification de la capacité et de la ligne de base historique. Les comportements anormaux dans le document servent à identifier rapidement les risques et ne correspondent pas aux seuils d'alerte finaux.

## 1. Surveillance des services des nœuds

La surveillance des nœuds est utilisée pour déterminer si l'hôte est sain, si les ressources sont suffisantes et s'il existe des goulets d'étranglement au niveau du disque ou du réseau. Les métriques des nœuds proviennent principalement du node-exporter, combinées au tableau de bord des processus système pour une localisation au niveau des processus.

### 1.1 Statut de base

| Dimension de surveillance | Métrique | Signification de la métrique | Norme/Unité commune | Performance anormale |
| --- | --- | --- | --- | --- |
| Nœud actif | `up` | Si l'exporter ou la cible de collecte est accessible | `1` indique collectible, `0` indique non collectible | En continu `0` indique un problème avec le nœud, le réseau ou l'exporter |
| Temps de démarrage | `node_boot_time_seconds` | Dernier temps de démarrage du nœud | Horodatage Unix | Un changement dans le temps de démarrage indique que le nœud a été redémarré |
| Informations sur le nœud | `node_uname_info`, `node_os_info` | Système d'exploitation, noyau et informations sur la distribution | Informations sur les étiquettes | Utilisé pour vérifier les versions du nœud, pas utilisé directement comme métrique d'alerte |

Suggestion de dépannage : vérifiez `up` d'abord, puis `node_boot_time_seconds`. Si le nœud n'est pas collectible et que le temps de démarrage a récemment changé, accordez la priorité à la confirmation du redémarrage de l'hôte, du réseau ACL, et état du processus node-exporter.

### 1.2 CPU Métriques

| Dimension de surveillance | Métrique | Signification de la métrique | Norme/Unité commune | Performance anormale |
| --- | --- | --- | --- | --- |
| CPU Utilisation | `node_cpu_seconds_total` | Temps cumulé que chaque CPU cœur passe dans différents modes | Pourcentage | `user` et `system` reste élevé à long terme, la puissance de calcul du nœud est limitée |
| Inactif CPU | `node_cpu_seconds_total{mode="idle"}` | CPU temps inactif | Pourcentage | Le temps d'inactivité est constamment faible, ce qui peut entraîner des files d'attente et une latence accrue |
| Attente IO | `node_cpu_seconds_total{mode="iowait"}` | Temps CPU attente pour les entrées/sorties du disque | Pourcentage | L'augmentation continue de l'iowait indique généralement un disque ou un lien de stockage plus lent |
| Charge système | `node_load1`, `node_load5`, `node_load15` | Charge moyenne sur 1/5/15 minutes | Valeur de charge | Une charge constamment supérieure au nombre de CPU cœurs indique une file d'attente de tâches notable |
| CPU Pression | `node_pressure_cpu_waiting_seconds_total` | Cumulatif CPU PSI temps d'attente | Secondes/seconde | CPU la contention des ressources est importante, les processus attendent la CPU planification |

Requêtes courantes :

```promql
100 - avg by (instance) (rate(node_cpu_seconds_total{mode="idle"}[5m])) * 100
```

```promql
avg by (instance) (rate(node_cpu_seconds_total{mode="iowait"}[5m])) * 100
```

Suggestions d'investigation : Lorsque CPU L'utilisation est élevée, commencez par différencier entre `user`, `system`, et `iowait`. Élevé `user` est principalement dû à la pression de calcul des affaires, élevé `system` peut être lié aux appels système et au traitement des paquets réseau, et élevé `iowait` nécessite de vérifier le débit du disque, IOPS, et la latence.

### 1.3 Indicateurs de Mémoire

| Dimension de surveillance | Métrique | Signification de la métrique | Unité Courante | Manifestation Anormale |
| --- | --- | --- | --- | --- |
| Mémoire Totale | `node_memory_MemTotal_bytes` | Mémoire physique totale du nœud | Octets | Utilisé pour calculer le taux d'utilisation |
| Mémoire disponible | `node_memory_MemAvailable_bytes` | Mémoire que le système peut allouer aux processus | Octets / Pourcentage | Une mémoire disponible constamment faible est susceptible de déclencher OOM ou une récupération fréquente |
| Mémoire libre | `node_memory_MemFree_bytes` | Mémoire complètement inutilisée | Octets | Ne peut pas être utilisée seule sous Linux pour juger de la pression mémoire |
| Pression de la mémoire | `node_pressure_memory_waiting_seconds_total` | Mémoire accumulée PSI Temps d'attente | Secondes/Seconde | Augmentation de la récupération ou de l'attente d'allocation de mémoire |
| OOM Compte | `node_vmstat_oom_kill` | Nombre de systèmes OOM tués | Compte/Incrément | Lorsqu'il augmente, identifiez les processus tués et le pic de mémoire |

Requêtes courantes :

```promql
(1 - node_memory_MemAvailable_bytes / node_memory_MemTotal_bytes) * 100
```

```promql
increase(node_vmstat_oom_kill[10m])
```

Suggestion d'enquête : Ne regardez pas seulement `MemFree` pour la mémoire. La disponibilité réelle doit être évaluée davantage par `MemAvailable`, combinée à la mémoire du jeu de travail du conteneur, aux processus RSS, et OOM enregistrements.

### 1.4 Capacité du disque et inodes

| Dimension de surveillance | Métrique | Signification de la métrique | Mesure/Unité courante | Performance anormale |
| --- | --- | --- | --- | --- |
| Système de fichiers total | `node_filesystem_size_bytes` | Capacité totale du point de montage | Octets | Utilisé pour calculer le taux d'utilisation du disque |
| Système de fichiers disponible | `node_filesystem_avail_bytes` | Espace disponible pour les utilisateurs réguliers | Octets | Un espace disponible insuffisant peut provoquer des échecs d'écriture |
| Système de fichiers libre | `node_filesystem_free_bytes` | Espace libre dans le système de fichiers | Octets | Inclut l'espace réservé root ; généralement considéré avec `avail` |
| Statut en lecture seule | `node_filesystem_readonly` | Si le système de fichiers est en lecture seule | `0/1` | Quand `1`, les écritures professionnelles échoueront |
| Inodes totaux | `node_filesystem_files` | Nombre total d'inodes dans le système de fichiers | Compte | Nécessite une attention particulière dans les scénarios de petits fichiers |
| Inodes restants | `node_filesystem_files_free` | Nombre d'inodes restants | Compte/Pourcentage | Lorsque les inodes sont épuisés, les fichiers ne peuvent pas être créés même s'il reste de l'espace disque |

Requêtes courantes :

```promql
(1 - node_filesystem_avail_bytes{fstype!~"tmpfs|overlay"} / node_filesystem_size_bytes{fstype!~"tmpfs|overlay"}) * 100
```

```promql
(1 - node_filesystem_files_free / node_filesystem_files) * 100
```

Suggestions d'enquête : Les alertes de capacité du disque doivent être vérifiées par point de montage, notamment pour les disques de données, les disques de journaux et les répertoires de runtime de conteneur. Une utilisation élevée des inodes provient généralement d'un grand nombre de petits fichiers, de tranches de journaux ou de fichiers temporaires non nettoyés.

### 1.5 Disque IOPS, Débit et Latence

| Dimension de surveillance | Métrique | Signification de la métrique | Mesure/Unité courante | Performance anormale |
| --- | --- | --- | --- | --- |
| Lecture IOPS | `node_disk_reads_completed_total` | Nombre de requêtes de lecture disque terminées | fois/sec | Lecture IOPS proche de la limite du périphérique, la latence de lecture augmente |
| Écrire IOPS | `node_disk_writes_completed_total` | Nombre de requêtes d'écriture sur disque terminées | fois/sec | Retard d'écriture, ralentissement des commits du journal ou de la base de données |
| Débit en lecture | `node_disk_read_bytes_total` | Octets cumulés lus depuis le disque | Octets/s | Un débit élevé et un iowait élevé indiquent que le stockage est occupé |
| Débit en écriture | `node_disk_written_bytes_total` | Octets cumulés écrits sur le disque | Octets/s | Un débit d'écriture élevé à long terme peut affecter les bases de données et le stockage d'objets |
| Temps de lecture | `node_disk_read_time_seconds_total` | Temps cumulé pour les requêtes de lecture | sec/sec | La latence de lecture augmente |
| Temps d'écriture | `node_disk_write_time_seconds_total` | Temps cumulé des requêtes d'écriture | secondes/seconde | Latence d'écriture accrue |
| IO occupé | `node_disk_io_time_seconds_total` | Temps cumulé passé par le disque à traiter les IO | pourcentage | Lorsqu'il est proche de la charge maximale, les applications attendent les IO |
| Temps IO pondéré | `node_disk_io_time_weighted_seconds_total` | Temps IO en tenant compte de la longueur de la file d'attente | secondes/seconde | L'accumulation dans la file indique un enchaînement sévère des requêtes de l'appareil |
| Pression IO | `node_pressure_io_waiting_seconds_total` | IO cumulé PSI Temps d'attente | secondes/seconde | Les processus passent plus de temps à attendre les IO |

Requêtes courantes :

```promql
rate(node_disk_reads_completed_total[5m])
```

```promql
rate(node_disk_writes_completed_total[5m])
```

```promql
rate(node_disk_read_bytes_total[5m])
```

```promql
rate(node_disk_written_bytes_total[5m])
```

```promql
rate(node_disk_io_time_seconds_total[5m]) * 100
```

```promql
rate(node_disk_read_time_seconds_total[5m]) / rate(node_disk_reads_completed_total[5m])
```

```promql
rate(node_disk_write_time_seconds_total[5m]) / rate(node_disk_writes_completed_total[5m])
```

Suggestion d'investigation : Ne regardez pas seulement la capacité du disque lors de la vérification des problèmes. Même si la capacité est normale, la performance métier peut ralentir lorsque IOPS, le débit, l'IO occupé et l'iowait augmentent tous simultanément. Les services IO intensifs comme les bases de données, Kafka, et MinIO doivent se concentrer sur la latence d'écriture et les files d'attente.

### 1.6 Métriques réseau

| Dimension de surveillance | Métrique | Signification de la métrique | Unité Courante | Signes anormaux |
| --- | --- | --- | --- | --- |
| Trafic entrant | `node_network_receive_bytes_total` | Octets cumulés reçus par la carte réseau | Octets/s | Augmentation soudaine du trafic entrant, pouvant être due à des pics de requêtes ou à la synchronisation des données |
| Trafic sortant | `node_network_transmit_bytes_total` | Octets cumulés envoyés par la carte réseau | Octets/s | Augmentation soudaine du trafic sortant, pouvant être due à des téléchargements, des sauvegardes ou à la réplication |
| Erreurs entrantes | `node_network_receive_errs_total` | Nombre cumulé de paquets d'erreur reçus | Nombre/s | Problèmes de carte réseau, de liaison ou de pilote |
| Erreurs sortantes | `node_network_transmit_errs_total` | Nombre cumulé de paquets d'erreur envoyés | Nombre/s | Problèmes de liaison ou de file d'attente de carte réseau |
| Perte de paquets entrants | `node_network_receive_drop_total` | Nombre cumulé de paquets reçus perdus | Nombre/s | File d'attente du noyau ou carte réseau incapable de suivre |
| Perte de paquets sortants | `node_network_transmit_drop_total` | Valeur cumulée de la perte de paquets envoyés | fois/seconde | Congestion de sortie ou NIC pression de file d'attente |

Requêtes courantes :

```promql
rate(node_network_receive_bytes_total{device!~"lo|veth.*|cni.*"}[5m])
```

```promql
rate(node_network_transmit_bytes_total{device!~"lo|veth.*|cni.*"}[5m])
```

```promql
rate(node_network_receive_drop_total[5m]) + rate(node_network_transmit_drop_total[5m])
```

Suggestions d'investigation : Pour les anomalies réseau, examinez le trafic, les paquets d'erreur et la perte de paquets ensemble. Un trafic élevé seul n'indique pas nécessairement une panne ; un trafic élevé accompagné de paquets d'erreur ou de pertes de paquets est plus probablement lié à un problème de liaison ou de pile réseau de l'hôte.

### 1.7 TCP, des descripteurs de fichiers et du stress système

| Dimension de surveillance | Métrique | Signification de la métrique | Unité / Mesure commune | Comportement anormal |
| --- | --- | --- | --- | --- |
| Actuel TCP Connexions | `node_netstat_Tcp_CurrEstab` | Nombre actuel d'établies TCP connexions | compte | Une augmentation soudaine des connexions peut indiquer un pic de trafic ou une fuite de connexion |
| TIME_WAIT | `node_sockstat_TCP_tw` | Nombre de TIME_WAIT connexions | compte | Trop de connexions de courte durée peuvent épuiser les ports ou augmenter la pression sur le noyau |
| TCP Alloués | `node_sockstat_TCP_alloc` | Nombre de sockets alloués TCP sockets | compte | Une augmentation continue du nombre de sockets nécessite une enquête sur la libération des connexions |
| TCP En utilisation | `node_sockstat_TCP_inuse` | Nombre de TCP sockets en utilisation | compte | Augmentation de la pression sur les connexions |
| TCP Orphelin | `node_sockstat_TCP_orphan` | Nombre de sockets orphelins | compte | Une augmentation anormale peut être liée à une fermeture de connexion anormale |
| Descripteurs de fichiers utilisés | `node_filefd_allocated` | Nombre de descripteurs de fichiers alloués par le système | pcs | Trop élevé peut affecter les nouvelles connexions et l'ouverture de fichiers |
| Limite des descripteurs de fichiers | `node_filefd_maximum` | Limite système des descripteurs de fichiers | pcs | Utilisé pour calculer le taux d'utilisation des handles |

Requêtes courantes : 

```promql
node_filefd_allocated / node_filefd_maximum * 100
```

Recommandations pour l'enquête : Les handles de fichiers et les TCP connexions sont généralement considérés ensemble. Lorsque le nombre de connexions serveur augmente fortement, si les handles système sont proches de leur limite, l'application peut rencontrer des échecs d'acceptation, des échecs d'ouverture de fichiers ou des échecs dans les connexions dépendantes.

### 1.8 Surveillance des processus

| Dimension de surveillance | Métrique | Signification de la métrique | Mesure/Unité courante | Comportement anormal |
| --- | --- | --- | --- | --- |
| Processus CPU | `process_cpu_seconds_total` | Total CPU temps du processus | secondes/seconde | Utilisation élevée à long terme CPU par un seul processus |
| Mémoire physique | `process_resident_memory_bytes` | Processus RSS mémoire | Octets | Croissance continue de RSS peut indiquer une fuite de mémoire |
| Mémoire virtuelle | `process_virtual_memory_bytes` | Mémoire virtuelle du processus | Octets | Une croissance anormale doit être évaluée avec RSS |
| Handles ouverts | `process_open_fds` | Nombre de handles de fichiers ouverts du processus | compte | Une croissance continue peut indiquer une fuite de handles |
| Handles maximum | `process_max_fds` | Nombre maximum de handles de fichiers que le processus peut ouvrir | compte | Utilisé pour calculer le taux d'utilisation des handles du processus |
| Heure de démarrage du processus | `process_start_time_seconds` | Heure de démarrage du processus | Horodatage Unix | Les changements dans l'heure de démarrage indiquent un redémarrage du processus |

Recommandations pour l'enquête : Les métriques de processus sont utilisées pour identifier des services spécifiques pour des problèmes au niveau du nœud. Lorsque le CPU nœud est élevé, vérifiez le processus CPU; lorsque la pression mémoire du nœud est élevée, vérifiez RSS; lorsque les handles du nœud sont élevés, vérifiez `process_open_fds`. 

## 2. Surveillance de containerd

La surveillance des conteneurs provient principalement de kubelet/cAdvisor, reflétant l'utilisation des ressources des conteneurs gérés par containerd. Le document continue d'utiliser la `container_*` nomenclature des métriques Prometheus, mais le runtime sous-jacent du conteneur pendant l'exécution réelle est containerd. 

### 2.1 Conteneur CPU

| Dimension de surveillance | Métrique | Signification de la métrique | Portée/Unité courante | Performance anormale |
| --- | --- | --- | --- | --- |
| CPU Utilisation | `container_cpu_usage_seconds_total` | Total CPU temps d'utilisation du conteneur | cœurs/secondes | Taux d'utilisation proche de la limite pendant une longue période, augmentation possible de la latence commerciale |
| CPU Temps limité | `container_cpu_cfs_throttled_seconds_total` | Temps total CPU est limité par CFS | secondes/secondes | Significatif CPU Le throttling indique que la limite est trop stricte ou que la charge est trop élevée |
| CPU Quota | `container_spec_cpu_quota` | Conteneur CPU quota | valeur du quota | Utilisé pour identifier si un CPU limite est définie |

Requêtes courantes : 

```promql
sum by (namespace, pod, container) (rate(container_cpu_usage_seconds_total{container!="",image!=""}[5m]))
```

```promql
sum by (namespace, pod, container) (rate(container_cpu_cfs_throttled_seconds_total{container!="",image!=""}[5m]))
```

Recommandation d'enquête : Conteneur élevé CPU n'implique pas nécessairement un besoin de mise à l'échelle. Vérifiez d'abord s'il est limité, puis vérifiez si les demandes/limites du Pod sont trop faibles, et enfin, considérez la latence de la demande du service pour déterminer si elle affecte réellement l'entreprise.

### 2.2 Mémoire du Conteneur

| Dimension de surveillance | Métrique | Signification de la métrique | Unité Courante | Comportement anormal |
| --- | --- | --- | --- | --- |
| RSS Mémoire | `container_memory_rss` | Pages anonymes du conteneur et RSS mémoire | Octets | La croissance continue se rapproche de la pression réelle de la mémoire du processus |
| Mémoire utilisée | `container_memory_usage_bytes` | Utilisation totale de la mémoire du conteneur | Octets | Inclut le cache, ne permet pas de déterminer une fuite seule |
| Mémoire de Working Set | `container_memory_working_set_bytes` | Mémoire de working set active du conteneur | Octets | Approcher la limite peut provoquer un OOMKilled |
| Limite de mémoire | `container_spec_memory_limit_bytes` | Limite de mémoire du conteneur | Octets | Utilisé pour calculer le taux d'utilisation de la mémoire |

Requêtes courantes :

```promql
container_memory_working_set_bytes{container!="",image!=""} / container_spec_memory_limit_bytes{container!="",image!=""} * 100
```

Suggestion d'enquête : Pour les risques de mémoire dans les conteneurs professionnels, privilégiez l'observation du working set et RSS. `usage_bytes` est fortement affecté par le cache de pages, approprié pour l'observation de la capacité, mais pas adapté comme seul critère de OOM jugement.

### 2.3 Disque et stockage temporaire du conteneur

| Dimension de surveillance | Métrique | Signification de la métrique | Mesure/Unité courante | Performance anormale |
| --- | --- | --- | --- | --- |
| Débit en lecture | `container_fs_reads_bytes_total` | Octets cumulés lus par le conteneur depuis le disque | Octets/s | Un pic soudain du trafic de lecture peut indiquer un scan, un import ou une récupération de cache d'origine |
| Débit en écriture | `container_fs_writes_bytes_total` | Octets cumulés écrits par le conteneur sur le disque | Octets/s | Les pics d'écriture peuvent provoquer une pression IO sur le nœud |
| Lecture IOPS | `container_fs_reads_total` | Nombre de requêtes de lecture par le conteneur | Ops/s | Une forte fréquence de lectures de petits blocs peut augmenter l'attente d'E/S |
| Écrire IOPS | `container_fs_writes_total` | Nombre de requêtes d'écriture du conteneur | Ops/s | Écriture excessive de journaux ou de fichiers temporaires |
| Utilisation du système de fichiers | `container_fs_usage_bytes` | Utilisation du système de fichiers du conteneur | Octets | Accumulation de fichiers temporaires ou de journaux |
| Limite du système de fichiers | `container_fs_limit_bytes` | Limite du système de fichiers du conteneur | Octets | Les écritures peuvent échouer lorsque la limite est approchée |

Requêtes courantes : 

```promql
sum by (namespace, pod, container) (rate(container_fs_reads_bytes_total{container!="",image!=""}[5m]))
```

```promql
sum by (namespace, pod, container) (rate(container_fs_writes_bytes_total{container!="",image!=""}[5m]))
```

Suggestion d'enquête : lorsque l'écriture sur disque du conteneur est anormale, vérifiez d'abord le volume de journaux du Pod, le répertoire des fichiers temporaires et les tâches par lots. Lorsque l'E/S du disque du nœud est élevée, les métriques FS du conteneur peuvent être utilisées pour localiser quel Pod écrit.

### 2.4 Réseau du conteneur

| Dimension de surveillance | Métrique | Signification de la métrique | Portée/Unité courante | Performance anormale |
| --- | --- | --- | --- | --- |
| Trafic entrant | `container_network_receive_bytes_total` | Nombre total d'octets reçus par le conteneur | Octets/s | Augmentation soudaine du trafic des requêtes ou du trafic de réplication |
| Trafic sortant | `container_network_transmit_bytes_total` | Nombre total d'octets envoyés par le conteneur | Octets/s | Augmentation du trafic de téléchargement, de synchronisation, de récupération d'origine ou d'exportation |
| Perte de paquets entrante | `container_network_receive_packets_dropped_total` | Nombre total de paquets perdus à la réception par le conteneur | fois/s | Perte de paquets causée par la pile réseau ou la pression du nœud |
| Perte de paquets sortante | `container_network_transmit_packets_dropped_total` | Nombre total de paquets perdus à l'envoi par le conteneur | fois/s | Congestion de sortie, NIC file d'attente, ou CNI problèmes |

Requêtes courantes :

```promql
sum by (namespace, pod) (rate(container_network_receive_bytes_total[5m]))
```

```promql
sum by (namespace, pod) (rate(container_network_transmit_bytes_total[5m]))
```

Suggestions d'investigation : le réseau des conteneurs doit être analysé en conjonction avec les métriques du nœud. NIC Si la perte de paquets augmente au niveau du Pod mais qu'il n'y a pas d'anomalie au niveau du nœud, continuez à vérifier CNI, iptables et la charge sur le nœud où le Pod se trouve. 

### 2,5 Threads et cycle de vie du conteneur

| Dimension de surveillance | Métrique | Signification de la métrique | Portée/Unité courante | Comportement anormal |
| --- | --- | --- | --- | --- |
| Nombre de threads | `container_threads` | Nombre de threads à l'intérieur du conteneur | compte | Une croissance continue des threads peut indiquer une fuite de threads |
| Dernière vue | `container_last_seen` | La dernière fois que le conteneur a été vu par cAdvisor | Horodatage Unix | Aucune mise à jour depuis longtemps peut indiquer que le conteneur s'est arrêté ou qu'il y a une anomalie de collecte |
| Nombre de redémarrages | `kube_pod_container_status_restarts_total` | Nombre total de redémarrages du conteneur | compte/incrément | Des redémarrages fréquents indiquent un crash, un échec de sondage, ou OOM |
| Raison d'attente | `kube_pod_container_status_waiting_reason` | Raison pour laquelle le conteneur est en état d'attente | valeur de l'étiquette | `CrashLoopBackOff`, `ImagePullBackOff`, etc., doivent être traités |
| État d'exécution | `kube_pod_container_status_running` | Si le conteneur est en cours d'exécution | `0/1` | Conteneur clé non `1` indique que le service n'est pas disponible |

Recommandations d'enquête : Pour les anomalies de conteneurs, vérifiez d'abord la raison de l'état, puis regardez le nombre de redémarrages et l'heure du redémarrage le plus récent. Si les redémarrages sont fréquents, continuez l'enquête en utilisant les journaux d'application, OOM événements et configurations de sondes. 

## 3. Kubernetes Surveillance de cluster

Kubernetes La surveillance est utilisée pour évaluer l'utilisation des ressources du cluster, la santé du plan de contrôle, l'état des répliques de charge de travail et l'état des objets de stockage. Les principales métriques proviennent de kube-state-metrics, kubelet et APIServer. 

### 3.1 Capacité du nœud et ressources planifiables

| Dimension de surveillance | Métrique | Signification de la métrique | Portée/Unité courante | Manifestation Anormale |
| --- | --- | --- | --- | --- |
| Capacité du nœud | `kube_node_status_capacity` | Capacité totale du nœud | CPU, mémoire, nombre de Pods, etc. | Utilisé pour la planification de capacité |
| Ressources allouables | `kube_node_status_allocatable` | Ressources planifiables du nœud | CPU, mémoire, nombre de Pods, etc. | Des ressources planifiables insuffisantes provoqueront des Pods en attente |
| Conditions du nœud | `kube_node_status_condition` | Nœud prêt, MemoryPressure et autres états | `0/1` | Un état Prêt anormal ou l'apparition de Pressure nécessite une attention immédiate |
| Planification interdite | `kube_node_spec_unschedulable` | Le nœud est-il cordonné | `0/1` | Lorsqu'il est réglé sur '1', le nœud ne planifie pas de nouveau Pod |
| Informations sur le nœud | `kube_node_info` | Version du nœud, informations sur le noyau et le runtime du conteneur | Informations sur les balises | Utilisé pour résoudre les différences de version |

Suggestion de dépannage : lorsque le pod est en attente, vérifiez d'abord les ressources attribuables et les demandes, puis vérifiez si le nœud est 'non planifiable', et enfin vérifiez si l'état du nœud subit une pression sur les ressources. 

### 3.2 Statut du Pod 

| Dimensions de surveillance | Indicateurs | Significations des indicateurs | Ouvertures/Unités courantes | Comportement anormal |
| --- | --- | --- | --- | --- |
| Informations sur le Pod | `kube_pod_info` | Informations sur le namespace du Pod, le nœud, etc. | Informations sur les balises | Utilisé pour localiser la distribution des Pods |
| Étape du Pod | `kube_pod_status_phase` | Statut en attente, en cours, réussi, échoué, etc. | `0/1` | L'augmentation des pods en attente/échoués indique des anomalies de planification ou d'exécution |
| Pod Prêt | `kube_pod_status_ready` | Le Pod est-il prêt | `0/1` | Le statut non prêt affecte la disponibilité du service |
| Raison du Pod | `kube_pod_status_reason` | Raison de l'anomalie du Pod | Valeur de l'étiquette | Évincé, NœudPerdu, etc. doivent être investigués |
| Redémarrages du conteneur | `kube_pod_container_status_restarts_total` | Nombre de redémarrages du conteneur | fois/incrément | La croissance des redémarrages indique des problèmes de stabilité |
| Conteneur en attente | `kube_pod_container_status_waiting` | Le conteneur est-il en état d'attente | `0/1` | Si l'état d'attente persiste, le Pod ne peut pas fournir le service normalement |
| Raison d'attente | `kube_pod_container_status_waiting_reason` | Raison de l'état d'attente | Valeur de l'étiquette | Échec du téléchargement de l'image, CrashLoop, etc. |
| Conteneur terminé | `kube_pod_container_status_terminated` | Le conteneur est-il terminé | `0/1` | La terminaison inattendue doit être vérifiée avec les redémarrages et les journaux |

Requêtes courantes :

```promql
sum by (namespace, phase) (kube_pod_status_phase == 1)
```

```promql
increase(kube_pod_container_status_restarts_total[10m])
```

Suggestion d'investigation : ne regardez pas seulement la phase du pod lorsqu'il y a une anomalie. Le statut Prêt, la raison et la raison de l'attente du conteneur illustrent mieux le problème spécifique.

### 3.3 Demandes et limites des ressources

| Dimension de surveillance | Métrique | Signification de la métrique | Mesure/Unité courante | Comportement anormal |
| --- | --- | --- | --- | --- |
| Ressources demandées | `kube_pod_container_resource_requests` | Demandes du conteneur | CPU, Mémoire | Des demandes trop élevées affectent la planification, trop faibles affectent la stabilité |
| Limites de ressources | `kube_pod_container_resource_limits` | Limites des conteneurs | CPU, Mémoire | Des limites trop basses peuvent provoquer CPU du bridage ou OOM |
| Capacité allouable du nœud | `kube_node_status_allocatable` | Ressources disponibles pour la planification sur un nœud | CPU, Mémoire | Utilisé pour calculer le taux d'allocation des ressources du cluster |
| Utilisation des conteneurs | `container_cpu_usage_seconds_total`, `container_memory_working_set_bytes` | Réelle CPU et utilisation de la mémoire | Cœurs/secondes, Octets | Utilisé pour déterminer si les demandes/limites sont raisonnables |

Requêtes courantes :

```promql
sum(kube_pod_container_resource_requests{resource="cpu"}) / sum(kube_node_status_allocatable{resource="cpu"}) * 100
```

```promql
sum(kube_pod_container_resource_requests{resource="memory"}) / sum(kube_node_status_allocatable{resource="memory"}) * 100
```

Suggestion d'enquête : La planification des ressources devrait prendre en compte à la fois la 'valeur demandée' et la 'valeur d'utilisation réelle'. Se fier uniquement aux demandes peut mal juger la pression sur l'entreprise, tandis que se fier uniquement à l'utilisation peut négliger la capacité de planification.

### 3.4 Réplicas de charge de travail

| Dimension de surveillance | Métrique | Signification de la métrique | Portée/Unité courante | Performance anormale |
| --- | --- | --- | --- | --- |
| Réplicas de déploiement | `kube_deployment_status_replicas` | Nombre actuel de réplicas de déploiement | unités | Incohérent avec les réplicas attendus |
| Réplicas mis à jour | `kube_deployment_status_replicas_updated` | Nombre de réplicas mis à jour vers la nouvelle version | unités | Aucune croissance pendant une longue période lors de la diffusion |
| Réplicas indisponibles | `kube_deployment_status_replicas_unavailable` | Nombre de réplicas indisponibles | unités | La capacité du service diminue lorsqu'elle est supérieure à 0 |
| Réplicas StatefulSet | `kube_statefulset_status_replicas` | Nombre actuel de réplicas StatefulSet | unités | Réplicas anormaux dans les services stateful |
| StatefulSet Prêt | `kube_statefulset_status_replicas_ready` | Nombre de réplicas StatefulSet prêts | unités | Si le nombre de prêts est inférieur aux réplicas attendus, le service est incomplet |

Recommandations pour l'enquête : En cas d'anomalie lors d'une diffusion, vérifier `updated` et `unavailable`. Pour les anomalies de StatefulSet, faire attention à PVC, l'ordre de démarrage des Pods et l'affinité des nœuds.

### 3.5 Jobs et tâches par lots

| Dimension de surveillance | Métrique | Signification de la métrique | Norme/Unité commune | Performance anormale |
| --- | --- | --- | --- | --- |
| Jobs en cours | `kube_job_status_active` | Nombre de jobs actuellement actifs | Compte | Une activité à long terme peut indiquer un job bloqué |
| Jobs échoués | `kube_job_status_failed` | Nombre d'échecs de tâches | Compte | Une augmentation des échecs nécessite de vérifier les journaux des tâches |
| Tâches réussies | `kube_job_status_succeeded` | Nombre de tâches exécutées avec succès | Compte | Utilisé pour déterminer l'achèvement des tâches |
| Temps d'achèvement | `kube_job_status_completion_time` | Temps d'achèvement de la tâche | Horodatage Unix | L'absence de temps d'achèvement peut indiquer des tâches incomplètes |

Recommandations d'enquête : Lorsque les tâches par lot présentent des anomalies, vérifiez `active`, `failed`, et `succeeded` ensemble. Ne regarder que les échecs peut faire passer inaperçu les tâches bloquées longtemps.

### 3.6 PVC et objets de stockage

| Dimension de surveillance | Métrique | Signification de la métrique | Norme/Unité commune | Performance anormale |
| --- | --- | --- | --- | --- |
| PVC Statut | `kube_persistentvolumeclaim_status_phase` | PVC Statuts Bound, Pending et autres | `0/1` | Pending empêchera le Pod de monter le stockage |
| PVC Capacité demandée | `kube_persistentvolumeclaim_resource_requests_storage_bytes` | La capacité de stockage demandée par le PVC | Octets | Utilisé pour la planification de la capacité et la gestion des quotas |

Suggestion de dépannage : Lorsque un service étatful échoue à démarrer, en plus de vérifier le Pod, vous devez également vérifier si le PVC est Bound, si la classe de stockage est disponible et si le stockage sous-jacent a une capacité insuffisante.

### 3,7 APIServer, etcd et plan de contrôle

| Dimension de surveillance | Métrique | Signification de la métrique | Calibre/Unité commun | Performance anormale |
| --- | --- | --- | --- | --- |
| Nombre de requêtes APIServer | `apiserver_request_total` | Nombre cumulatif de requêtes APIServer | requêtes/sec | Des pics soudains de requêtes peuvent provenir des contrôleurs, kubectl, ou des composants métier |
| Latence APIServer | `apiserver_request_duration_seconds_bucket` | Plages de durée des requêtes APIServer | P50/P95/P99 | Une latence accrue affectera la planification, le déploiement et la synchronisation des contrôleurs |
| Latence etcd | `etcd_request_duration_seconds_bucket` | Plages de durée des requêtes etcd | P50/P95/P99 | Un etcd lent peut ralentir l'ensemble du plan de contrôle |
| Attente dans la file | `workqueue_queue_duration_seconds_bucket` | Durée d'attente de la file du contrôleur | Durée en percentile | Arriéré dans la file, la synchronisation de l'état des ressources ralentit |
| Traitement de la file | `workqueue_work_duration_seconds_bucket` | Durée de traitement du contrôleur | Durée en percentile | Le traitement du contrôleur ralentit |

Requêtes courantes :

```promql
sum by (verb, resource) (rate(apiserver_request_total[5m]))
```

```promql
histogram_quantile(0.95, sum(rate(apiserver_request_duration_seconds_bucket[5m])) by (le, verb, resource))
```

Recommandations d'enquête : Les problèmes du plan de contrôle se manifestent généralement par un déploiement lent, des mises à jour de statut des Pods lentes et des kubectl réponses lentes. Lorsque la latence de l'APIServer et celle d'etcd augmentent simultanément, il faut donner la priorité à la vérification de etcd, de l'IO disque et de la charge du nœud du plan de contrôle.

## 4. MySQL Surveillance

MySQL La surveillance est utilisée pour observer la disponibilité des instances, la pression de connexion, SQL le volume des requêtes, les requêtes lentes, les hits du cache, les tables temporaires, les attentes de verrou, les descripteurs de fichiers et le débit réseau.

### 4.1 État de l'instance et volume des requêtes

| Dimension de surveillance | Métrique | Signification de la métrique | Portée/Unité courante | Manifestation Anormale |
| --- | --- | --- | --- | --- |
| Instance vivante | `up` | Que le collecteur mysql puisse être collecté | `0/1` | Quand `0`, instance, réseau ou collecteur est anormal |
| Temps de disponibilité | `mysql_global_status_uptime` | MySQL Durée de fonctionnement | Secondes | La diminution indique un redémarrage de l'instance |
| Total des requêtes | `mysql_global_status_queries` | Nombre cumulé de requêtes | fois/seconde | QPS Un pic peut indiquer un pic d'activité ou des requêtes anormales |
| Questions | `mysql_global_status_questions` | Nombre cumulatif de déclarations initiées par les clients | fois/seconde | À visualiser avec les requêtes pour évaluer la pression des demandes |
| Statistiques des commandes | `mysql_global_status_commands_total` | Compte cumulatif des différentes commandes | fois/seconde | Peut distinguer des commandes telles que select, insert, update, delete |

Requêtes courantes : 

```promql
rate(mysql_global_status_queries[5m])
```

```promql
sum by (command) (rate(mysql_global_status_commands_total[5m]))
```

Suggestions d'investigation : Quand QPS augmente, vérifiez d'abord la répartition des commandes. Si `select` augmente en même temps que les indicateurs de type scan, faites attention aux index et aux requêtes lentes ; si les commandes d'écriture augmentent, continuez à surveiller les attentes de verrouillage, l'I/O disque et la latence d'écriture de l'hôte.

### 4.2 Connexions et Threads

| Dimension de surveillance | Métrique | Signification de la métrique | Calibre/Unité commun | Performance anormale |
| --- | --- | --- | --- | --- |
| Connexions Actuelles | `mysql_global_status_threads_connected` | Nombre de threads actuellement connectés | compte | S'approcher de la limite peut provoquer l'échec de nouvelles connexions |
| Threads Actifs | `mysql_global_status_threads_running` | Nombre de threads actuellement en exécution | compte | Une augmentation continue indique généralement une exécution lente ou une attente de verrou SQL Exécution lente ou attente de verrou |
| Nombre maximum de connexions historiques | `mysql_global_status_max_used_connections` | Nombre maximum historique de connexions utilisées | compte | Approche du maximum_Le nombre de connexions indique que le pool de connexions nécessite une évaluation |
| Connexions maximales | `mysql_global_variables_max_connections` | MySQL Configuration maximale des connexions | compte | Utilisé pour calculer le taux d'utilisation des connexions |
| Clients anormaux | `mysql_global_status_aborted_clients` | Nombre cumulatif de déconnexions anormales des clients | fois/sec | Problèmes réseau, délais d'attente ou exceptions côté client |
| Connexion échouée | `mysql_global_status_aborted_connects` | Nombre total d'échecs de connexion | fois/seconde | Erreurs d'authentification, limite de connexion, anomalies réseau, etc. |

Requêtes courantes :

```promql
mysql_global_status_threads_connected / mysql_global_variables_max_connections * 100
```

Suggestions d'investigation : un nombre élevé de connexions ne signifie pas nécessairement que la base de données est lente ; cela peut également être dû à un pool de connexions d'application mal configuré. `Threads_running` être élevé pendant une longue période est plus préoccupant, car cela correspond généralement à SQL des problèmes d'exécution ou d'attente de verrou.

### 4.3 Requêtes lentes, scans et tri

| Dimension de surveillance | Métrique | Signification de la métrique | Mesure/Unité courante | Comportement anormal |
| --- | --- | --- | --- | --- |
| Requêtes lentes | `mysql_global_status_slow_queries` | Nombre cumulatif de requêtes lentes | fois/sec | Une augmentation indique plus de lenteur SQL |
| Scans de jointures complètes | `mysql_global_status_select_full_join` | Nombre de jointures sans index | fois/sec | Indique que des index de conditions de jointure peuvent manquer |
| Analyses complètes de tables | `mysql_global_status_select_scan` | Nombre d'analyses complètes de tables | fois/sec | Les analyses complètes sur de grandes tables peuvent ralentir l'instance |
| Fusion de tri | `mysql_global_status_sort_merge_passes` | Nombre de fois où le tri nécessite plusieurs fusions | fois/sec | Tampon de tri insuffisant ou trop de données à trier |

Suggestions d'investigation : lorsque le nombre de requêtes lentes augmente, vérifiez par rapport aux périodes de publication commerciale et aux enregistrements de modifications. SQL Si les métriques de scan et de tri augmentent, reportez-vous généralement aux journaux lents, aux plans d'exécution et à la conception des index.

### 4.4 Pool de Tampon InnoDB

| Dimension de surveillance | Métrique | Signification de la métrique | Calibre/Unité commun | Performance anormale |
| --- | --- | --- | --- | --- |
| Taille du Pool de Tampon | `mysql_global_variables_innodb_buffer_pool_size` | Taille de Configuration du Pool de Tampon InnoDB | Octets | Trop petit entraînera une augmentation des lectures sur disque |
| Pages du Pool de Tampon | `mysql_global_status_buffer_pool_pages` | Nombre de différents types de pages du Pool de Tampon | Pages | Utilisé pour surveiller les pages sales, libres, de données et autres pages |
| Taille de Page | `mysql_global_status_innodb_page_size` | Taille de page InnoDB | Octets | Utilisé pour convertir le nombre de pages en capacité |

Suggestion d'investigation : Lorsque le taux de réussite du Pool de Tampon est faible, la base de données accédera davantage au disque. Il est nécessaire de l'évaluer conjointement avec le débit de lecture du disque du nœud, les lectures IOPS, et l'iowait.

### 4.5 Tables Temporaires, Caches de Tables et Descripteurs de Fichiers

| Dimension de surveillance | Métrique | Signification de la métrique | Calibre/Unité commun | Performance anormale |
| --- | --- | --- | --- | --- |
| Table Temporaire | `mysql_global_status_created_tmp_tables` | Nombre Total de Tables Temporaires Créées | fois/seconde | Augmentation de la Complexité des Requêtes |
| Tables Temporaires sur Disque | `mysql_global_status_created_tmp_disk_tables` | Nombre total de tables temporaires sur disque créées | fois/sec | Augmentation de la pression d'E/S sur le disque, SQL peut ralentir |
| Fichiers temporaires | `mysql_global_status_created_tmp_files` | Nombre total de fichiers temporaires créés | fois/sec | Augmentation des fichiers temporaires |
| Verrous de table immédiats | `mysql_global_status_table_locks_immediate` | Nombre de fois où des verrous de table ont été acquis immédiatement | fois/sec | Métrique de référence normale |
| Verrous de table en attente | `mysql_global_status_table_locks_waited` | Nombre de fois où des verrous de table ont été attendus | fois/sec | Augmentation de la contention des verrous |
| Succès de cache de table | `mysql_global_status_table_open_cache_hits` | Nombre de succès dans le cache d'ouverture de table | fois/sec | Peu de succès peut indiquer des ouvertures fréquentes de tables |
| Échecs de cache de table | `mysql_global_status_table_open_cache_misses` | Nombre d'échecs dans le cache d'ouverture de table | fois/sec | Évaluation du cache de table nécessaire |
| Débordements de cache de table | `mysql_global_status_table_open_cache_overflows` | Nombre de débordements dans le cache d'ouverture de table | fois/sec | Configuration insuffisante ou trop de tables |
| Tables ouvertes | `mysql_global_status_open_tables` | Nombre actuel de tables ouvertes | pcs | Le risque augmente à l'approche de la limite de cache |
| Configuration du cache de table | `mysql_global_variables_table_open_cache` | table_ouvert_valeur configurée du cache | pcs | Utilisé pour calculer le taux d'utilisation |
| Fichiers ouverts | `mysql_global_status_open_files` | Nombre actuel de fichiers ouverts | pcs | Peut affecter SQL l'exécution lorsqu'on approche de la limite de fichiers |
| Limite de fichiers | `mysql_global_variables_open_files_limit` | MySQL limite des descripteurs de fichiers | pcs | Utilisé pour calculer le taux d'utilisation des descripteurs de fichiers |

Suggestions de dépannage : Les tables temporaires, les attentes de verrou et les échecs de cache de table apparaissent souvent avec des requêtes lentes. Lorsque les tables temporaires sur disque augmentent, faites attention à l'E/S d'écriture du nœud, à la latence du disque et SQL au tri/regroupement.

### 4.6 Débit réseau

| Dimension de surveillance | Métrique | Signification de la métrique | Unité Courante | Performance anormale |
| --- | --- | --- | --- | --- |
| Trafic entrant | `mysql_global_status_bytes_received` | Cumulatif MySQL octets reçus | Octets/s | Augmentation du corps de la requête ou du trafic d'écriture |
| Trafic sortant | `mysql_global_status_bytes_sent` | Octets cumulés envoyés par MySQL | Octets/s | Les grandes requêtes, les balayages complets de table et les exportations massives augmenteront le trafic sortant |

Requêtes courantes :

```promql
rate(mysql_global_status_bytes_received[5m])
```

```promql
rate(mysql_global_status_bytes_sent[5m])
```

Suggestions d'investigation : Quand MySQL Lorsque le trafic sortant augmente soudainement, il faut généralement prêter attention aux grands ensembles de résultats, aux tâches d'exportation et aux requêtes sans pagination.

## 5. MongoDB Surveillance

MongoDB La surveillance est utilisée pour observer l'état de l'instance, le nombre de connexions, le volume des opérations, le balayage des requêtes, l'utilisation de la mémoire, le débit réseau et l'état du tampon de réplication.

### 5.1 Instances et connexions

| Dimension de surveillance | Métrique | Signification de la métrique | Mesure/Unité courante | Performance anormale |
| --- | --- | --- | --- | --- |
| Instance vivante | `up` | Si mongo exporter peut collecter des données | `0/1` | Si `0`, l'instance ou l'exportateur est anormal |
| Temps de disponibilité | `mongodb_ss_uptime` | MongoDB Durée de fonctionnement | Secondes | Des valeurs plus petites indiquent un redémarrage de l'instance |
| Nombre de connexions | `mongodb_ss_connections` | Statistiques actuelles liées aux connexions | compte | Un nombre de connexions anormalement élevé peut indiquer un pool de connexions ou un pic d'activité |

Suggestions d'investigation : Lorsque le nombre de connexions augmente, confirmez d'abord s'il y a un pic d'activité, des modifications de la configuration du pool de connexions ou des reconnexions anormales de clients.

### 5.2 Opérations et gestion des documents

| Dimension de surveillance | Métrique | Signification de la métrique | Mesure/Unité courante | Performance anormale |
| --- | --- | --- | --- | --- |
| Nombre d'opérations | `mongodb_ss_opcounters` | Le nombre cumulatif d'opérations telles que l'insertion, la requête, la mise à jour, la suppression | fois/seconde | Une augmentation soudaine d'un certain type d'opération indique un changement dans les modèles d'accès aux affaires |
| Gestion des documents | `mongodb_ss_metrics_document` | Nombre cumulatif de documents insérés, mis à jour, supprimés, retournés, etc. | fois/seconde | Si le nombre de retours est significativement plus élevé que ce qui est réellement nécessaire, l'ensemble des résultats peut être trop grand |
| Entrées d'index scannées | `mongodb_ss_metrics_queryExecutor_scanned` | Nombre d'entrées d'index scannées lors des requêtes | fois/seconde | Un scan excessif peut indiquer un indexage incorrect |
| Documents scannés | `mongodb_ss_metrics_queryExecutor_scannedObjects` | Nombre de documents scannés lors des requêtes | fois/seconde | Un scan élevé de documents indique une faible efficacité des requêtes |

Requêtes courantes : 

```promql
sum by (type) (rate(mongodb_ss_opcounters[5m]))
```

Recommandations d'enquête : Une manifestation courante des MongoDB requêtes lentes est une augmentation des scanned/scannedObjects. Il est nécessaire d'analyser en combinaison avec les journaux lents et les hits d'index.

### 5.3 Mémoire, Réseau et Disque

| Dimension de surveillance | Métrique | Signification de la métrique | Unité/mesure courante | Manifestation Anormale |
| --- | --- | --- | --- | --- |
| Mémoire résidente | `mongodb_ss_mem_resident` | MongoDB mémoire résidente | Mo ou Octets | Une augmentation continue nécessite de vérifier la mémoire de l'hôte |
| Mémoire virtuelle | `mongodb_ss_mem_virtual` | MongoDB mémoire virtuelle | Mo ou Octets | Une augmentation seule n'indique pas nécessairement une pression réelle |
| Trafic entrant | `mongodb_ss_network_bytesIn` | MongoDB octets reçus cumulés | Octets/s | Augmentation du trafic d'écriture ou des requêtes |
| Trafic sortant | `mongodb_ss_network_bytesOut` | MongoDB octets envoyés cumulés | Octets/s | Requêtes importantes ou tâches d'exportation provoquant une augmentation du trafic sortant |
| E/S de lecture de l'hôte | `node_disk_reads_completed_total` | Lecture IOPS sur le nœud où MongoDB réside | fois/s | Les scans de requêtes provoquent une augmentation des E/S de lecture |
| E/S d'écriture de l'hôte | `node_disk_writes_completed_total` | Écrire IOPS sur le nœud où MongoDB est situé | fois/sec | Augmentation de l'écriture ou pression sur le journal | 

Suggestion de dépannage : MongoDB la performance de la mémoire et du disque doit être considérée avec la mémoire du nœud et ses E/S disque. Visualiser les métriques de l'instance en parallèle avec les lectures/écritures disque de l'hôte facilite la détermination de savoir si MongoDB elle-même est lente ou si les ressources sous-jacentes sont lentes. 

### 5.4 Tampon de Réplication 

| Dimension de surveillance | Métrique | Signification de la métrique | Mesure/Unité courante | Performance anormale | 
| --- | --- | --- | --- | 
| Taille du Tampon de Réplication | `mongodb_ss_metrics_repl_buffer_sizeBytes` | Taille du tampon de réplication | Octets | Une croissance continue du tampon indique que la consommation de la réplication n'est pas en temps voulu | 

Suggestion de dépannage : Les tampons de réplication anormaux sont généralement liés à la capacité de traitement de l'esclave, au réseau ou aux écritures sur disque. Ils doivent être analysés conjointement avec le retard de réplication, le réseau du nœud et les métriques d'écriture sur disque. 

## 6. Redis Surveillance 

Redis La surveillance est utilisée pour observer la disponibilité de l'instance, le nombre de connexions, le traitement des commandes, les niveaux de mémoire, l'espace de clés, le taux de réussite (hit rate), l'éviction et le débit du réseau. 

### 6.1 Instance et Clients 

| Dimension de surveillance | Métrique | Signification de la métrique | Mesure/Unité courante | Performance anormale | 
| --- | --- | --- | --- | --- |
| Instance vivante | `up` | Si Redis L'exportateur peut être collecté | `0/1` | Quand `0`, l'instance ou l'exportateur est anormal |
| Temps de disponibilité | `redis_uptime_in_seconds` | Redis Durée de fonctionnement | Secondes | Une diminution indique un redémarrage de l'instance |
| Connexions Clients | `redis_connected_clients` | Nombre actuel de connexions clients | compte | Une augmentation soudaine peut indiquer un problème de pool de connexions ou une tempête de reconnexions |

### 6.2 Commandes, Mémoire et Espace de Clés

| Dimension de surveillance | Métrique | Signification de la métrique | Unité Courante | Comportement anormal |
| --- | --- | --- | --- | --- |
| Commandes Traitées | `redis_commands_processed_total` | Nombre total de Redis commandes traitées | fois/seconde | Une augmentation soudaine QPS peut accroître l'instance CPU |
| Classification des commandes | `redis_commands_total` | Nombre total de commandes par type | fois/seconde | Peut identifier les changements dans les commandes get, set, del, etc. |
| Mémoire utilisée | `redis_memory_used_bytes` | Actuel Redis utilisation de la mémoire | Octets | Approcher la mémoire maximale peut déclencher l'éviction |
| Mémoire maximale | `redis_memory_max_bytes` | Redis configuration maxmemory | Octets | Utilisé pour calculer le taux d'utilisation de la mémoire |
| Nombre de clés | `redis_db_keys` | Nombre de clés dans chaque base de données | compte | Une croissance anormale des clés peut indiquer un cache sans expiration ou des anomalies d'écriture |
| Clés expirantes | `redis_db_keys_expiring` | Nombre de clés avec expiration définie | compte | Une proportion faible nécessite une attention au cycle de vie du cache |

Requêtes courantes :

```promql
rate(redis_commands_processed_total[5m])
```

```promql
redis_memory_used_bytes / redis_memory_max_bytes * 100
```

### 6.3 Taux de réussite, Éviction et Réseau

| Dimension de surveillance | Métrique | Signification de la métrique | Calibre/Unité commun | Performance anormale |
| --- | --- | --- | --- | --- |
| Nombre de réussites | `redis_keyspace_hits_total` | Nombre total de réussites de clés | fois/s | Calculer le taux de réussite avec les échecs |
| Nombre d'échecs | `redis_keyspace_misses_total` | Nombre total de clés manquées | fois/s | Une augmentation des pertes peut entraîner une pression accrue vers la source |
| Clés expirées | `redis_expired_keys_total` | Nombre total de clés expirées | fois/s | Les tempêtes d'expiration peuvent provoquer CPU des variations |
| Clés expulsées | `redis_evicted_keys_total` | Nombre total de clés expulsées | fois/s | La croissance indique une pression sur la mémoire ou un maxmemory insuffisant |
| Trafic entrant | `redis_net_input_bytes_total` | Nombre total d'octets reçus par Redis | Octets/s | Augmentation du trafic d'écriture ou des requêtes |
| Trafic sortant | `redis_net_output_bytes_total` | Nombre total d'octets envoyés par Redis | Octets/s | Trafic sortant élevé causé par de grandes valeurs ou des lectures par lots |

Requêtes courantes :

```promql
rate(redis_keyspace_hits_total[5m]) / (rate(redis_keyspace_hits_total[5m]) + rate(redis_keyspace_misses_total[5m])) * 100
```

```promql
rate(redis_evicted_keys_total[5m])
```

Recommandation d'enquête : Pour Redis, concentrez-vous sur les risques de mémoire et d'expulsion. Une baisse du taux de réussite transférera la pression à la base de données principale. Une augmentation des expulsions indique que la capacité du cache ou la stratégie d'expulsion doit être évaluée.

## 7. Kafka Surveillance

Kafka La surveillance est utilisée pour observer le nombre de Brokers, l'état des Topics/Partitions, les offsets de production et de consommation, le retard du Consumer Group, le nombre de membres et l'état de synchronisation des réplicas.

### 7.1 Broker, Topic et Partition

| Dimension de surveillance | Métrique | Signification de la métrique | Calibre/Unité commun | Performance anormale |
| --- | --- | --- | --- | --- |
| Nombre de Brokers | `kafka_brokers` | Le nombre de Brokers actuellement visibles | pcs | Une diminution du nombre indique que le Broker n'est pas disponible ou que l'exportateur ne peut pas être accédé |
| Partitions de Topic | `kafka_topic_partitions` | Nombre de Partitions d'un Topic | pcs | Les changements dans les partitions affectent la concurrence et la capacité de consommation |
| Offset courant de la Partition | `kafka_topic_partition_current_offset` | Dernier offset de la Partition | offset / taux de croissance | Devrait augmenter continuellement lors de l'écriture de production en cours |
| Offset le plus ancien de la Partition | `kafka_topic_partition_oldest_offset` | Offset le plus ancien de la Partition | offset | Utilisé pour observer la plage de données conservées |

Requêtes courantes : 

```promql
sum by (topic) (rate(kafka_topic_partition_current_offset[5m]))
```

Suggestion d'enquête : Lorsque le taux de production est anormal, vérifiez d'abord la croissance actuelle de l'offset du topic. Si l'entreprise confirme qu'il y a des écritures mais que l'offset n'augmente pas, vérifiez les erreurs côté producteur, l'état du Broker et la configuration du Topic.

### 7.2 Groupe de consommateurs et retard

| Dimension de surveillance | Métrique | Signification de la métrique | Mesure/Unité courante | Performance anormale |
| --- | --- | --- | --- | --- |
| Offset de consommation | `kafka_consumergroup_current_offset` | Offset actuel consommé par le groupe de consommateurs | offset / taux de croissance | Aucune croissance indique que la consommation s'est arrêtée ou est bloquée |
| Retard de partition | `kafka_consumergroup_lag` | Arriéré du groupe de consommateurs sur la partition | compte | L'augmentation du retard indique que la consommation est en retard par rapport à la production |
| Retard total du groupe | `kafka_consumergroup_lag_sum` | Arriéré total du groupe de consommateurs | compte | L'augmentation continue du retard total indique un retard commercial croissant |
| Membres du groupe | `kafka_consumergroup_members` | Nombre de membres dans le groupe de consommateurs | compte | Une diminution du nombre de membres peut entraîner une capacité de consommation réduite |

Requêtes courantes :

```promql
sum by (consumergroup, topic) (kafka_consumergroup_lag)
```

```promql
sum by (consumergroup, topic) (rate(kafka_consumergroup_current_offset[5m]))
```

Suggestions de dépannage : La métrique commerciale principale de Kafka est le retard. Lorsque le retard augmente, vérifiez d'abord si le nombre de membres consommateurs a diminué, puis voyez si le taux de consommation a chuté, et enfin vérifiez le temps de traitement de l'application, les dépendances en aval et l'IO du Broker.

### 7.3 Réplicas et ISR

| Dimension de surveillance | Métrique | Signification de la métrique | Mesure/Unité courante | Performance anormale |
| --- | --- | --- | --- | --- |
| Nombre de réplicas | `kafka_topic_partition_replicas` | Nombre de réplicas de partition | compte | Un nombre de réplicas inférieur à celui attendu réduit la fiabilité |
| ISR Réplicas | `kafka_topic_partition_in_sync_replica` | Nombre de réplicas de partition synchronisés | compte | Une diminution de ISR indique des réplicas en retard ou des problèmes de Broker |
| Leader préféré | `kafka_topic_partition_leader_is_preferred` | Indique si le leader est le réplica préféré | `0/1` | Un déséquilibre à long terme peut provoquer une forte pression sur certains Brokers |

Suggestions de dépannage : Une diminution de ISR représente un risque de fiabilité plus qu'un retard ordinaire. Vérifiez l'état du courtier, le réseau, la latence d'écriture sur le disque et la synchronisation des réplicas.

## 8. MinIO Surveillance du stockage d'objets

MinIO La surveillance est utilisée pour observer la disponibilité du cluster de stockage d'objets, l'état des nœuds et des disques, la capacité des Buckets, S3 les requêtes, les erreurs, le trafic, les poignées de processus et les activités des tâches de réparation. 

### 8.1 Nœuds et disques du cluster 

| Dimension de surveillance | Métrique | Signification de la métrique | Calibre/Unité commun | Manifestation Anormale |
| --- | --- | --- | --- | --- |
| Nœuds en ligne | `minio_cluster_nodes_online_total` | Nombre de nœuds en ligne MinIO nœuds | pcs | Une diminution du nombre indique que des nœuds ne sont pas disponibles |
| Nœuds hors ligne | `minio_cluster_nodes_offline_total` | Nombre de nœuds hors ligne MinIO nœuds | pcs | Plus de 0 nécessite une attention à la disponibilité du cluster |
| Disques en ligne | `minio_cluster_disk_online_total` | Nombre de disques en ligne | pcs | Une diminution du nombre de disques affecte la redondance et la capacité d'écriture |
| Disques hors ligne | `minio_cluster_disk_offline_total` | Nombre de disques hors ligne | pcs | Supérieur à 0 nécessite un dépannage des disques ou des montages |
| Capacité utilisable | `minio_cluster_capacity_usable_free_bytes` | Capacité utilisable du cluster | Octets | Une diminution continue indique un risque de capacité insuffisante |

Suggestion de dépannage : Pour le stockage d'objets, vérifiez d'abord le statut en ligne des nœuds et des disques. N'évaluez pas les disques hors ligne uniquement par quantité ; le risque doit être jugé en combinaison avec la stratégie de redondance de code d'effacement. 

### 8.2 Capacité du bucket et nombre d'objets

| Dimension de surveillance | Métrique | Signification de la métrique | Calibre/Unité commun | Performance anormale |
| --- | --- | --- | --- | --- |
| Capacité du bucket | `bucket_usage_size` | Capacité utilisée du bucket | Octets | Croissance rapide de la capacité, besoin d'évaluer l'expansion |
| Nombre d'objets | `bucket_objects_count` | Nombre d'objets dans le bucket | Compte | Trop de petits objets augmentent la pression sur les métadonnées et le balayage |
| Répartition de la taille des objets | `minio_bucket_objects_size_distribution` | Répartition des tailles d'objets dans le bucket | Statistiques par bucket | Les changements dans la répartition des objets affectent la performance du stockage et des requêtes |

Requêtes courantes :

```promql
sum by (bucket) (bucket_usage_size)
```

```promql
sum by (bucket) (bucket_objects_count)
```

Recommandations d'enquête : La croissance de la capacité devrait être analysée séparément par Bucket. Lorsque le nombre d'objets augmente rapidement mais que la croissance de la capacité n'est pas évidente, c'est généralement dû à une augmentation des petits objets. Une attention particulière devrait être portée au nettoyage du cycle de vie et au modèle d'écriture des activités.

### 8.3 S3 Requêtes, Erreurs et Trafic

| Dimension de surveillance | Métrique | Signification de la métrique | Mesure/Unité courante | Performance anormale |
| --- | --- | --- | --- | --- |
| S3 Nombre de Requêtes | `minio_s3_requests_total` | Nombre cumulatif de S3 API requêtes | fois/seconde | Augmentation soudaine des demandes, peut être un pic d'activité ou des tentatives de nouvelle requête |
| S3 Nombre d'erreurs | `minio_s3_requests_errors_total` | Nombre cumulatif de S3 API erreurs | fois/seconde | Augmentation du taux d'erreurs affectant la lecture/écriture des objets |
| Trafic reçu | `minio_s3_traffic_received_bytes` | Cumulatif S3 octets reçus | Octets/s | Trafic de téléchargement augmenté |
| Trafic envoyé | `minio_s3_traffic_sent_bytes` | Cumulatif S3 octets envoyés | Octets/s | Augmentation du trafic de téléchargement ou de récupération depuis l'origine |

Requêtes courantes :

```promql
sum by (api) (rate(minio_s3_requests_total[5m]))
```

```promql
sum(rate(minio_s3_requests_errors_total[5m])) / sum(rate(minio_s3_requests_total[5m])) * 100
```

Recommandation d'enquête : Lorsque le taux d'erreur augmente, commencez par le décomposer par S3 type, puis vérifiez le Bucket correspondant, l'état du disque du nœud et le trafic réseau. API 

### 8.4 Processus du nœud, des fichiers et E/S

| Dimension de surveillance | Métrique | Signification de la métrique | Calibre/Unité commun | Manifestation Anormale |
| --- | --- | --- | --- | --- |
| Utilisation du disque du nœud | `minio_node_disk_used_bytes` | Utilisation du disque de MinIO nœud | Octets | Déséquilibre de capacité d'un seul nœud |
| Descripteurs de fichiers ouverts | `minio_node_file_descriptor_open_total` | Nombre de descripteurs de fichiers ouverts par MinIO processus | Compte | Les requêtes peuvent échouer en approchant la limite du système |
| Appels système de lecture | `minio_node_syscall_read_total` | Nombre cumulatif d'appels système de lecture | Fois/seconde | Augmentation anormale des appels de lecture |
| Appels système d'écriture | `minio_node_syscall_write_total` | Nombre cumulatif d'appels système d'écriture | Fois/seconde | Augmentation anormale des appels d'écriture |
| Octets lus par le processus | `minio_node_io_rchar_bytes` | Octets cumulés lus par le processus | Octets/s | Augmentation de la charge de lecture |
| Octets écrits par le processus | `minio_node_io_wchar_bytes` | Octets cumulés écrits par le processus | Octets/s | Augmentation de la charge d'écriture |
| Nombre de goroutines | `minio_node_go_routine_total` | Nombre de goroutines dans le MinIO processus | Compte | Une croissance continue peut indiquer un arriéré de demandes ou une fuite |
| Heure de début | `minio_node_process_starttime_seconds` | MinIO heure de début du processus | Horodatage Unix | Les modifications indiquent un redémarrage du processus |

Suggestion d'investigation : Pour MinIO les problèmes de performance, considérez S3 les demandes, les disques des nœuds, l'IO des processus et les goroutines ensemble. Un volume élevé de demandes seul n'est pas nécessairement anormal ; le taux d'erreur, la latence d'IO et le statut hors ligne du disque sont des signaux de risque plus clairs.

### 8.5 Activités de réparation et d'utilisation

| Dimension de surveillance | Métrique | Signification de la métrique | Norme/Unité commune | Comportement anormal |
| --- | --- | --- | --- | --- |
| Activité de réparation | `minio_heal_time_last_activity_nano_seconds` | Heure de la dernière activité de réparation | Horodatage en nanosecondes | Les réparations longues ou fréquentes nécessitent une attention à la santé du disque |
| Activité d'utilisation | `minio_usage_last_activity_nano_seconds` | Heure de la dernière analyse d'utilisation | Horodatage en nanosecondes | Des analyses d'utilisation anormales peuvent affecter la précision des statistiques de capacité |

Suggestion d'investigation : Après une récupération anormale d'un nœud ou d'un disque, surveillez si les activités de réparation progressent normalement pour éviter que la redondance des objets ne reste à risque trop longtemps.

## 9. Elasticsearch Surveillance

Elasticsearch La surveillance est utilisée pour observer la santé du cluster de recherche, le nombre de nœuds, la répartition des shards, les opérations de lecture/écriture des index, le cache, JVM, les pools de threads, les disques et le réseau. Les échecs ES ne sont généralement pas déterminés par une seule métrique ; plus couramment, des "anomalies de shard   JVM pression   rejets de pool de threads   seuils de disque" apparaissent ensemble.

### 9.1 Santé du Cluster et Nœuds

| Dimension de surveillance | Métrique | Signification de la métrique | Mesure/Unité courante | Comportement anormal |
| --- | --- | --- | --- | --- |
| Santé du Cluster | `elasticsearch_cluster_health_status` | Statut de santé du cluster ES | Valeur du statut | Le jaune/rouge indique des anomalies de shards primaires ou de répliques |
| Nombre de Nœuds | `elasticsearch_cluster_health_number_of_nodes` | Nombre de nœuds du cluster | Compte | Une baisse du nombre de nœuds peut indiquer qu'un nœud est hors ligne |
| Nombre de Nœuds de Données | `elasticsearch_cluster_health_number_of_data_nodes` | Nombre de nœuds de données dans le cluster | Compte | Une diminution des nœuds de données affecte la capacité des shards et la capacité de lecture/écriture |
| Tâches en attente | `elasticsearch_cluster_health_number_of_pending_tasks` | Nombre de tâches en attente dans le cluster | Compte | Une croissance continue indique que les mises à jour de l'état du master ou du cluster sont lentes |
| Shards primaires actifs | `elasticsearch_cluster_health_active_primary_shards` | Nombre de shards primaires actifs | pcs | Risque élevé si en diminution, peut affecter la disponibilité de l'index |
| Shards actifs | `elasticsearch_cluster_health_active_shards` | Nombre total de shards actifs | pcs | La diminution indique que les shards ne sont pas entièrement récupérés |
| Shards en initialisation | `elasticsearch_cluster_health_initializing_shards` | Nombre de shards en initialisation | pcs | Aucune diminution sur une longue période indique une récupération lente |
| Shards en relocalisation | `elasticsearch_cluster_health_relocating_shards` | Nombre de shards en relocalisation | pcs | Trop de relocalisations augmentent la pression sur le réseau et le disque |
| Shards non assignés | `elasticsearch_cluster_health_unassigned_shards` | Nombre de shards non assignés | pcs | Supérieur à 0 indique que des shards ne sont pas assignés à un nœud |
| Shards non assignés retardés | `elasticsearch_cluster_health_delayed_unassigned_shards` | Nombre de shards non assignés retardés | pcs | En attente de réaffectation après qu’un nœud soit hors ligne |

Requêtes courantes : 

```promql
elasticsearch_cluster_health_status
```

```promql
elasticsearch_cluster_health_unassigned_shards
```

Suggestions d'investigation : ES doit d'abord vérifier l'état de santé et les shards non assignés. Le statut rouge doit donner la priorité au traitement des shards primaires ; le jaune est principalement causé par des réplicas non assignés, qui ne peuvent pas non plus être laissés sans surveillance pendant longtemps. 

### 9.2 Capacité du disque et système de fichiers

| Dimension de surveillance | Métrique | Signification de la métrique | Mesure / Unité courante | Performance anormale |
| --- | --- | --- | --- | --- |
| Disque de données total | `elasticsearch_filesystem_data_size_bytes` | Capacité totale du répertoire de données ES | Octets | Utilisé pour calculer le taux d'utilisation du disque |
| Disque de données disponible | `elasticsearch_filesystem_data_available_bytes` | Capacité disponible du répertoire de données ES | Octets | Un espace disponible insuffisant peut déclencher une migration de shards ou des restrictions d'écriture |

Requêtes courantes :

```promql
(1 - elasticsearch_filesystem_data_available_bytes / elasticsearch_filesystem_data_size_bytes) * 100
```

Suggestion d'investigation : ES est très sensible à l'utilisation du disque. Lorsque l'utilisation du disque est trop élevée, des migrations de shards, des indices en lecture seule ou des échecs d'écriture peuvent se produire. Il est nécessaire de surveiller la croissance des index, les politiques de rétention et la distribution des disques des nœuds.

### 9.3 Documents, Index et Suppressions

| Dimension de surveillance | Métrique | Signification de la métrique | Unité Courante | Comportement anormal |
| --- | --- | --- | --- | --- |
| Nombre de documents | `elasticsearch_indices_docs` | Nombre actuel de documents | compte | La croissance rapide et continue des documents nécessite une évaluation de la capacité |
| Documents supprimés | `elasticsearch_indices_docs_deleted` | Nombre de documents supprimés | compte | Un taux de suppression élevé peut provoquer une pression sur la fusion |
| Nombre d'écritures dans l'index | `elasticsearch_indices_indexing_index_total` | Nombre cumulatif d'opérations sur l'index | fois/sec | Une augmentation soudaine des écritures augmente CPUla pression sur le disque et le rafraîchissement |
| Durée d'écriture de l'index | `elasticsearch_indices_indexing_index_time_seconds_total` | Temps cumulatif des opérations sur l'index | sec/sec | Une augmentation du temps d'écriture ralentit le chemin d'écriture |
| Nombre d'opérations de suppression | `elasticsearch_indices_indexing_delete_total` | Nombre cumulatif d'opérations de suppression | fois/sec | Une augmentation soudaine des suppressions peut provoquer une pression sur la fusion des segments |
| Durée des opérations de suppression | `elasticsearch_indices_indexing_delete_time_seconds_total` | Durée cumulative des opérations de suppression | secondes/seconde | Augmentation de la durée des suppressions |

Requêtes courantes :

```promql
sum by (cluster) (rate(elasticsearch_indices_indexing_index_total[5m]))
```

```promql
rate(elasticsearch_indices_indexing_index_time_seconds_total[5m]) / rate(elasticsearch_indices_indexing_index_total[5m])
```

Recommandation de dépannage : lorsque les écritures sont lentes, ne regardez pas seulement les écritures QPS. Vous devriez également considérer le rafraîchissement, la fusion, le translog, les rejets de pool de threads et les E/S disque.

### 9.4 Requêtes et requêtes Get

| Dimension de surveillance | Métrique | Signification de la métrique | Mesure/Unité courante | Comportement anormal |
| --- | --- | --- | --- | --- |
| Nombre de requêtes | `elasticsearch_indices_search_query_total` | Nombre cumulatif de requêtes de recherche | fois/seconde | Augmentation soudaine des requêtes |
| Latence des requêtes | `elasticsearch_indices_search_query_time_seconds` | Temps cumulatif des requêtes de recherche | secondes/seconde | Augmentation de la latence moyenne des requêtes |
| Nombre de requêtes Fetch | `elasticsearch_indices_search_fetch_total` | Nombre cumulatif lors de la phase de récupération de recherche | fois/seconde | Les grands ensembles de résultats peuvent augmenter le nombre de récupérations |
| Latence de récupération | `elasticsearch_indices_search_fetch_time_seconds` | Temps cumulatif de récupération de recherche | secondes/seconde | Une récupération lente est généralement liée à de grands ensembles de résultats, au disque ou au réseau |
| Nombre de requêtes Get | `elasticsearch_indices_get_exists_total`, `elasticsearch_indices_get_missing_total` | Nombre cumulatif de réussites et d'échecs des requêtes Get | fois/seconde | Une augmentation des échecs peut indiquer un accès des utilisateurs à des documents inexistants |
| Durée des requêtes Get | `elasticsearch_indices_get_time_seconds`, `elasticsearch_indices_get_exists_time_seconds`, `elasticsearch_indices_get_missing_time_seconds` | Temps cumulatif des requêtes Get | secondes/seconde | Slow Get indique une pression croissante sur le chemin de lecture |

Requêtes courantes : 

```promql
rate(elasticsearch_indices_search_query_time_seconds[5m]) / rate(elasticsearch_indices_search_query_total[5m])
```

```promql
rate(elasticsearch_indices_search_fetch_time_seconds[5m]) / rate(elasticsearch_indices_search_fetch_total[5m])
```

Recommandations de dépannage : Les requêtes lentes doivent être différenciées entre requête et récupération. Les requêtes lentes sont davantage liées aux conditions de la requête, à la structure de l'index et à la pression sur les shards ; les récupérations lentes sont plus courantes lorsqu'il y a de nombreux champs retournés, de grands ensembles de résultats ou des lectures disque lentes.

### 9.5 Segment, Fusion, Rafraîchissement et Translog

| Dimension de surveillance | Métrique | Signification de la métrique | Calibre/Unité commun | Symptômes anormaux |
| --- | --- | --- | --- | --- |
| Nombre de segments | `elasticsearch_indices_segments_count` | Nombre actuel de segments | compte | Trop de segments peuvent affecter les requêtes et la mémoire |
| Mémoire des segments | `elasticsearch_indices_segments_memory_bytes` | Mémoire occupée par les segments | Octets | Une augmentation continue peut comprimer JVM |
| Nombre de fusions | `elasticsearch_indices_merges_total` | Nombre cumulatif d'opérations de fusion | fois/sec | Des fusions fréquentes indiquent une forte pression d'écriture ou de suppression |
| Nombre de documents dans la fusion | `elasticsearch_indices_merges_docs_total` | Nombre cumulatif de documents traités par les fusions | compte/sec | Augmentation de la charge de fusion |
| Volume de données de fusion | `elasticsearch_indices_merges_total_size_bytes_total` | Données cumulatives traitées par la fusion | Octets/s | Les grandes fusions peuvent saturer les entrées/sorties disque |
| Durée de la fusion | `elasticsearch_indices_merges_total_time_seconds_total` | Temps cumulé passé sur les fusions | Secondes/seconde | Des fusions lentes peuvent affecter la performance des écritures et des requêtes |
| Nombre de rafraîchissements | `elasticsearch_indices_refresh_total` | Nombre cumulatif de rafraîchissements | Fois/seconde | Des rafraîchissements fréquents augmentent la charge |
| Durée du rafraîchissement | `elasticsearch_indices_refresh_time_seconds_total` | Temps cumulé de rafraîchissement | Secondes/seconde | Un rafraîchissement lent impacte la visibilité quasi temps réel |
| Nombre de vidages | `elasticsearch_indices_flush_total` | Nombre cumulatif de vidages | Fois/seconde | Des vidages fréquents peuvent être liés au translog et à la pression d'écriture |
| Durée du vidage | `elasticsearch_indices_flush_time_seconds` | Temps cumulé de vidage | Secondes/seconde | Des vidages lents peuvent affecter la stabilité |
| Opérations Translog | `elasticsearch_indices_translog_operations` | Nombre actuel d'opérations translog | compte | Une accumulation continue nécessite une attention particulière au vidage |
| Taille du translog | `elasticsearch_indices_translog_size_in_bytes` | Taille actuelle du translog | Octets | Une taille excessive peut affecter le temps de récupération |
| Régulation du stockage | `elasticsearch_indices_store_throttle_time_seconds_total` | Temps cumulé de régulation du stockage d'index | secondes/seconde | Régulation accrue, écritures affectées par le disque |

Suggestion d'enquête : Lorsque la pression d'écriture est élevée, la fusion, le rafraîchissement, le vidage et le translog changent ensemble. Une augmentation du temps de fusion et de la régulation du stockage indique généralement que le disque a commencé à affecter ES.

### 9.6 Cache et disjoncteur

| Dimension de surveillance | Métrique | Signification de la métrique | Unité/mesure courante | Comportement anormal |
| --- | --- | --- | --- | --- |
| Mémoire du cache de requêtes | `elasticsearch_indices_query_cache_memory_size_bytes` | Mémoire utilisée par le cache de requêtes | Octets | Une utilisation excessive peut comprimer le JVM |
| Évictions du cache de requêtes | `elasticsearch_indices_query_cache_evictions` | Nombre cumulé d'évictions du cache de requêtes | fois/seconde | Des évictions fréquentes indiquent un cache instable |
| Mémoire des Fielddata | `elasticsearch_indices_fielddata_memory_size_bytes` | Mémoire utilisée par les fielddata | Octets | Une utilisation élevée des fielddata peut facilement déclencher une pression mémoire |
| Évictions des fielddata | `elasticsearch_indices_fielddata_evictions` | Nombre cumulé d'évictions des fielddata | fois/seconde | Pression élevée due aux requêtes ou aux agrégations |
| Évictions du cache de filtres | `elasticsearch_indices_filter_cache_evictions` | Nombre cumulé d'évictions du cache de filtres | fois/seconde | Invalidation fréquente du cache de filtres |
| Taille estimée du disjoncteur | `elasticsearch_breakers_estimated_size_bytes` | Mémoire estimée du disjoncteur | Octets | Les requêtes peuvent être rejetées lorsqu'on approche de la limite |
| Limite du disjoncteur | `elasticsearch_breakers_limit_size_bytes` | Limite du circuit de disjoncteur | Octets | Utilisé pour calculer le taux d'utilisation du disjoncteur |
| Déclenchement du disjoncteur | `elasticsearch_breakers_tripped` | Nombre de fois que le disjoncteur a été déclenché | fois/incrément | Description de la croissance : requêtes bloquées en raison du risque de mémoire |

Requêtes courantes : 

```promql
elasticsearch_breakers_estimated_size_bytes / elasticsearch_breakers_limit_size_bytes * 100
```

```promql
increase(elasticsearch_breakers_tripped[10m])
```

Recommandations d'enquête : Les requêtes d'agrégation, de tri et les requêtes scriptées peuvent facilement augmenter l'utilisation des fielddata et du disjoncteur. Lorsque le disjoncteur est déclenché, il est généralement nécessaire de limiter la taille des requêtes, d'optimiser le mapping de l'index ou d'ajuster la méthode de requête.

### 9.7 JVM, CPU, et Charge

| Dimension de surveillance | Métrique | Signification de la métrique | Mesure/Unité courante | Performance anormale |
| --- | --- | --- | --- | --- |
| JVM Mémoire utilisée | `elasticsearch_jvm_memory_used_bytes` | Actuel JVM mémoire utilisée | Octets | Continuellement proche de la limite, pression GC accrue |
| JVM Mémoire Max | `elasticsearch_jvm_memory_max_bytes` | Maximum disponible JVM mémoire | Octets | Utilisé pour calculer JVM utilisation |
| JVM Mémoire engagée | `elasticsearch_jvm_memory_committed_bytes` | JVM mémoire engagée | Octets | Observer JVM allocation de mémoire |
| JVM Pic du pool de mémoire | `elasticsearch_jvm_memory_pool_peak_used_bytes` | Utilisation maximale de chaque pool de mémoire | Octets | Un pic élevé dans la génération ancienne nécessite une attention |
| Nombre de GC | `elasticsearch_jvm_gc_collection_seconds_count` | Nombre d'occurrences de GC | fois/seconde | GC fréquent, la latence peut fluctuer |
| Temps de GC | `elasticsearch_jvm_gc_collection_seconds_sum` | Temps total de GC | sec/sec | Un temps de GC élevé peut affecter les requêtes et les écritures |
| Processus CPU | `elasticsearch_process_cpu_percent` | Processus ES CPU utilisation | pourcentage | Tendance élevée prolongée CPU peut indiquer une charge élevée de requêtes ou d'écriture |
| Charge système | `elasticsearch_os_load1`, `elasticsearch_os_load5`, `elasticsearch_os_load15` | Charge du nœud 1/5/15 minutes | valeur de charge | Charge supérieure à CPU cœurs indique un enchaînement de tâches évident |
| Nombre de fichiers ouverts | `elasticsearch_process_open_files_count` | Nombre de fichiers ouverts par le processus ES | compte | Approcher des limites système peut affecter l'accès aux fichiers d'index |

Requêtes courantes : 

```promql
elasticsearch_jvm_memory_used_bytes / elasticsearch_jvm_memory_max_bytes * 100
```

```promql
rate(elasticsearch_jvm_gc_collection_seconds_sum[5m])
```

Suggestion d'investigation : plus de mémoire ES JVM la mémoire n'est pas toujours meilleure. JVM L'utilisation, le temps de GC, les fielddata, le cache de requêtes et les breakers doivent être surveillés ensemble pour déterminer si la pression mémoire est causée par les requêtes ou un décalage entre la taille du heap et l'échelle des données.

### 9.8 Pool de threads et réseau

| Dimension de surveillance | Métrique | Signification de la métrique | Mesure/Unité courante | Performance anormale |
| --- | --- | --- | --- | --- |
| Threads Actifs | `elasticsearch_thread_pool_active_count` | Nombre de threads actifs dans le pool de threads | Compte | Des threads actifs élevés à long terme indiquent une forte pression de traitement |
| Tâches terminées | `elasticsearch_thread_pool_completed_count` | Nombre cumulatif de tâches terminées par le pool de threads | Fois/Seconde | Utilisé pour observer le débit de traitement |
| Tâches rejetées | `elasticsearch_thread_pool_rejected_count` | Nombre cumulatif de tâches rejetées par le pool de threads | Fois/Seconde | Une augmentation indique que le pool de threads ou la file d'attente est pleine |
| Trafic entrant | `elasticsearch_transport_rx_size_bytes_total` | Octets cumulés reçus par le transport | Octets/s | Augmentation de la communication entre nœuds ou du trafic de requêtes |
| Trafic sortant | `elasticsearch_transport_tx_size_bytes_total` | Octets cumulés envoyés par le transport | Octets/s | Augmentation du trafic due au déplacement de shards, aux requêtes ou à la réplication |

Requêtes courantes : 

```promql
sum by (type) (rate(elasticsearch_thread_pool_rejected_count[5m]))
```

```promql
rate(elasticsearch_transport_rx_size_bytes_total[5m]) + rate(elasticsearch_transport_tx_size_bytes_total[5m])
```

Suggestion d'investigation : Le rejet du pool de threads est un signal de risque commercial très direct. Pour les rejets d'écriture, vérifiez le pool de threads bulk/index ; pour les rejets de recherche, vérifiez le pool de threads de recherche, puis identifiez les goulets d'étranglement en combinaison avec CPU, JVM, et les E/S disque.

## 10. Surveillance du service applicatif

La surveillance des applications couvre les requêtes serveur courantes, les appels de dépendances côté client, les ressources d'exécution, les liens d'affaires de l'édition collaborative, et les tâches du service RS. L'accent des métriques applicatives n'est pas mis sur les seuils individuels des ressources, mais sur le volume de requêtes, les erreurs, la latence et la santé des dépendances.

### 10.1 Métriques serveur courantes

| Dimension de surveillance | Métrique | Signification de la métrique | Portée/Unité courante | Performance anormale |
| --- | --- | --- | --- | --- |
| Disponibilité du service | `up` | Si l'exportateur d'application ou le point de terminaison des métriques est collectable | `0/1` | `0` signifie que les métriques ne sont pas accessibles ou que le service est anormal |
| Informations de build | `ego_build_info` | Version de build de l'application, branche et autres informations | Informations sur les étiquettes | Utilisé pour vérifier la version publiée |
| Nombre de démarrages | `ego_server_started_total` | Nombre cumulatif de démarrages du serveur | fois/incrément | Une augmentation indique un redémarrage du processus ou une nouvelle version |
| Requêtes serveur | `ego_server_handle_total` | Nombre cumulatif de requêtes serveur | fois/seconde | Une augmentation ou diminution soudaine des requêtes doit être jugée en combinaison avec le contexte commercial |
| Consommation de temps côté serveur | `ego_server_handle_seconds_count`, `ego_server_handle_seconds_bucket` | Statistiques de temps de requête côté serveur | P50/P95/P99 | Une latence accrue affecte l'expérience utilisateur | 

Requêtes courantes : 

```promql
sum by (service, method) (rate(ego_server_handle_total[5m]))
```

```promql
histogram_quantile(0.95, sum(rate(ego_server_handle_seconds_bucket[5m])) by (le, service, method))
```

Suggestions d'enquête : Pour les anomalies côté serveur, vérifiez d'abord si le volume de requêtes a changé, puis examinez les erreurs et la latence. Si la latence augmente mais que les ressources ne sont pas élevées, continuez à examiner les appels aux dépendances en aval et les files d'attente.

### 10.2 Appels de dépendance client

| Dimension de surveillance | Métrique | Signification de la métrique | Granularité/unité courante | Comportement anormal |
| --- | --- | --- | --- | --- |
| Volume des appels client | `ego_client_handle_total` | Le nombre de fois où l'application appelle des services en aval en tant que client | fois/seconde | Augmentation soudaine du volume des appels en aval, pouvant amplifier la pression sur les dépendances |
| Latence client | `ego_client_handle_seconds_count`, `ego_client_handle_seconds_bucket` | Statistiques de latence des appels en aval | P50/P95/P99 | Un service en aval lent peut ralentir le service actuel |
| État client | `ego_client_stats_gauge` | Pool de connexions ou métriques de type état du client | Valeur actuelle | Épuisement du pool de connexions, connexions inactives anormales, etc. |
| Kafka Latence de production | `kafka_produce_duration_seconds_bucket` | Temps nécessaire à l'application pour produire Kafka Messages | P50/P95/P99 | Augmentation de la latence de production, pouvant être due à des problèmes de Broker ou de réseau |

Requêtes courantes :

```promql
histogram_quantile(0.95, sum(rate(ego_client_handle_seconds_bucket[5m])) by (le, service, target, method))
```

Suggestion d'enquête : Lorsqu'une interface métier est lente, comparez le temps consommé côté serveur avec le temps consommé par les dépendances client. Si le ratio de temps client est élevé, vérifiez en priorité les services en aval, les middlewares ou le réseau correspondants.

### 10.3 Exécution et processus

| Dimension de surveillance | Métrique | Signification de la métrique | Norme/Unité commune | Manifestation Anormale |
| --- | --- | --- | --- | --- |
| Goroutine Go | `go_goroutines` | Nombre de goroutines dans le processus Go | Compte | Une croissance continue peut indiquer un blocage ou une fuite |
| Durée GC Go | `go_gc_duration_seconds` | Durée GC Go | Secondes/Percentile | Une augmentation du temps de GC peut affecter la latence |
| Mémoire Heap Go | `go_memstats_alloc_bytes`, `go_memstats_heap_inuse_bytes` | Allocation et utilisation du heap Go | Octets | Une croissance continue nécessite de vérifier les fuites de mémoire |
| Mémoire système Go | `go_memstats_sys_bytes` | Mémoire demandée par l'exécution Go au système | Octets | Observer conjointement avec RSS |
| Mémoire de pile Go | `go_memstats_stack_inuse_bytes` | Utilisation de la pile du goroutine | Octets | Augmente avec la croissance du goroutine |
| Node.js Nombre de GC | `nodejs_gc_duration_seconds_count` | Node.js Nombre de GC | fois/sec | Un GC fréquent peut indiquer une pression sur le tas |
| Node.js Durée du GC | `nodejs_gc_duration_seconds_sum` | Node.js Durée totale du GC | sec/sec | Une augmentation de la durée du GC peut affecter la réponse |
| Node.js Espace du tas | `nodejs_heap_space_size_used_bytes` | Utilisation de chaque Node.js espace de tas | Octets | Attention nécessaire si proche de la limite ou en croissance continue |
| Processus CPU | `process_cpu_seconds_total` | Processus CPU temps | cœurs/sec | Élevé CPU utilisation |
| Processus RSS | `process_resident_memory_bytes` | Mémoire physique du processus | Octets | Croissance RSS continue |
| Handles du processus | `process_open_fds` | Nombre de descripteurs de fichier ouverts dans le processus | compte | Fuites de handles, fuites de connexion |

Suggestion d'investigation : métriques d'exécution de Go et Node.js sont principalement utilisées pour expliquer la latence de l'application et l'augmentation des ressources. Lorsque l'application P95 augmente, si la durée du GC augmente simultanément, vérifier en priorité l'allocation mémoire et le cycle de vie des objets.

### 10.4 Service d'Édition Collaborative

| Dimension de surveillance | Métrique | Signification de la métrique | Unités Courantes | Indications Anormales |
| --- | --- | --- | --- | --- |
| Kafka Retard du Consommateur | `kafka_consumergroup_lag` | Arriéré des groupes de consommateurs liés dans l'édition collaborative | Compte | L'augmentation du retard peut causer des délais dans le traitement des événements |
| Durée du Processus | `process_flow_duration_seconds_bucket` | Durée du processus d'édition collaborative | P50/P95/P99 | Ralentissement dans le lien de collaboration du document |
| Nombre de Processus | `process_total` | Nombre total de processus traités | Fois/Seconde | Changements anormaux dans le volume de traitement |
| Taille du Contenu du Fichier | `file_content_size_bytes_bucket` | Distribution des tailles de contenu des fichiers | Statistiques par tranche | Une augmentation de la proportion de gros fichiers peut affecter le temps de traitement |
| Durée des Modifications | `handle_changeset_cost_seconds_bucket` | Temps nécessaire pour traiter un ensemble de modifications | P50/P95/P99 | Augmentation du délai de synchronisation de l'édition |
| Nombre de Calculs Modoc | `modocComputeCount` | Nombre de calculs modoc | Fois/Seconde | Augmentation anormale du volume de calcul |
| Invocations sans serveur | `serverless_invocations` | Nombre d'appels sans serveur | Fois/Seconde | Les échecs ou pics d'invocation peuvent affecter le lien |

Requêtes courantes :

```promql
histogram_quantile(0.95, sum(rate(handle_changeset_cost_seconds_bucket[5m])) by (le))
```

Suggestions d'enquête : pour les liens d'édition collaborative, Kafka Le retard, la durée du processus, la durée du jeu de modifications et la taille des fichiers doivent être examinés ensemble. Lorsque la proportion de gros fichiers augmente, une augmentation de la durée pourrait être une pression de capacité normale plutôt qu'une défaillance ponctuelle.

### Service RS 10.5

| Dimension de surveillance | Métrique | Signification de la métrique | Portée/Unité courante | Performance anormale |
| --- | --- | --- | --- | --- |
| HTTP Nombre de Requêtes | `http_requests_total` | Nombre cumulatif de HTTP requêtes | fois/seconde | Augmentation ou diminution soudaine des requêtes |
| HTTP Durée | `http_requests_duration_seconds_bucket`, `http_requests_duration_seconds_sum`, `http_requests_duration_seconds_count` | HTTP Durée de la requête | P50/P95/P99 | Latence de l'interface augmentée |
| gRPC Nombre de Requêtes | `grpc_requests_total` | Nombre cumulatif de gRPC requêtes | fois/seconde | gRPC Exceptions d'appel |
| gRPC Durée | `grpc_requests_duration_seconds` | gRPC Durée de la requête | P50/P95/P99 | Traitement en aval ou interne plus lent |
| Durée de la tâche d'exportation | `export_task_duration_milliseconds_count` | Nombre et durée du traitement des tâches d'exportation | ms/temps | Tâches d'exportation en ralentissement ou s'accumulant |
| Durée de la tâche d'importation | `import_task_duration_milliseconds_count` | Nombre de processus de tâches d'importation et durée | ms / par tâche | Tâches d'importation ralentissant ou s'empilant |
| Tâches d'exportation en cours | `export_task_in_progress` | Tâches d'exportation actuellement exécutées | compte | Si cela ne diminue pas pendant longtemps, cela indique que les tâches sont bloquées |
| Tâches d'importation en cours | `import_task_in_progress` | Tâches d'importation actuellement exécutées | compte | Si cela ne diminue pas pendant longtemps, cela indique que les tâches sont bloquées |
| Métriques Tokio | `tokio_metrics` | Métriques d'exécution Rust Tokio | Valeur actuelle / taux | File d'attente d'exécution ou planification des tâches anormale |
| Métriques jemalloc | `jemalloc` | Métriques de l'allocateur de mémoire | Octets / compte | Fragmentation de la mémoire ou anomalie d'allocation |
| TCP Métriques | `tcp` | Service RS TCP Métriques liées à la connexion | Nombre / taux | Pression de connexion ou anomalie réseau |

Suggestion d'investigation : Le service RS devrait examiner à la fois les requêtes en ligne et les tâches de longue durée telles que l'import/export. Un nombre de tâches en cours qui n'augmente jamais véritablement indique généralement "des tâches bloquées" plus fiablement que la durée moyenne.

## 11. Lecture des métriques et suggestions d'investigation

### 11.1 Ordre général d'investigation

| Étape | Élément d'observation | Objectif |
| --- | --- | --- |
| 1 | `up`, heure de début, Pod prêt | Confirmer si le service est toujours actif et s'il a récemment redémarré |
| 2 | Volume de requêtes, taux d'erreur, P95/P99 latence | Déterminer si cela affecte réellement l'activité |
| 3 | CPU, mémoire, disque, réseau | Déterminer s'il existe un goulot d'étranglement des ressources |
| 4 | Latence des dépendances en aval, Kafka Retard, requêtes de base de données lentes | Déterminer si cela ralentit à cause des dépendances |
| 5 | Version de publication, configuration, changements de trafic | Déterminer si cela est lié à des changements |

Lors du dépannage réel, ne vous précipitez pas pour regarder tous les graphiques en premier. Confirmez d'abord "s'il y a un impact sur l'activité", puis trouvez "d'où vient l'impact". Par exemple, si une interface est lente, regardez d'abord l'application P95, puis vérifiez la latence de dépendance client ; si la dépendance est normale, regardez de nouveau le service CPU, GC, mémoire, et limitation du conteneur.

### 11.2 Combinaisons d'exceptions courantes

| Symptôme | Performance métrique commune | Direction d'investigation prioritaire |
| --- | --- | --- |
| Interface lente | Application P95/P99 en augmentation, CPU pas élevée | Dépendances en aval, requêtes de base de données lentes, Kafka Retard |
| CPU pleinement utilisé | `container_cpu_usage_seconds_total` limites élevées, limitation élevée | CPU interfaces chaudes, tâches de traitement par lots |
| Mémoire OOM | Ensemble de travail proche de la limite, nombre de redémarrages en augmentation | Fuites de mémoire, limite trop faible, traitement d'objets volumineux |
| Disque lent | iowait, IO occupé, latence lecture/écriture en hausse | Base de données, Kafka, MinIO, écriture de journal |
| Réseau anormal | Pic de trafic accompagné de chute/erreurs | Nœud NIC, CNI, lien, nombre de connexions |
| Kafka Latence | `kafka_consumergroup_lag` en augmentation continue | Instances consommateurs, temps de consommation, dépendances en aval |
| Redis Contre-pression | Taux de réussite en baisse, échecs en hausse | Politique d'expiration des clés, pénétration du cache, capacité |
| MySQL Lent | requêtes lentes, scan, attente de verrou en hausse | SQL, index, verrous, IO disque |
| MinIO Risque | Disque hors ligne, taux d'erreur, niveaux de capacité en hausse | Disque, nœuds, croissance du Bucket, état de réparation |
| Elasticsearch Requête lente | temps de requête/recherche en hausse, rejets de thread pool en hausse | Conditions de requête, structure d'index, JVM, IO disque |
| Elasticsearch Écriture lente | temps d'indexation, temps de fusion, limitation du stockage en hausse | Pics d'écriture, rafraîchissement, fusion, niveaux de disque |
