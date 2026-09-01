# MySQL 8 Exigences

[← ShimoDocs Suite documentation de déploiement](../../README.md)

Ce document est destiné à guider le personnel de mise en œuvre, d'exploitation ou d'intégration pour compléter la ShimoDocs connexion à une MySQL initialisation de base de données 8, ainsi que le démarrage du service et la vérification de la connexion étape par étape.


## 1. Confirmation préopératoire

## MySQL Confirmation des spécifications de l'instance

| Version recommandée | Utilisateurs inférieurs à 3000 | Utilisateurs supérieurs à 3000 | 
| --- | --- | --- |
| MySQL 8.0 | 4C 8G 200G SSD | 8C 16G 200G SSD | 

## MySQL Configuration et exigences de haute disponibilité
Prend en charge la commutation de haute disponibilité maître-esclave
Jeu de caractères : utf8mb4 
Fuseau horaire : Asia/Shanghai ou UTC 
Connexions : max_connexions ≥ 1000
Utilisateur de connexion : privilèges administrateur

> [!TIP]
>
> Doit configurer une instance séparée MySQL ;
> 1. Pour atteindre l'isolation des pannes, la sécurité des permissions et la sauvegarde et récupération indépendantes, assurant le fonctionnement stable et efficace du système de documents.
> 2. Le système ne prend actuellement pas en charge les noms de bases de données et les préfixes de table personnalisés, donc la planification et la préparation d'une instance séparée doivent être complétées avant le déploiement.





## Connectivité réseau 
Les ports pour connecter le réseau du cluster métier k8s à l' MySQL instance doivent être ouverts 

```js
telnet IP 3306
```
## Authentification de l'utilisateur
Le MySQL L'utilisateur fourni doit être authentifié lors de la connexion au MySQL serveur

# Explication : 
- Le MySQL Les configurations dans le document sont toutes des paramètres recommandés, utilisés pour l'évaluation de la capacité du projet en phase initiale et la planification des ressources, et ne sont pas des configurations finales de production. La configuration finale réelle sera déterminée après l'évaluation présales.
- Lors de l'utilisation de serveurs avec une CPU architecture nationale, il est recommandé d'estimer les ressources globales au double de la spécification standard.
- Il est recommandé de réserver de la capacité pour l'expansion dans l'environnement de production formel et de prioriser le déploiement haute disponibilité.
