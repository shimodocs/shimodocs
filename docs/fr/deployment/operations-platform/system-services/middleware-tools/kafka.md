# Kafka Outils

[← ShimoDocs Suite documentation de déploiement](../../../README.md)

> [!TIP]
>
> Le Kafka l'outil vous permet de voir Kafka l'état du cluster, les topics, les messages, les groupes de consommateurs et les informations de partition via la Console Redpanda, couramment utilisé pour dépanner l'écriture de messages, le retard des consommateurs et les problèmes de lien asynchrone.
>
> Une fois la page chargée avec succès, la Console Redpanda sera intégrée dans MDP.

## 1. Accès Kafka

1. Connectez-vous à la **MDP Plateforme d'Opérations**.
2. Sélectionner **Services Systèmes** en haut.
3. Développer **Outils Middleware** dans la barre de navigation gauche.
4. Sélectionner **Kafka**.
5. Attendez que la Console Redpanda ait fini de se charger.

Si la console n'est pas prête, la page indiquera qu'elle est en cours de démarrage ou que le démarrage a échoué et affichera des informations sur l'erreur.

## 2. Voir l'aperçu du cluster

Après être entré Kafka, **Aperçu** s'affiche par défaut.

Vous pouvez consulter les informations suivantes :

| Informations | Description |
| --- | --- |
| État du cluster | État de fonctionnement du cluster. |
| Taille de stockage du cluster | Taille de stockage actuelle du cluster. |
| Version du cluster | Informations sur la version du cluster. |
| Brokers en ligne | Nombre de brokers en ligne. |
| Sujets | Nombre de sujets. |
| Réplicas | Nombre de réplicas. |
| Détails du broker | ID du broker, état et taille. |

## 3. Voir les sujets

1. Dans la navigation de gauche de la console Redpanda, sélectionnez **Sujets**.
2. Trouvez le sujet cible dans la liste des sujets.
3. Cliquez sur le sujet pour entrer dans la page de détails.
4. Consultez des informations telles que les partitions, les messages et la configuration du sujet.

Le dépannage des sujets se concentre généralement sur :

| Informations | Description |
| --- | --- |
| Partitions | L'état des partitions du sujet. |
| Messages | La liste des messages du sujet. |
| Configuration | Configuration du sujet, telle que la politique de rétention. |

## 4. Voir les messages

1. Entrez dans le sujet cible.
2. Ouvrez la zone de visualisation des messages.
3. Sélectionnez la partition, la position ou la plage de temps à l'aide des filtres fournis sur la page.
4. Consultez la clé du message, la valeur, les en-têtes, la partition, le décalage et l'horodatage.

> Le contenu des messages peut contenir des champs métiers. Lors du dépannage, privilégiez la localisation par ID métier, clé, décalage et horodatage.

## 5. Voir les groupes de consommateurs

1. Dans la console Redpanda, sélectionnez **Groupes de consommateurs** à partir de la navigation de gauche.
2. Recherchez ou sélectionnez le groupe de consommateurs cible.
3. Entrez les détails du groupe de consommateurs.
4. Visualisez les Topics associés au groupe de consommateurs, les partitions, l'Offset actuel, l'Offset de fin du journal et le retard (Lag).

## 6. Détermination du retard du consommateur

| Statut | Description |
| --- | --- |
| Le retard est de 0 | Le groupe de consommateurs actuel n'a aucun retard. |
| Le retard augmente continuellement | La vitesse de consommation est inférieure à la vitesse de production. |
| Le retard ne change pas mais n'est pas de 0 | Il peut y avoir un consommateur arrêté, un blocage de partition ou une erreur de consommation. |
| Le retard d'une seule partition est significativement élevé | Cela peut être dû à une clé chaude ou à une consommation anormale dans cette partition. |

## 7. Visualiser les Brokers

1. Sur la page d'aperçu, trouvez **Détails du Broker**.
2. Vérifiez l'ID du Broker, le statut en cours d'exécution et la taille de stockage.
3. Cliquez **Afficher** pour voir les détails du broker.

## 8. Scénarios courants de dépannage

| Scénario | Suggestions opérationnelles |
| --- | --- |
| Confirmez si Kafka fonctionne normalement | Vérifiez le statut du cluster et les brokers en ligne dans l'aperçu. |
| Confirmez si des messages sont écrits | Allez sur le Topic et vérifiez les messages. |
| Dépannez les retards du consommateur | Allez dans les Groupes de consommateurs et vérifiez le retard (Lag). |
| Localisez un seul message | Recherchez par Topic, Partition, Offset, Clé ou horodatage. |
| Confirmez la configuration du Topic | Allez dans les détails du Topic et vérifiez la Configuration. |
