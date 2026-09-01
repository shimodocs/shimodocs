# Dépannage d'installation

[← ShimoDocs Suite documentation de déploiement](../README.md)

> [!TIP]
>
> Les problèmes courants pendant la phase d'installation tombent généralement dans les catégories suivantes.

## 1 Désynchronisation de l'heure

Symptômes du problème :

* Échec de connexion

* Erreurs d'authentification

* Exceptions lors des appels de service

Exigences de gestion :

* Vérifier d'abord la déviation de l'heure de tous les nœuds

* Après avoir corrigé le NTPservice de synchronisation de l'heure (/time synchronization), continuer l'installation ou l'acceptation

Commandes d'investigation :

```plain
timedatectl status
date
```


## 2 Erreur de configuration du chemin du disque de données

Phénomène :

* Après l'installation, le disque se remplit rapidement

* Échec de l'écriture des données

* Le répertoire persistant est situé sur le lecteur système

Exigences de traitement :

* Le répertoire persistant doit pointer explicitement vers le disque de données

* Les données métier ne sont pas stockées dans le répertoire du disque système

Commande de dépannage :

```plain
findmnt -n -o TARGET -T /data
df -Th|egrep -v "overlay|tmpfs"
```


## 3 Échec de la connexion du service de dépendance

Phénomène :

* L'inspection du service échoue pendant l'installation

* La connexion à la base de données, au cache, à la file de messages ou au stockage d'objets échoue

Exigences de traitement :

* Vérifiez d'abord si l'adresse, le port, le compte et PASSWORD sont saisis correctement

* Ensuite, vérifiez la connectivité réseau et les politiques de sécurité

* Enfin, vérifiez si le service cible lui-même est disponible

Commandes de dépannage :

```plain
nc -zv <MYSQL_HOST> 3306
nc -zv <REDIS_HOST> 6379
nc -zv <MONGO_HOST> 27017
nc -zv <KAFKA_HOST> 9092
```


## 4 Incompatibilité du paquet hors ligne

Phénomène :

* Échec du chargement du miroir

* Le processus d'installation indique que le service ne peut pas démarrer et que la version ne correspond pas

* Le package d'installation ne correspond pas au package miroir hors ligne

Exigences de traitement :

* Confirmer que le paquet d'installation, le paquet image hors ligne et la version du produit sont cohérents

* Confirmer si le paquet d'installation correspond à l' CPU architecture

* Confirmer que les matériaux de différents projets ou de différentes dates ne sont pas mélangés

## 5 La page de l'installateur ne peut pas être ouverte

Phénomène :

* La page Web UI ne peut pas être accessible

* Le port 18080 n'écoute pas

* Le processus de l'installateur s'est terminé

Commande de dépannage :

```plain
ps -ef | grep mdp | grep -v grep
ss -lntp | grep 18080
tail -n 100 /root/nohup.out
```


## 6. Ordre recommandé de dépannage

Résoudre les problèmes d'installation dans l'ordre suivant :

1. Tout d'abord, confirmer s'il s'agit d'un problème d'environnement : système, temps, disque, port, réseau

2. Reconfirmer s'il s'agit d'un problème de configuration : nom de domaine, répertoire, adresse de dépendance, compte PASSWORD

3. Reconfirmer s'il s'agit d'un problème de matériel : paquet d'installation, paquet hors ligne, compatibilité architecturale

4. Enfin, vérifier le journal de l'installateur et l'état d'exécution du service

Explication :

* Ne pas répéter l'installation si les prérequis ne sont pas remplis

* Ne pas exécuter des commandes avec la même raison d'échec explicite de manière répétée

## 7. Quand arrêter l'installation

Si les situations suivantes se produisent, arrêter d'abord l'installation et continuer uniquement après avoir corrigé les problèmes sous-jacents :

* Tous les temps de nœuds sont désynchronisés

* Le disque de données n'est pas monté de manière indépendante

* Le service externe dépendant est inaccessible

* La version du matériel hors ligne est incohérente

* Le service de l'installateur ne s'est pas correctement démarré
