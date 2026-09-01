# Inspection des middlewares

[← ShimoDocs Suite documentation de déploiement](../../../README.md)

## Vue d'ensemble des fonctions 

L'inspection du middleware est utilisée pour vérifier si le système dépendant MySQL, Redis, Elasticsearch, S3, MongoDB, et Kafka peut se connecter et lire/écrire normalement, vous aidant à détecter rapidement les anomalies du service sous-jacent. 

La page prend en charge l'inspection instantanée, l'inspection planifiée, les tendances de disponibilité récentes, les enregistrements historiques, ainsi que les notifications d'échec et de récupération. 

## Entrée sur la page 

Après vous être connecté à l'interface de gestion, sélectionnez **Inspection des middlewares** dans la navigation de gauche pour entrer sur la page. 

L'inspection du middleware est uniquement ouverte aux administrateurs. Si vous ne voyez pas ce menu, veuillez contacter l'administrateur système pour confirmer les permissions de votre compte. 

## Inspection instantanée 

Sur la page **Aperçu** page, cliquez **Inspection instantanée**, et le système effectuera une inspection selon les objets de vérification enregistrés. 

Les résultats de l'inspection peuvent inclure les statuts suivants : 

- **Normal**: La connexion des composants et les opérations d'inspection ont réussi. 
- **Échoué**: Le composant ne peut pas être connecté, la lecture/écriture a échoué, ou la réponse est anormale. 
- **Ignoré**: Le composant n'est pas configuré dans l'environnement actuel, ou les conditions d'inspection ne sont pas remplies. 

Cliquer sur les résultats du composant vous permet de voir l'adresse cible, le temps de réponse et les détails des erreurs. 

## Voir les tendances de disponibilité 

La page d'ensemble affiche la disponibilité récente de chaque composant basée sur les résultats d'inspection historiques. Elle prend en charge la visualisation des changements de statut au cours de la dernière heure, des 6 heures, 24 heures, 3 jours, 7 jours, 14 jours ou 30 jours. 

Déplacez la souris sur la période pour afficher des informations telles que le nombre d'inspections pendant cette période, le temps de réponse moyen et les erreurs récentes. 

## Configurer les inspections planifiées 

Sur la page **Planification et alertes** sur cette page, vous pouvez définir : 

- **Activer les inspections planifiées**: Une fois activé, le système exécutera automatiquement aux intervalles définis. 
- **Intervalle d'inspection**: Prise en charge de 1 à 1440 minutes. 
- **Durée de conservation de l'historique (en jours)**: Prise en charge de 7 à 365 jours ; le régler sur `0` signifie aucune suppression automatique. 
- **Cibles d'inspection**: Sélectionnez le middleware à inspecter. 
- **Canaux de notification**: Choisissez les canaux pour recevoir les notifications d'inspection. 
- **Notifier en cas d'échec**: Envoyer une notification lorsque le statut global passe de normal à anormal. 
- **Notifier lors de la récupération**: Envoyer une notification lorsqu'un statut anormal redevient normal. 

Les modifications doivent être appliquées en cliquant sur **Enregistrer**. Si aucun canal de notification n'existe encore, veuillez aller sur la page **Canal de notification** pour créer et activer d'abord un canal.

## Voir l'historique des inspections

Sur la page **Historique** sur cette page, vous pouvez voir l'heure de l'inspection, le mode de déclenchement, la durée d'exécution et le statut final.

Les modes de déclenchement incluent l'inspection manuelle et l'inspection planifiée. Cliquez sur un enregistrement pour voir les résultats détaillés de chaque composant lors de cette inspection. 

## Situations courantes

- **Aucun enregistrement d'inspection**: Vous pouvez d'abord cliquer sur **Inspecter maintenant**, ou activer l'inspection planifiée.
- **Le composant indique sauté**: Veuillez confirmer que le middleware correspondant a été configuré et activé dans le système.
- **Échec de l'inspection**: Vérifiez le réseau, le compte, l'adresse de connexion et l'état du service middleware selon les détails de l'erreur.
- **Notification non reçue**: Veuillez confirmer que le canal de notification a été sélectionné et activé, et vérifiez les commutateurs de notification de défaillance ou de récupération.
- **L'invite indique que l'inspection est en cours**: Une seule tâche d'inspection peut être exécutée à la fois, veuillez attendre la fin de la tâche actuelle et réessayer.

> Les inspections effectueront des tests de connexion légers ou des vérifications de lecture/écriture sur le middleware ; il est recommandé de définir un intervalle d'inspection raisonnable en fonction de l'échelle de l'environnement.
