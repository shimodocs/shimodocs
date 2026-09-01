# Réparation de documents

[← ShimoDocs Suite documentation de déploiement](../../../README.md)

Les symptômes d'un fichier gravement endommagé incluent le fait que le document ne s'ouvre pas correctement, des fenêtres d'erreur apparaissent lors du chargement et le contenu ne s'affiche pas.

Lorsque le document ne peut pas être ouvert, utilisez cette fonction pour réparer le fichier

# Illustration d'utilisation

Il existe 2 méthodes de réparation

## Préparations

### Référence rapide pour les types de fichiers

|**Type de fichier**|**Fichier URL Caractéristiques d'adresse**|**Remarques**|
|:----|:----|:----|
|rdoc(richdoc)|/**docs**/{fileguid}|Document léger|
|mosheet(modoc)|/**feuilles**/{fileguid}|Tableau|
|modoc(modoc)|/**docx**/{fileguid}|Document professionnel|

### Avertissement sur le risque opérationnel

L'échec de la réparation n'entraîne aucun risque

## Récupération à partir de données chiffrées

Prend en charge uniquement la réparation des fichiers de type tableau. Pour les autres types de fichiers, sélectionnez [Réparer à partir des données historiques]

C'est la méthode recommandée. Vous pouvez entrer directement dans le fichier GUID pour effectuer la réparation. Ceci GUID est le ShimoDocs fichier GUID.

Le principe de la réparation consiste à convertir les données de fichiers chiffrés dans le stockage d'objets en données de contenu de fichiers non chiffrées, applicable à la plupart des scénarios.

Si cette méthode échoue à réparer, choisissez alors une autre méthode.

### Fichier GUID

1. Ouvrez les outils de développement du navigateur

2. Filtrer les pull requests

3. Dans la requête, la partie rp3OMYnMrdcQJZkm, cette chaîne de 16 caractères, est le guid

## Restaurer à partir des données historiques

Restaurer à partir des archives historiques

1. ID du fichier client

2. Type de fichier

   1. Pour les documents/tableurs/présentations traditionnels, choisissez modoc

   2. Pour les documents légers, choisissez richdoc

1. Sélectionner la source de données

### ID du fichier client

Si le client utilise ShimoDocs pour l'ensemble du site, il s'agit de l'adresse du fichier du document dans le navigateur, par exemple, le suivant m8AZMoYMrRsYbOkb

### Comment déterminer la source de données 

Vérifiez la configuration du service svc-edit 

Élément de configuration : history.driver 

Si c'est mysql, l'interrupteur « Utiliser la source de données Mongo » est désactivé 

Si c'est mongo, l'interrupteur « Utiliser la source de données Mongo » est activé 

