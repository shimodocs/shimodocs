# Recherche d'événements de transcodage

[← ShimoDocs Suite documentation de déploiement](../../../README.md)

## Aperçu des fonctionnalités 
La fonction de requête des événements de transcodage est utilisée pour vérifier rapidement les événements de transcodage récents dans le MDP backend, aidant à localiser et dépanner les problèmes pendant le processus de transcodage. 

Par défaut, la liste affichera les événements de transcodage les plus récents. 

## tâche_acquisition d'identifiant
Un identifiant de tâche_est généré lors des tâches d'importation et d'exportation

Ouvrez le mode développeur du navigateur. Lors de l'exportation, vous pouvez obtenir l'identifiant de la tâche_en vérifiant cette interface comme montré dans la figure ci-dessous

## Rechercher par tâche_id
Entrez la tâche_id dans la zone de saisie taskID pour filtrer rapidement les événements de transcodage liés à cette tâche.

## Voir le lien
Comme indiqué dans la figure ci-dessous, cliquez sur l'icône "Voir le lien" dans l'enregistrement de la ligne pour voir tous les événements liés à la tâche_id, ce qui facilite l'analyse du processus complet de cette tâche de transcodage du début à la fin.

## Localisation des exceptions

### gRPC Réussi, rappel non reçu
Si gRPC est envoyé avec succès et une réponse est reçue avec succès, cela indique que la tâche de transcodage a été envoyée au service de transcodage. Dans ce cas, si le rappel n'est pas reçu à temps en raison d'un délai d'attente du service de transcodage, le service de transcodage doit être examiné.

### Rappel reçu
Si vous pouvez voir qu'il y a un rappel pour la tâche_id, alors il est généralement considéré comme un échec de transcodage, tel que des formats incompatibles ou d'autres exceptions.
