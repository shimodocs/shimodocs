# Kafka Configuration

[← ShimoDocs Suite documentation de déploiement](../../README.md)

Ce document est destiné à guider le personnel de mise en œuvre, d'exploitation ou d'intégration pour compléter la ShimoDocs intégration avec le middleware de message externe Kafka étape par étape, pour des scénarios tels que le traitement asynchrone des tâches, les notifications de messages, la synchronisation des données et la livraison des journaux d'audit. 


## 1. Confirmation pré-opération 

## Kafka Exigences de l'instance 


| Middleware | Version recommandée | Pour moins de 3000 utilisateurs | Pour plus de 3000 utilisateurs | 
| --- | --- | --- | --- | 
| Kafka | Kafka 3.5 | 2C 4G 300G SSD | 4C 8G 300G SSD | 


## Exigences de configuration 
- Broker >= 3 
- Facteur de réplication : le nombre par défaut de répliques est de 3, dans un environnement de production il est obligatoire d'avoir ≥ 3 pour garantir une haute disponibilité 
- Rétention des messages : 72 heures (peut être ajustée selon les besoins de l'entreprise) 
- Taille maximale d'un message unique par sujet : 10 Mo 
- Authentification : prise en charge SASL l'accès chiffré (PLAIN, SCRAM-SHA-256, SCRAM-SHA-512) 





## Connectivité réseau 

Les ports pour accéder Kafka aux instances depuis le K8s cluster d'entreprise doivent être ouverts 

```js
telnet IP 9092
```



## Autres exigences
- Intranet RTT est recommandé d'être < 5ms ; inter-datacenter / inter-région est recommandé d'être < 20ms.
- La bande passante doit répondre au débit maximal pour éviter l'accumulation de messages causée par la saturation du réseau.
- Assurez-vous que le Kafka Courtier et ShimoDocs le temps du serveur d'application est synchronisé (NTP), car la déviation temporelle peut affecter l'ordre des messages et TTL calcul.
