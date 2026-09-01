# Journaux des services

[← ShimoDocs Suite documentation de déploiement](../../../README.md)

## Aperçu des fonctionnalités 
La fonctionnalité de journaux de service est une plateforme de récupération de journaux similaire à Kibana qui peut collecter les journaux des Pods de différents ShimoDocs services et fournit des capacités de recherche, de requête et d'analyse proportionnelle des journaux. 

## Entrée et Navigation
Menu latéral gauche : Services Système --> Opérations de Service --> Journaux de Service

## SQL Mode
La boîte de saisie prend en charge les requêtes syntaxiques ClickHouse SQL Après la saisie SQL, vous pouvez exécuter la requête en mode brut ClickHouse.

Comme indiqué dans la figure ci-dessous, saisissez

``` sql
`_raw_log_` like '%access%'
```

peut être utilisé pour interroger tous les journaux contenant access. 

## Filtrage Conditionnel
Comme indiqué dans la figure ci-dessous, cliquez sur le bouton "Ajouter condition" pour ajouter une nouvelle condition de filtre.

## Analyse proportionnelle
Comme indiqué dans la figure ci-dessous, cliquez sur l'icône à côté d'un champ dans l'enregistrement de la ligne pour ouvrir le menu déroulant. Après avoir sélectionné "Valeurs principales", vous pouvez voir la proportion de ce champ dans la plage horaire actuelle dans le coin supérieur droit.

## Description du champ

| Champ intégré | Description |
| --- | --- |
| lv | Le niveau d'erreur du journal, y compris info, error, warn |
| container.name | CONTAINER_NAME |
| method | La méthode dans le journal d'accès; gRPC affiche le gRPC method, HTTP affiche le API path |
| peerIP | L'adresse IP du pair |
| nomDuPair | Le nom du pair, tel que le nom du service, etc. |
| composant | Le composant dans le journal d'accès, tel que server.begin |
| coût | Le temps consommé dans le journal d'accès, en millisecondes |

## Analyse de Cas
### Rechercher tous les journaux d'erreurs du jour

Dans Ajouter une condition, sélectionnez le champ lv et ajoutez lv = erreur comme montré dans l'image ci-dessous

### Voir les journaux de requêtes

    1. Utilisez `msg`='access' pour voir tous les journaux de requêtes, y compris HTTP et gRPC
    
2. Voir HTTP requêtes

3. Voir gRPC requêtes

