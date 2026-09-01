# Redis Configuration

[← ShimoDocs Suite documentation de déploiement](../../README.md)

Ce document est destiné à guider les implémenteurs, le personnel opérationnel ou les intégrateurs pour compléter l'intégration de ShimoDocs avec des systèmes externes Redis pas à pas. Il est généralement utilisé pour des scénarios principaux tels que la gestion des sessions, les verrous distribués, les compteurs de limitation de débit et les files de messages

## 1. Confirmation préopératoire

## Redis Exigences de l'instance


| Middleware | Version recommandée | Pour moins de 3000 utilisateurs | Pour plus de 3000 utilisateurs |
| --- | --- | --- | --- |
| Redis | Redis 6.2.21 | 2C 4G 100G SSD | 2C 8G 100G SSD |


## Exigences de configuration du cluster
- Prise en charge de la haute disponibilité maître-esclave/sentinelle
- Persistance des données
- Le mode cluster n'est pas pris en charge
- Le nombre de bases de données doit être >= 64





## Connectivité réseau

Les ports pour connecter le K8s réseau du cluster professionnel à l' Redis instance doivent être ouverts

```js
telnet IP 6379
```

## Authentification et Autorisation
- Dans les environnements de production, il est recommandé d'activer PASSWORD l'authentification (requirepass / ACL).


## Autres exigences
- Réseau interne P99 la latence doit être < 10ms
- Synchronisation de l'horloge : Redis les nœuds du cluster et ShimoDocs les serveurs d'application doivent être NTP synchronisés
- Sauvegardes complètes régulières
