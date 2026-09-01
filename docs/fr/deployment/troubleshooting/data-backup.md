# Sauvegarde des données

[← ShimoDocs Suite documentation de déploiement](../README.md)

Ce document explique la portée de la sauvegarde des données, les exigences en matière de récupération, les méthodes d'exécution et les éléments de vérification après récupération pour le ShimoDocs environnement privatisé.

Ce document couvre les contenus suivants :

* Portée et limites de responsabilité de la sauvegarde

* Exigences de sauvegarde et de récupération de la base de données

* Exigences de sauvegarde et de récupération du stockage d'objets

* Éléments de confirmation avant récupération

* Éléments de vérification après récupération

Ce document ne couvre pas les contenus suivants :

* Étapes initiales d'installation et de déploiement

* Plans de mise à niveau et de migration

* Instructions des outils de récupération spécifiques aux fournisseurs de middleware tiers

* Processus de gestion des incidents en production

# 2. Portée de la sauvegarde et limites de responsabilité

## 2.1 Portée de la sauvegarde

Les données qui doivent être incluses dans le périmètre de la sauvegarde dans le ShimoDocs L'environnement privatisé comprend :

* MySQL données

* MongoDB données

* Redis données

* données de stockage d'objets 

* fichiers de configuration d'installation et de paramètres d'environnement 

Les répertoires de données, les répertoires de sauvegarde et les périodes de conservation des sauvegardes sont gérés de manière uniforme par le côté client. 

## 2.2 Limites de responsabilité 

Les limites des responsabilités de sauvegarde et de récupération sont les suivantes : 

* Le côté client est responsable de la formulation et de l'exécution des politiques de sauvegarde formelles 

* Le côté client est responsable de la conservation des fichiers de sauvegarde, de la sécurité des supports et de la gestion des périodes de rétention 

* Le côté client est responsable des exercices de récupération, de l'approbation de la récupération et de l'acceptation des résultats de récupération 

* ShimoDocs peut fournir un support technique et des conseils sur l'opération de récupération 

Lorsque des middleware externes, un stockage d'objets auto-construit ou une infrastructure maintenue par le client sont impliqués, la stratégie de sauvegarde et de récupération est entièrement prise en charge par le client. 

# 3. Confirmation avant l'exécution de la récupération 

La récupération des données est une opération à haut risque. Les confirmations suivantes doivent être effectuées avant l'exécution. 

## 3.1 Confirmation de la cible 

Avant la récupération, clarifiez les informations suivantes : 

* Environnement cible 

* Cluster cible, nœuds, NAMESPACE 

* Portée des données à récupérer 

* Point de récupération dans le temps 

* Fenêtre d'exécution 

## 3.2 Confirmation des risques

Confirmez les points suivants avant la récupération :

* Si cette récupération écrasera les données en ligne actuelles

* Si cette récupération nécessite une interruption de service

* Si la dernière sauvegarde a été ajoutée aux données en ligne actuelles

* Si le point de restauration après une récupération échouée a été clarifié

## 3.3 Confirmation de la validité de la sauvegarde

Vérifiez ce qui suit avant la récupération :

* Les fichiers de sauvegarde sont complets et lisibles

* Le point temporel de la sauvegarde répond aux objectifs de récupération

* Le répertoire de sauvegarde est correctement monté

* Tous les fichiers de configuration requis pour la récupération sont complets

* Les fichiers de sauvegarde ont passé la vérification de récupérabilité

# 4. Stratégie de sauvegarde

## 4.1 Sauvegarde de la base de données

Les critères de sauvegarde de la base de données sont les suivants :

|Scénario|Méthode d'exécution|Fréquence|Période de rétention|Description|
|:----|:----|:----|:----|:----|
|En utilisant ShimoDocs le middleware intégré|Sauvegarde planifiée par le système|Une fois par jour|7 jours|Exécuté par des tâches planifiées au sein du cluster|
|Utiliser un middleware auto-maintenu par le client|Sauvegarde côté client|Une fois par jour ou plus|7 jours ou plus|Exécuter selon la politique du côté client|



La sauvegarde de la base de données doit couvrir au minimum :

* MySQL

* MongoDB

* Redis

## 4.2 Sauvegarde du stockage d’objets

Les critères de sauvegarde du stockage d’objets sont les suivants :

|Type de données|Méthode d'exécution|Fréquence|Période de rétention|Description|
|:----|:----|:----|:----|:----|
|Données métier du stockage d’objets|Sauvegarde froide ou réplication de reprise après sinistre|Exécuter selon le niveau d’activité|Exécuter selon la politique client|Couvre les pièces jointes de documents et les objets de fichiers|
|Données de configuration du stockage d’objets|Sauvegarde de configuration|Sauvegarde synchronisée après modifications|Exécuter selon la politique client|Couvre les paramètres d’accès et les informations de montage|



Les copies multiples dans le stockage d’objets font partie du mécanisme de redondance du cluster et ne sont pas équivalentes à une sauvegarde de données.

## 4.3 Sauvegarde des fichiers de configuration

Les configurations suivantes sont incluses dans le périmètre de la sauvegarde :

* Paramètres d’installation

* Configuration de domaine et de protocole

* Adresses de dépendances externes et informations de port

* Informations d’accès au stockage d’objets 

* Fichiers de configuration liés à l’activité 

# 5. Récupération de la base de données 

Cette section s’applique à toute récupération de données pour MySQL, MongoDB, et Redis. 

## 5.1 Préparation avant la récupération 

Effectuer les préparations suivantes avant de réaliser la récupération de la base de données : 

* Préparer un répertoire de récupération sur le nœud cible, par exemple, `/data/restore` 

* Placer les données à récupérer dans le répertoire de récupération 

* Vérifier que la configuration du middleware dans le `global_config.json` fichier correspond à l’environnement actuel 

* Vérifier le nœud de récupération, le point de récupération, la fenêtre d’exécution et les informations d’approbation 

## 5.2 Vérification des tâches de sauvegarde 

Vérifier les tâches de sauvegarde de base de données programmées : 

```plain
kubectl get cronjob
```


Enregistrez également les informations suivantes :

* Nom du CronJob

* Dernière heure d'exécution

* Résultat de la dernière exécution

* Répertoire de stockage des fichiers de sauvegarde

## 5.3 Reprise de l'exécution

La récupération de la base de données est effectuée via un travail unique, et le script de récupération se trouve dans l'image de sauvegarde.

Les étapes d'exécution sont les suivantes :

1. Préparer la liste des tâches de restauration `db-restore.yaml`

2. Modifier `spec.template.spec.nodeName` vers le nœud où se trouve le répertoire de récupération

3. Modifier `hostPath.path` vers le répertoire où les données sont restaurées

4. Exécuter la `kubectl apply -f db-restore.yaml` commande pour effectuer la restauration des données

La liste de tâches exemple est la suivante :

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  labels:
    job-name: db-restore
  name: db-restore
spec:
  template:
    metadata:
      labels:
        job-name: db-restore
      name: db-restore
    spec:
      containers:
      - command:
        - /bin/sh
        - -c
        - cd /data/pri-init/scripts/backup && sh restore_all.sh
        image: registryo.shimo.im/smbase/backup:co
        imagePullPolicy: Always
        name: db-restore
        volumeMounts:
        - name: db-config
          mountPath: /data/pri-init/scripts/global_config.json
          subPath: global_config.json
        - name: data
          mountPath: /backup
      dnsPolicy: ClusterFirst
      nodeName: master-1
      volumes:
      - name: db-config
        configMap:
          name: init-invoker
          items:
          - key: global_config.json
            path: global_config.json
      - name: data
        hostPath:
          path: /data/restore
      imagePullSecrets:
      - name: ee
      restartPolicy: Never
      schedulerName: default-scheduler
```


## 5.4 Instructions d'exécution

Après l'exécution de la tâche de récupération de la base de données, les données suivantes seront restituées :

* MySQL

* MongoDB

* Redis

Pendant la période de récupération, les données commerciales peuvent être écrasées. Prévoir une fermeture complète et une vérification des données avant l'exécution.

# 6. Récupération de stockage d'objets

Cette section s'applique à MinIO et S3récupération de stockage d'objets compatible avec

## 6.1 Méthodes de sauvegarde

Les méthodes de sauvegarde courantes pour le stockage d'objets sont les suivantes :

|Méthode|Scénario applicable|Description|
|:----|:----|:----|
|Copie synchronisée Rsync|Environnement autonome|Convient pour une sauvegarde à froid au niveau des répertoires|
|Snapshot de disque|Environnement autonome|Convient pour une récupération rapide sur la même plateforme de stockage|
|`mc mirror`|Environnement autonome ou clusterisé|Convient pour la sauvegarde à froid et la récupération des données objets|
|Réplication de site / Réplication de bucket|Environnement clusterisé|Convient pour la réplication de reprise après sinistre|



## 6.2 Reprise de l'exécution

Les méthodes de récupération couramment utilisées dans un environnement autonome sont les suivantes :

* Lors de l'utilisation de Rsync pour la sauvegarde, effectuez une synchronisation inverse pour restaurer le répertoire de données

```plain
rsync -av backup:/data/minio/ /data/minio/
```


* Lors de l'utilisation `mc mirror` pour la sauvegarde, effectuez une restauration miroir inverse

```plain
mc mirror backup-minio/ new-minio/
```


Les directives de récupération pour l'environnement de cluster sont les suivantes :

* Lorsqu'une copie de reprise après sinistre existe, effectuez la récupération conformément au plan de basculement primaire-secondaire

* Lors de l'utilisation de la sauvegarde à froid, effectuez la récupération conformément au répertoire de données du stockage d'objets ou au contenu du dépôt d'images

## 6.3 Instructions d'exécution

Avant de restaurer le stockage d'objets, les points suivants doivent être confirmés :

* Plage du bucket cible à restaurer

* Point de récupération

* Si l'objet en ligne doit être écrasé

* Chemin de stockage cible et configuration des permissions

* ACCESS_DOMAIN et configuration de la passerelle après récupération

# 7. Vérification après récupération

Une fois la récupération terminée, vérifiez au minimum les points suivants :

* Le statut du service de base de données est normal

* Le statut du service de stockage d'objets est normal

* Gestion possible via le panneau d'administration

* La connexion des utilisateurs est normale

* Les documents principaux peuvent être créés, modifiés, enregistrés, importés et exportés normalement

* Le point de récupération des données correspond aux attentes

Enregistrez les informations suivantes après la fin de la récupération :

* Heure d'exécution de la restauration

* Point de temps de récupération des données

* Exécutant, Approbateur, Inspecteur

* Problèmes constatés après récupération
