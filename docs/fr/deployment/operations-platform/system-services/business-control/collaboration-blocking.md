# Blocage de collaboration

[← ShimoDocs Suite documentation de déploiement](../../../README.md)

## Description de la fonction

Lorsqu'un arriéré se produit dans Kafka, et qu'il est confirmé que l'arriéré anormal est causé par un certain fichier, vous pouvez utiliser cette fonction de blocage pour interdire la modification de ce fichier, résolvant ainsi le problème d'arriéré Kafka .

## Illustration d'utilisation

1. Sélectionnez blocage collaboratif 

2. Entrez le guid du fichier, à noter : cela fait référence au guid à l'intérieur de ShimoDocs, pas à l'id du fichier du client 

Saisissez l'adresse ShimoDocs fichier GUID et cliquez sur 'Ajouter au blocage'; le fichier sera interdit de modification sous 3 minutes. 

Cliquez sur le bouton 'Débloquer' pour restaurer la fonctionnalité de modification du fichier 

### Comment obtenir le GUID 

1. Ouvrez les outils de développement du navigateur 

2. Filtrer les requêtes de récupération 

3. Dans la requête, la chaîne de 16 caractères provenant de rp3OMYnMrdcQJZkm est le GUID 

### Comment déterminer l'effet du blocage

Le document ne peut pas être sauvegardé avec succès ; après avoir modifié le fichier, une fenêtre contextuelle hors ligne apparaît après 2 minutes, et les données sont perdues après le rafraîchissement de la page.

### Quand débloquer

Il n'est pas recommandé de débloquer. En général, cela est dû au fait que le fichier est trop volumineux pour que le serveur prenne en charge la modification. Après avoir été bloqué, il devient en lecture seule. Il est recommandé de copier manuellement le contenu du fichier dans un nouveau fichier.
