# Configuration des middlewares

[← ShimoDocs Suite documentation de déploiement](../../../README.md)

## 1. Aperçu 

La configuration middleware est la page dans la MDP plateforme d'opérations qui s'intègre avec divers stockages et middleware dans l'environnement client, gérant de manière centralisée les informations de connexion pour des composants tels que S3 le stockage d'objets, Redisla file de messages, Kafkala base de données relationnelle, MySQLla base de données de documents, MongoDBet la recherche en texte intégral. ElasticsearchLes modifications de configuration sont distribuées à l'environnement client via des tâches asynchrones, avec affichage en temps réel des progrès des tests et de la publication pendant le processus de modification. 

Principales capacités : 

- Configurer les informations de connexion pour chaque middleware (Endpoint, clé d'accès, USERNAMEPASSWORDetc.) 
- Basculer entre différents fournisseurs (S3 et OSS, AWS / MinIO / Tencent Cloud COS / Huawei Cloud OBS, MySQL et DM) Dameng) 
- Suivre les valeurs modifiées dans les formulaires ; seuls les champs modifiés seront soumis 
- Chaque section de configuration peut être testée indépendamment ; la publication n'est autorisée qu'après réussite de la vérification 
- Publication en un clic : soumettre par lot toutes les configurations de champ modifiées et exécuter de manière asynchrone 

### 1.1 Utilisateurs concernés 

| Rôle           | Opérations courantes                                     | 
| -------------- | ---------------------------------------------------- | 
| Ingénieur de mise en œuvre | Remplir les informations de connexion du middleware lors du déploiement initial | 
| Opérations de garde | Remplacement des identifiants, modification des points de terminaison, test des connexions | 
| Réponse d'urgence | Basculer sur le middleware de secours et modifier les configurations de délai d'attente |

### 1.2 Opérations non recommandées dans ce module 

Changer de fournisseur (tel que S3 → OSS) implique des modifications avec migration de données à grande échelle en aval et doit être traité selon le processus de changement. L'ajustement par lot des informations de connexion dans plusieurs environnements n'est pas pris en charge par ce module ; vous devez entrer chaque environnement individuellement pour configurer la page. La planification de la capacité du middleware et les alertes de surveillance ne se trouvent pas sur cette page ; veuillez utiliser le module de gestion de cluster et de configuration des alertes. 

---

## 2. Explication détaillée de chaque configuration du middleware 

### 2.1 S3 Stockage d'objets 

**Étapes opérationnelles**: Entrez dans « Configuration du middleware » depuis le menu de gauche, qui se trouve par défaut dans cette section → Faites défiler vers le bas pour voir trois sections de configuration dans l'ordre : Paramètres de l'instance publique S3 , Paramètres de l'instance de modification collaborative S3 , et Paramètres du compartiment. 

#### 2.1.1 Public S3 et Modification collaborative S3

**Étapes opérationnelles**: Remplissez d'abord le public S3 paramètres d'instance, puis remplissez l'édition collaborative S3 paramètres d'instance, et enfin remplissez les paramètres du Bucket. Après avoir modifié un champ, cliquez sur « Tester la connexion » en bas.
Les champs du formulaire dans les deux sections sont cohérents :
| Champ               | Description                                                              | Requis |
| ----------------- | ---------------------------------------------------------------------- | ---- |
| Type de stockage           | Sélection déroulante 'S3 (Stockage d'objets)' ou 'OSS (Alibaba Cloud)'                             | Oui   |
| Sous-type               | Chargé dynamiquement en fonction du type de stockage : Pour S3, les options incluent AWS / Tencent Cloud COS / Huawei Cloud OBS / MinIO / Autres ; pour OSS, seul Alibaba Cloud OSS est disponible | Oui    |
| ID de clé d'accès     | Identifiant d'identification                                                        | Oui   |
| Clé secrète d'accès | Clé d'identification, champ de saisie PASSWORD masqué | Oui |
| Région | Par exemple `cn-north-1` | Oui |
| ForcePathStyle | Case à cocher, activer ou non l'accès au style chemin | Non |
| SSL | Case à cocher, activer ou non HTTPS | Non |
| Point de terminaison | Adresse de service interne | Oui |
| Adresse d'accès publique | Adresse d'accès côté utilisateur | Oui |
| Règle de remplacement d'adresse | Regex ou chaîne utilisée pour mapper l'adresse interne à l'adresse publique | Oui |

#### 2.1.2 Paramètres du Bucket 

**Étapes opérationnelles**: Tous les Buckets retournés par le serveur seront rendus un par un, et vous pouvez remplir le CDN nom de domaine selon les besoins. 

| Champ       | Description                  |
| --------- | ------------------------- |
| Nom du bucket | Nom du Bucket         |
| Préfixe      | Préfixe du chemin de stockage d'objet  |
| CDN Domaine  | CDN Domaine d'accélération     |
| Activer CDN Authentification | Case à cocher, une fois activée, deux éléments "Type d'authentification" et "Clé d'authentification" seront ajoutés |

> Après avoir activé CDN l'authentification, le type d'authentification et la clé d'authentification correspondants sont requis. 

### 2.2 Redis
**Étapes**: Utilisez la navigation rapide à droite pour cliquer sur l' Redis icône pour faire défiler jusqu'à cette section → sélectionnez un mode → remplissez l'adresse et PASSWORD → cliquez sur "Tester la connexion". 

Description du champ :

| Champ     | Description                        |
| -----     | -----------------------------    |
| Mode      | Autonome ou Sentinel          |
| Adresse   | Adresse de connexion en mode autonome, par exemple, `redis-sentinel-master-ss:6379` |
| Nom du Master | Requis en mode Sentinel, par exemple, `mymaster` |
| Liste d'adresses | Adresses multiples en mode Sentinel, peuvent être ajoutées/supprimées dynamiquement |
| PASSWORD  | Requis                          |

Changer de mode réinitialisera automatiquement les champs Adresse, Nom du Maître et Liste d'adresses.

### 2.3 Kafka
**Étapes d'utilisation**: Cliquez sur l' Kafka icône dans la navigation rapide à droite pour faire défiler jusqu'à cette section → Remplissez l'adresse du courtier → Si SASL est activé, développez les SASL sous-champs → Cliquez sur "Tester la connexion".

Description du champ :

| Champ         | Description                                         |
| ---------- | ------------------------------------------------ |
| Adresse du courtier | Tableau, peut être ajouté/supprimé dynamiquement        |
| Préfixe du sujet    | Préfixe ajouté automatiquement à tous les sujets         |
| Activer SASL Authentification | Interrupteur, son activation ajoute trois SASL configurations |
| Mécanisme d'authentification | PLAIN / SCRAM-SHA-256 / SCRAM-SHA-512 (après activation SASL) |
| USERNAME / PASSWORD   | SASL Identifiants (après activation SASL)           |

### 2.4 MySQL (Base de données relationnelle)
**Étapes opérationnelles**: Dans la navigation rapide à droite, cliquez sur l' RDB icône et faites défiler jusqu'à cette section → sélectionnez MySQL ou DM Dameng → remplissez l'hôte, le port, USERNAMEPASSWORD → cliquez sur "Tester la connexion".

Description du champ :

| Champ      | Description        |
| -------- | ---------------- |
| Type de base de données | MySQL ou DM (Dameng) |
| Adresse de l'hôte  | Par exemple `mysql-master` |
| Port          | 3306           |
| USERNAME / PASSWORD | Identifiants      |

> Le “RDB Base de données relationnelle” dans le menu de droite et le titre de la page correspond à la MySQL section de configuration.

### 2.5 MongoDB
**Étapes opérationnelles**: Cliquez sur l' MongoDB icône dans la navigation rapide à droite pour faire défiler jusqu'à cette section → Remplissez la chaîne de connexion → Renseignez chaque identifiant de base de données un par un selon la configuration du serveur → Cliquez sur "Tester la connexion".

Description du champ : 

| Champ           | Description                       |
| ------------- | -------------------------------- |
| Chaîne de connexion | Par exemple `mongo-master:27017` |
| Chaque base de données USERNAME / PASSWORD | Renseignée une par une pour les bases de données configurées sur le serveur |

### 2.6 Elasticsearch
**Étapes :** Utilisez la navigation rapide à droite pour cliquer sur le Elasticsearch icône et faites défiler jusqu'à cette section → Remplissez l'adresse de l'hôte et le port → Si l'authentification est activée, remplissez USERNAME et PASSWORD → Cliquez sur 'Tester la connexion'.

Description du champ : 

| Champ     | Description       | Requis |
| ----      | --------------  | ----    |
| Adresse de l'hôte | par ex., `elasticsearch` | Oui      |
| Port        | 9200             | Oui      |
| USERNAME    | Identifiants ES   | Non       |
| PASSWORD    | Identifiants ES   | Non       |

---

## 3. Opérations courantes 

### 3.1 Mise à jour des identifiants (par exemple, rotation de la clé d'accès) 

1. Accéder à la configuration du middleware 
2. Remplacer l'ID de clé d'accès et le secret de clé d'accès dans la S3 carte publique 
3. Cliquer sur « Tester la connexion » et attendre le message vert « Test de connexion réussi » 
4. Répéter le test de connexion pour les autres sections modifiées 
5. Cliquez sur "Publier la configuration" en bas 
6. Le système indique qu'une tâche asynchrone a été créée et redirige vers l'onglet journal des tâches 

### 3.2 Changement de fournisseurs de middleware 

1. Accéder à la configuration du middleware 
2. Dans la carte correspondante, modifiez le "Type / Sous-type de stockage" ainsi que le point de terminaison, la clé d'accès, les règles de remplacement d'adresse, etc. du nouveau fournisseur 
3. Après modification, cliquez sur "Tester la connexion" pour vérifier 
4. Cliquez sur "Publier la configuration" 

> Le changement de fournisseur implique le rechargement du pool de connexions pour les services en aval, donc veuillez éviter les heures de pointe ; après le changement, il est recommandé de surveiller les journaux de l'application pendant 5 à 10 minutes 

### 3.3 Activer Kafka SASL 

1. Allez dans la configuration du middleware et localisez la Kafka section 
2. Activez l'interrupteur "Activer SASL l'authentification", et les SASL champs se développeront 
3. Remplissez le mécanisme d'authentification, USERNAME, et PASSWORD 
4. Cliquez sur "Tester la connexion" 
5. Après validation, cliquez sur "Publier la configuration" 

### 3.4 Récupération après une opération erronée 

Avant de cliquer sur "Publier la configuration", l'état du formulaire est enregistré dans le localStorage du navigateur. Il peut être récupéré de la manière suivante : 

- Cliquez sur le bouton "Tout réinitialiser" en bas, et tous les champs seront restaurés aux valeurs initiales du serveur 

### 3.5 Suivi des tâches asynchrones 

Après avoir publié la configuration avec succès, le système basculera vers l'onglet du journal des tâches pour afficher la progression des tâches. Les tâches peuvent être longues ou courtes, selon le nombre d'instances de middleware et le nombre de champs modifiés. 

--- 

## 4. Problèmes courants 

**Q1 : Cliquer sur Redis dans la navigation rapide en haut à droite n'a aucune réponse.**

La navigation rapide côté droit ne fait que faire défiler jusqu'à la section correspondante. Si cette section n'est pas sur la page actuelle (par exemple, bloquée par une fenêtre contextuelle), vous pouvez faire défiler la page ou cliquer à nouveau sur l' Redis icône dans la navigation côté droit pour repositionner.

**Q2 : Après avoir publié la configuration, le statut ne semble pas se mettre à jour.**

La page se rafraîchira automatiquement après la publication. Si le navigateur ne se rafraîchit pas automatiquement, vous pouvez appuyer manuellement sur F5 pour récupérer la dernière configuration.

**Q3 : Le nombre dans « N configurations modifiées » ne correspond pas au nombre réel.**

La page compte en fonction des champs ayant des valeurs modifiées dans le formulaire. Dans certaines situations, comme après une réinitialisation puis une modification, ou lors de l'ajout/suppression dynamique d'éléments d'un tableau, cela peut entraîner des écarts dans le comptage. Vous pouvez cliquer sur « Tout réinitialiser » et remplir à nouveau les entrées.

**Q4 : La carte des paramètres du Bucket ne peut pas trouver le Bucket que je veux ajouter.**

La page affiche les buckets existants en fonction de la configuration côté serveur. L'ajout d'un nouveau bucket nécessite de modifier le fichier de configuration serveur sous-jacent, pas cette page. Si cela est nécessaire, veuillez contacter l'ingénieur en charge de la mise en œuvre.

---

## Annexe A : Référence terminologique

| Terme       | Explication                                                                |
| -------- | ----------------------------------------------------------------- |
| Fichier de configuration serveur | La source de configuration finale pour la maintenance de la plateforme, formée en fusionnant les valeurs par défaut de la plateforme avec les valeurs définies sur cette page |
| Bucket   | Buckets de stockage dans S3 / OSS |
| Point de terminaison | Adresse du service middleware, utilisée pour l'accès interne au cluster |
| Adresse d'accès public | Adresse middleware visible par l'utilisateur |
| Règles de remplacement d'adresse | Mapper une adresse interne à une regex ou une chaîne d'une adresse publique |
| SASL     | Simple Authentication and Security Layer, mécanismes d'authentification pour des composants tels que Kafka |
| Sentinel | L'un de Redis |
| DM       | Dameng Base de données (base de données relationnelle domestique)                                             |
| Champ sale       | Champs dans le formulaire qui ont été modifiés et sont différents de la valeur initiale          |
