# MongoDB Configuration

[← ShimoDocs Suite documentation de déploiement](../../README.md)

Cet article est destiné à guider le personnel de mise en œuvre, d'exploitation ou d'intégration pour compléter l'intégration de ShimoDocs avec des systèmes externes MongoDB pas à pas

## 1. Confirmation avant l'exploitation

## MongoDB Exigences de l'instance


| Middleware | Version recommandée | Moins de 3000 utilisateurs | Plus de 3000 utilisateurs |
| --- | --- | --- | --- |
| MongoDB | MongoDB 4.4 | 2C 8G 100G SSD | 4C 16G 100G SSD |


## Exigences de configuration du cluster
- Prise en charge des clusters à haute disponibilité avec jeu de réplicas, au moins 3 nœuds sont obligatoires dans les environnements de production
- Il est recommandé d'activer SCRAM-SHA-256 authentification





## Connectivité réseau

Les ports pour K8s les clusters d'entreprise à accéder MongoDB doit être ouvert

```js
telnet IP 27017
```

## Authentification et Autorisation
- Dans les environnements de production, il est recommandé de faire respecter SCRAM-SHAl'authentification -256.


## Autres exigences
- Réseau interne P99 latence de lecture < 5 ms, latence d'écriture < 10 ms
- Disque IOPS doit répondre aux exigences de pics d'écriture ; SSD est obligatoire
- Synchronisation de l'horloge : MongoDB les nœuds du cluster et ShimoDocs les serveurs d'application doivent être NTP synchronisés
- Sauvegardes complètes régulières et sauvegardes Oplog continues
