# Planification des ressources

[← ShimoDocs Suite documentation de déploiement](../README.md)

## 1. Objet du document

Ce document est utilisé pour guider la planification des ressources serveur et middleware dans des scénarios de déploiement privatifs, à titre de référence pour les ingénieurs de mise en œuvre, les ingénieurs d'exploitation et le personnel technique support avant-vente.

Le contenu du document est basé sur la planification de capacité de projets historiques, des configurations types et des référentiels middleware, et peut être utilisé pour l'estimation avant-vente, la demande de ressources, le déploiement d'implémentation et l'évaluation des extensions ultérieures.

## 2. Portée et instructions

### 2.1 Portée

Ce document s'applique à la planification préalable des nœuds d'application et des ressources middleware pour différentes échelles d'utilisateurs dans des scénarios de déploiement privatifs.

### 2.2 Instructions

* Les configurations dans ce document sont toutes des configurations recommandées, utilisées pour l'évaluation de la capacité des projets en phase initiale et la planification des ressources.

* Les ressources des nœuds applicatifs et les ressources des middleware doivent être calculées séparément; une planification mixte n'est pas recommandée.

* Dans les scénarios à grande échelle avec de nombreux utilisateurs, les ressources middleware doivent être calibrées davantage en fonction de la charge maximale des activités, des modèles de concurrence, des résultats des tests de résistance de capacité et des données de surveillance en production.

* Dans un environnement de production formel, il est recommandé de réserver une capacité d'expansion et de privilégier la construction à haute disponibilité.

* Si vous utilisez des serveurs d'architecture domestiques, CPU il est recommandé d'estimer les ressources globales à deux fois la spécification standard.

## 3. Principes de planification

### 3.1 Principes de déploiement des applications et des middleware

* Pour les scénarios avec moins de 10 000 utilisateurs, il est possible d'évaluer s'il convient de déployer certains middleware au sein du K8s cluster en fonction de la situation réelle du projet.

* Pour les scénarios avec 10 000 utilisateurs ou plus, il est recommandé de déployer complètement séparément les nœuds applicatifs et les middleware.

* Il est recommandé de déployer les middleware principaux tels que les bases de données, les caches, les files de messages et les services de recherche avec une architecture à haute disponibilité en priorité.

* Lorsque les conditions le permettent, il est recommandé de privilégier l'utilisation des services middleware managés matures sur le cloud public afin d'améliorer la stabilité et la maintenabilité.

### 3.2 Principes de planification du stockage d'objets

* Il est préférable d'utiliser des services de stockage d'objets sur le cloud public, tels que Alibaba Cloud OSS, Huawei Cloud OBS, Tencent Cloud COS, AWS S3.

* Si un déploiement privé du stockage d'objets est utilisé, SSD Des disques doivent être utilisés, et les performances, la stabilité et l'opérabilité après l'augmentation de la capacité doivent être soigneusement évaluées.

* Si l'activité implique de grandes quantités de téléchargements, de téléversements, de prévisualisations de gros fichiers ou des scénarios d'édition collaborative multi-utilisateurs de grands tableurs, il est recommandé de prioriser l'utilisation de services de stockage objet indépendants.

## 4. Planification des nœuds d'application

### 4.1 Classification des spécifications des nœuds d'application

#### Spécification A

* Spécification recommandée : `24C / 48G / >=500G SSD * N`

* Plage d'application : moins de 10 000 utilisateurs

* Fonctionnalités applicables : 

   * Peut supporter des scénarios d'entreprises petites à moyennes 

   * Le middleware peut être déployé dans l' K8s environnement selon le projet 

   * Un seul nœud supporte une charge élevée ; lorsqu'un nœud tombe en panne, la portée de l'impact est relativement large 

#### Spécification B 

* Spécification recommandée : `16C / 32G / >=300G SSD * N` 

* Plage d'application : 10 000 utilisateurs et plus 

* Fonctionnalités applicables : 

   * Convient aux scénarios de déploiement à grande échelle et haute disponibilité 

   * Doit utiliser un middleware indépendant 

   * Utilise une approche multi-nœuds de petite taille, offrant une planification plus équilibrée et une montée en charge plus flexible 

   * Lorsque qu'un nœud est maintenu ou rencontre un problème, l'impact global sur l'activité est moindre 






### 4.2 Critères de calcul des nœuds d'application 

Basé sur des exemples de projets existants et des règles de calcul de capacité, il est recommandé d'estimer les nœuds d'application en utilisant la formule suivante : 

`Number of nodes = Number of users × 0.03 ÷ 160` 

Cela peut être simplement compris comme : 

`Number of nodes ≈ Number of users ÷ 5300`

Où :

* Le coefficient d'utilisateurs concurrents est estimé à `0.03`.

* La capacité d'un seul `16C / 32G` nœud est d'environ `150 ~ 180 QPS`.

* Il est recommandé d'utiliser `160 QPS/node` comme base de calcul.

* Il est recommandé d'arrondir le résultat calculé à la hausse, en réservant une capacité supplémentaire pour l'expansion.

### 4.3 Tableau de configuration recommandé pour les nœuds d'application

| Échelle d'utilisateurs (personnes) | Spécifications du nœud | Quantité recommandée | Suggestions de déploiement |
|:----|:----|:----|:----|
|500|24C / 48G / 500G SSD|1 unité|Peut être déployé sur une seule machine ; pour une haute disponibilité, il est recommandé de déployer au moins 3 serveurs|
|3000|24C / 48G / 500G SSD|3 unités|Mode cluster, déploiement à haute disponibilité (seuil minimum de spécification pour le déploiement en cluster)|
|10,000|24C / 48G / 500G SSD|3 unités|Mode cluster, déploiement à haute disponibilité ; l'utilisation d'un middleware externe peut être évaluée en fonction des besoins du projet|
|30,000|16C / 32G / 300G SSD|5 unités|Mode cluster, déploiement à haute disponibilité, utilisant un middleware indépendant|
|50,000|16C / 32G / 300G SSD|10 unités|Mode cluster, déploiement à haute disponibilité, utilisant un middleware indépendant|
|100,000|16C / 32G / 300G SSD|18 ~ 20 unités|Il est recommandé de commencer par 18 unités et de réserver de la capacité pour l'expansion, en utilisant un middleware indépendant|
|200,000|16C / 32G / 300G SSD|38 ~ 40 unités|Il est recommandé de construire et de déployer par phases|
|300,000|16C / 32G / 300G SSD|56 ~ 60 unités|Il est recommandé de construire et de déployer par phases|
|500,000|16C / 32G / 300G SSD|94 ~ 100 unités|Il est recommandé de planifier une pool de ressources indépendante et de construire et déployer par phases|
|700,000|16C / 32G / 300G SSD|132 ~ 140 unités|Il est recommandé de planifier une pool de ressources indépendante et de construire et déployer par phases|

### 4.4 Conclusions sur la planification des nœuds d'application

* Il est recommandé que les utilisateurs en dessous de 10 000 utilisent la Spécification A.

* Il est recommandé que les utilisateurs de 10 000 et plus utilisent la Spécification B.

* Pour une échelle d'utilisateurs de 100 000, il est possible de commencer avec 18 unités selon l'ancre modèle, et les autres échelles sont estimées selon une formule unifiée et arrondies à la hausse.

* Pour les projets à croissance continue, il est recommandé de procéder à une stratégie d'expansion par phases afin d'éviter un investissement unique excessif.

## 5. Planification du middleware

### 5.1 Principes de classification du middleware

La planification actuelle des ressources middleware est exécutée selon deux niveaux de référence :

* `Users below 3,000`: Utiliser une configuration de référence à petite échelle.

* `3000 users and above`: Utiliser une configuration de référence à grande échelle. 

Pour des scénarios de plus grande envergure tels que 10 000, 30 000, 50 000, 100 000, 200 000, 300 000, 500 000, 700 000 utilisateurs, il est recommandé de commencer uniformément avec la configuration de référence '3000 utilisateurs et plus', et de s'adapter dynamiquement en fonction de la croissance de l'activité. 

### 5.2 Tableau de référence des spécifications middleware 

|Middleware|Version recommandée|Moins de 3000 utilisateurs|3000 utilisateurs et plus|Exigences de haute disponibilité| 
|:----|:----|:----|:----|:----| 
|MySQL|MySQL 8.0|4C / 8G / 200G SSD|8C / 16G / 200G SSD|Haute disponibilité avec basculement maître-esclave<br>Jeu de caractères : utf8mb4<br>Fuseau horaire : Asia/Shanghai ou UTC<br>Connexions : max_connexions ≥ 1000| 
|MongoDB|MongoDB 4.4|2C / 8G / 100G SSD|4C / 16G / 100G SSD|Cluster de haute disponibilité en ensemble réplicat| 
|Redis|Redis 6.2.21|2C / 4G / 100G SSD|2C / 8G / 100G SSD|Haute disponibilité maître-esclave/sentinelle, persistance des données ; Mode Cluster non supporté ; Nombre de bases de données ≥ 64| 
|Kafka|Kafka 3.5|2C / 4G / 300G SSD|4C / 8G / 300G SSD|brokers >= 3, facteur de réplication par défaut 3<br>Rétention des messages : 72 heures (ajustable selon les besoins de l'activité)<br>Taille maximale d'un message unique par Topic : 10 MB<br>Authentification : prend en charge SASL l'accès chiffré (PLAIN, SCRAM-SHA-256, SCRAM-SHA-512)|
|Elasticsearch|ES 8.18.5|2C / 4G / 200G SSD|4C / 8G / 200G SSD|Nombre de nœuds >= 3<br>Installations requises :<br>analysis-ik (segmentation de mots chinois),<br>analysis-pinyin (segmentation Pinyin)|
|Stockage d'objets|S3 protocole compatible|Compatible avec S3|Compatible avec S3 protocole|Préférer le cloud public, doit supporter HTTPS l'accès externe|

Remarque : 

* Les spécifications de middleware ci-dessus doivent être adaptées en fonction de la charge réelle





## 6. Suggestions de mise en œuvre et d'exploitation & maintenance

### 6.1 Suggestions de mise en œuvre du déploiement

* MySQL, MongoDB, Redis, Kafka, Elasticsearch il est recommandé de déployer en mode cluster à haute disponibilité.

* Si les conditions le permettent, il est recommandé de donner la priorité à l'utilisation des bases de données et des services de middleware gérés par le cloud public pour améliorer la stabilité et la maintenabilité.

* Pour les scénarios utilisateurs de 10 000 utilisateurs ou plus, il est recommandé de déployer les nœuds d'application et le middleware séparément. 

* Pour Kafka, il est recommandé d'utiliser une instance séparée afin d'éviter le partage des ressources avec d'autres entreprises. 

### 6.2 Recommandations pour la mise en œuvre du stockage d'objets 

* Il est recommandé de prioriser l'utilisation des produits de stockage d'objets du cloud public. 

* Si un stockage d'objets privé est utilisé, SSD des disques doivent être employés. 

* Si l'espace d'équipe implique un grand nombre de scénarios pour le téléchargement, le téléversement ou l'aperçu de fichiers volumineux, la capacité de stockage d'objets, le débit et la bande passante doivent être des facteurs clés d'évaluation. 

### 6.3 Considérations sur la mise à l'échelle 

Dans les scénarios commerciaux suivants, il est recommandé de prioriser l'évaluation et l'ajout de ressources middleware : 

* Un grand nombre de pièces jointes téléversées, téléchargées ou prévisualisées 

* Recherche en texte intégral à haute fréquence 

* Accumulation de messages ou tâches asynchrones intensives 

* Écritures en lot et analyses statistiques pendant les périodes de pointe 

* Croissance continue du volume des journaux 

Les principaux indicateurs sur lesquels se concentrer incluent : 

* Base de données : CPU, mémoire, E/S disque 

* Redis: nombre de connexions, taux de réussite, utilisation de la bande passante 

* Kafka: nombre de brokers, accumulation de messages, espace disque 

* Elasticsearch: Nombre de nœuds, taille de l'index, capacité de stockage 

* Stockage d'objets : performances en lecture/écriture, débit des requêtes, capacité, bande passante 






## 7. Conclusion 

* Pour les scénarios à petite échelle (moins de 10 000 utilisateurs), il est recommandé d'utiliser la configuration des nœuds d'application de la Spécification A, et d'évaluer s'il faut déployer certains middleware au sein du cluster en fonction de la situation du projet. 

* Pour les scénarios à moyenne et grande échelle (10 000 utilisateurs ou plus), il est recommandé d'utiliser la configuration des nœuds d'application de la Spécification B, associée à un middleware indépendant et à une architecture haute disponibilité. 
* Il est recommandé de configurer le middleware en fonction de deux bases : « moins de 3000 utilisateurs » et « 3000 et plus ». Pour les projets à grande échelle, l'expansion continue se fait sur la base des tests de résistance et des données de surveillance. 
* Avant la mise en œuvre officielle, la confirmation de la planification des ressources, la vérification de compatibilité et les tests de capacité sous contrainte doivent être réalisés simultanément afin d'éviter toute divergence entre les spécifications de déploiement et le scope réellement supporté. 

* Si utilisation d'un produit national CPU architecture serveur, il est recommandé d'estimer les ressources à deux fois la spécification standard. 

* Ce manuel est destiné à la sélection avant installation et ne remplace pas les tests de résistance sur site ni la mise en œuvre finale.
