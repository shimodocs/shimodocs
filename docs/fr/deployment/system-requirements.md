# Exigences du système

[← ShimoDocs Suite documentation de déploiement](README.md)

## 1. Préparer les ressources selon le scénario

| Scénario d'utilisation | Déploiement recommandé | Préparation des ressources |
| --- | --- | --- |
| Petite équipe légère, PoC, démonstration, vérification des fonctionnalités | Déploiement sur serveur unique | 1 serveur |
| Lancement officiel, fonctionnement à long terme, nécessitant une haute disponibilité ou une montée en charge future | Cluster à haute disponibilité | 3 serveurs ou plus |

- Le déploiement sur serveur unique convient pour une vérification rapide et une utilisation à petite échelle.
- Le déploiement en cluster est adapté au lancement officiel, à l'exploitation à long terme et à l'évolution future.

## 2. Exigences du système d'exploitation

| Système d'exploitation | Versions prises en charge | Architecture prise en charge |
| --- | --- | --- |
| Ubuntu | 22.04, 24.04 | x86 |

Exécuter sur chaque serveur :

```bash
cat /etc/os-release
uname -m
```

Résultats de confirmation : 

- Le système d'exploitation est Ubuntu 22.04 ou Ubuntu 24.04. 
- CPU L'architecture est x86. 
- Le compte d'installation est `root`, ou dispose de privilèges d'administration système équivalents. 

Remarque : Raisons pour ne plus prendre en charge les systèmes CentOS 
- CentOS Linux 7 et 8 ont atteint leur fin de vie, CentOS ne fournit plus officiellement CentOS 9 et les versions ultérieures, et ne reçoit plus de nouvelles mises à jour de sécurité, corrections de vulnérabilités ou correctifs de problèmes. 
- Les composants système de base ne peuvent pas recevoir de correctifs de sécurité à long terme, laissant potentiellement des vulnérabilités exposées mais non réparables, ce qui ne répond pas aux exigences de sécurité de l'environnement de production. 
- Le noyau, glibc, OpenSSL et d'autres composants de base dans CentOS 7/8 sont relativement anciens et ne peuvent pas répondre aux exigences des nouveaux Kubernetes environnements d'exécution et bibliothèques de dépendances. 
- CentOS Stream a un positionnement de version et un mécanisme de mise à jour différents par rapport à CentOS Linux traditionnel, et les environnements CentOS Stream qui n'ont pas subi de vérification spéciale de compatibilité ne sont pas non plus inclus dans le support officiel. 


## 3. Exigences de configuration du serveur 

### 3.1 Déploiement sur un seul nœud 

- Convient aux petites équipes légères, moins de 200 personnes. 
- PoC, des scénarios de démonstration et de vérification fonctionnelle peuvent être préparés en fonction des ressources du nœud unique. 

| Projet | Exigence | 
| --- | --- | 
| Nombre de serveurs | 1 | 
| CPU | 16 cœurs ou plus |
| Mémoire | 32 Go ou plus |
| Disque système | Racine `/` partition 100 Go ou plus |
| Disque de données | Monté indépendamment `/data`, espace disponible de 300 Go ou plus, prend en charge l'extension |

### 3.2 Déploiement de cluster

Pour les scénarios nécessitant un lancement officiel, une opération à long terme, une haute disponibilité ou une expansion future, préparez les ressources selon les exigences du cluster.

| Élément | Exigence |
| --- | --- |
| Nombre de serveurs | 3 ou plus |
| Rôles recommandés | `3 master   N worker` |
| CPU par nœud | 16 cœurs ou plus |
| Mémoire par nœud | 32 Go ou plus |
| Disque système par nœud | Racine `/` partition 100 Go ou plus |
| Disque de données par nœud | Monté indépendamment `/data`, espace disponible de 300 Go ou plus, prend en charge l'extension |

Notes sur la partition :

- Réservez au moins 100 Go pour la racine `/` partition.
- Il est recommandé de placer `/root`, `/var`, `/tmp` sous la partition racine pour une gestion unifiée.
- Utilisez un disque de données indépendant pour le répertoire de données, monté à `/data`.

## 4. Commandes d'autocontrôle du serveur

Exécuter sur chaque serveur : 

```bash
# ============================================
# 1. View CPU architecture and core information
#    - Architecture type (x86_64/aarch64)
# ============================================
lscpu

# ============================================
# 2. Display memory and swap usage in GiB
# ============================================
free -g

# ============================================
# 3. File System Disk Space Usage
# ============================================
df -h

# ============================================
# 4. Find the executable file path
# ============================================
which iptables gzip tar

# ============================================
# 5. Display system time, time zone, and NTP synchronization status
#    Distributed clusters must have strict time synchronization, otherwise it will affect authentication and log sequencing.
# ============================================
timedatectl status
```

Liste de contrôle de comparaison :

| Élément d'inspection | Condition de réussite |
| --- | --- |
| CPU | 16 cœurs ou plus |
| Mémoire | 32 Go ou plus |
| Disque système | Racine `/` espace disponible de la partition 100 Go ou plus |
| Disque de données | `/data` monté, espace disponible 300 Go ou plus |
| Commandes de base | `iptables`, `gzip`, `tar` peut être trouvé |
| Synchronisation de l'heure | La synchronisation de l'heure système est normale |

## 5. Exigences du navigateur

| Navigateur | Exigence de version |
| --- | --- |
| Chrome | 86 ou plus |
| Safari | 11 ou plus |
| Firefox | 102 ou plus |
| Edge | 84 ou plus |

Il est recommandé de privilégier l'utilisation des versions récentes de Chrome ou Edge pour accéder à l'installateur et ShimoDocs Suite.

## 6. Exigences middleware

| Composant | Exigence de version |
| --- | --- |
| Elasticsearch | 8.18.x |
| MongoDB | 4.4.x |
| Redis | 6.2.x |
| MySQL | 8.0 |
| Dameng | V8 03134284194-20240920-243574-20108 |
| Kafka | 2.7 à 3.5 |
| Stockage d'objets | Compatible avec S3 protocole<br>et assurez-vous que son adresse de point de terminaison peut être directement accédée par les navigateurs clients depuis le réseau public (puisque ShimoDocs le chargement des ressources statiques de l'application et les opérations de lecture/écriture de documents doivent être effectués via des connexions directes du navigateur au stockage d'objets). |

Le stockage d'objets peut choisir Huawei Cloud OBS, Alibaba Cloud OSS, Tencent Cloud COS, AWS S3. Pour un déploiement local, utiliser MinIO peut être considéré.

Si vous utilisez un middleware intégré à l'installateur, continuez avec les options par défaut sur la page d'installation. 
Si vous utilisez un middleware externe existant, préparez l'adresse, le port, le compte, PASSWORD, DATABASE_NAME ou le nom du Bucket avant l'installation.

## 7. Exigences relatives aux ports

Avant le déploiement, assurez-vous que le serveur, le groupe de sécurité, le pare-feu, le répartiteur de charge et les politiques réseau ont autorisé les ports suivants.

| Port | Cible | Objectif |
| --- | --- | --- |
| `18080/TCP` | Interface Web de l'installateur | Accéder à la page d'installation |
| `80/TCP` ou `443/TCP` | ShimoDocsSERVICE_DOMAIN | Entrée d'accès utilisateur |
| `22/TCP` | Tous les nœuds de déploiement | SSH Connexion et distribution des tâches d'installation |
| `3306/TCP` | MySQL | Connexion à la base de données |
| `6379/TCP` | Redis | Connexion au cache |
| `27017/TCP` | MongoDB | Connexion à la base de données de documents |
| `9092/TCP` | Kafka | Connexion à la file de messages |
| `9200/TCP` | Elasticsearch | Connexion au service de recherche |
| Par port de service | S3 / OBS / OSS / COS / MinIO | Connexion au stockage d'objets |

## 8. Exigences relatives aux E/S disque

Il est recommandé d'utiliser des SSD pour les disques de données. La performance du disque doit répondre aux normes suivantes :

| Élément | Exigence |
| --- | --- |
| Lecture/écriture mixte IOPS | Supérieure à 5000 |
| Débit de lecture/écriture séquentielle | Supérieur à 150 Mo/s |
| Latence moyenne | Environ 5 ms ou moins |

Après l'installation `fio`, des tests peuvent être réalisés sur `/data`.

### 8.1 Test de lecture/écriture mixte

```bash
fio --name=randrw-test \
  --filename=/data/testfile \
  --size=20G \
  --rw=randrw \
  --rwmixread=70 \
  --bs=4k \
  --ioengine=libaio \
  --direct=1 \
  --iodepth=32 \
  --numjobs=4 \
  --runtime=300 \
  --time_based \
  --group_reporting
```

Faites attention à la IOPS dans les résultats ; la lecture/écriture mixte IOPS doit dépasser 5000 pour continuer. 

### 8.2 Test de lecture séquentielle

```bash
fio --name=seqread-test \
  --filename=/data/testfile \
  --size=20G \
  --rw=read \
  --bs=1M \
  --ioengine=libaio \
  --direct=1 \
  --iodepth=32 \
  --numjobs=1 \
  --runtime=300 \
  --time_based \
  --group_reporting
```

### 8.3 Test d'écriture séquentielle

```bash
fio --name=seqwrite-test \
  --filename=/data/testfile \
  --size=20G \
  --rw=write \
  --bs=1M \
  --ioengine=libaio \
  --direct=1 \
  --iodepth=32 \
  --numjobs=1 \
  --runtime=300 \
  --time_based \
  --group_reporting
```

Le débit de lecture séquentielle et d'écriture séquentielle atteignant plus de 150 Mo/s peut continuer. 

Les fichiers de test peuvent être supprimés après les tests : 

```bash
rm -f /data/testfile
```

## 9. Exigences relatives à la bande passante réseau publique

Estimez la bande passante pour les scénarios d'accès au réseau public en fonction du nombre d'utilisateurs :

```text
PUBLIC_NETWORK_BANDWIDTH = NUMBER_OF_USERS x 0.25 Mbps
```

Exemple :

| Nombre d'utilisateurs | Bande passante réseau publique recommandée |
| --- | --- |
| 100 utilisateurs | Plus de 25 Mbps |
| 200 utilisateurs | Plus de 50 Mbps |
| 500 utilisateurs | Plus de 125 Mbps |

Pour les scénarios d'accès à l'intranet, il est également recommandé d'évaluer la bande passante sortante, entrante et équilibrée en utilisant les mêmes critères.

## 10. Recommandations de version du navigateur pour l'installateur et la plateforme d'exploitation

Il est recommandé d'utiliser Google Chrome version 111 ou supérieure, de préférence la dernière version stable.

## 11. Liste de contrôle avant le déploiement

Avant de commencer l'installation, confirmez chaque élément :

- La version du système d'exploitation répond aux exigences.
- CPULa mémoire, le disque système et le disque de données répondent aux exigences.
- `/data` est monté sur un disque de données séparé.
- `iptables`, `gzip`, et `tar` sont installés.
- La synchronisation du temps système est normale.
- La méthode d'installation en ligne ou hors ligne a été déterminée.
- Port de l'installateur `18080` est accessible.
- Ports d'accès métier `80` ou `443` sont ouverts.
- Si un middleware externe est utilisé, les informations de connexion sont entièrement préparées.
- Le stockage d'objets est compatible avec le protocole S3 et les autorisations du bucket et du compte sont prêtes. 
- Les tests IO du disque de données répondent aux exigences. 
- La bande passante réseau publique ou interne répond au nombre d'utilisateurs attendu.
