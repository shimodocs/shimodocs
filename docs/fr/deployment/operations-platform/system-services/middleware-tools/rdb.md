# RDB Outils

[← ShimoDocs Suite documentation de déploiement](../../../README.md)

> [!TIP]
>
> RDB est utilisé pour visualiser et dépanner les données de base de données relationnelle dans la plateforme d'opérations et est couramment utilisé pour confirmer les enregistrements commerciaux, les enregistrements de configuration, les statuts de tâches, les journaux d'opération et d'autres données structurées.
>
> Avant d'utiliser, veuillez vous assurer que votre compte actuel dispose des autorisations de l'outil middleware et que l'environnement de déploiement correct est sélectionné.

> Le RDB L'outil accède directement aux données de la base de données. Avant de faire une requête, veuillez confirmer les tables et les conditions de filtrage pour éviter d'exécuter des requêtes coûteuses ou de mal manipuler les données de production.

## 1. Accès RDB

1. Connectez-vous à **MDP Plateforme d'Opérations**.
2. Sélectionner **Services Systèmes** en haut.
3. Développer **Outils Middleware** dans le panneau de navigation à gauche.
4. Sélectionner **RDB**.

La page comprend généralement des zones pour la connexion à la base de données, la sélection de tables, SQL la saisie et les résultats de la requête.

## 2. Sélectionner une connexion à la base de données

1. Sur la page RDB , sélectionnez l'instance de base de données à laquelle vous devez accéder.
2. Confirmer que le nom de l'instance, l'adresse de la base de données ou l'identifiant de l'environnement correspond à la cible actuelle du dépannage.
3. Sélectionner la base de données cible.
4. Développer la liste des tables et confirmer que la table cible existe.

> Si l'instance de base de données est vide ou si la connexion échoue, veuillez d'abord vérifier la configuration du middleware, les permissions du compte et la connectivité réseau.

## 3. Visualisation de la structure de la table

1. Sélectionnez la table cible dans la liste des tables à gauche.
2. Visualisez les noms des champs, les types de champs, les clés primaires et les informations sur les index.
3. Confirmez les conditions de requête ultérieures en fonction de la signification des champs.

Il est recommandé de se concentrer sur les informations suivantes :

| Informations | Description |
| --- | --- |
| Clé primaire | Utilisée pour interroger précisément un seul enregistrement. |
| ID de l'entreprise | Tel que l'ID de locataire, l'ID utilisateur, l'ID de tâche, l'ID de fichier. |
| Champ de statut | Utilisé pour confirmer l'état actuel du flux de l'entreprise. |
| Champ de temps | Utilisé pour limiter la plage de temps de la requête. |
| Champ indexé | De préférence utilisé comme filtre de requête pour réduire les analyses complètes de la table. |

## 4. Utilisation de Common SQL

'Common' SQLest utilisé pour exécuter rapidement des requêtes prédéfinies, adapté aux scénarios de vérification à haute fréquence tels que les certificats, applications, fichiers et utilisateurs.

1. Cliquez **Common SQL** au-dessus de l' SQL éditeur.
2. Sélectionnez le SQL vous devez utiliser dans la liste déroulante.
3. Si vous devez d'abord vérifier le contenu de l'instruction, cliquez sur **Aperçu** à côté du correspondant SQL.
4. Vérifiez la description, la base de données, le nom de la table et SQL le contenu dans la fenêtre d'aperçu.
5. Après avoir confirmé que tout est correct, cliquez sur **Exécuter**.

Couramment utilisé SQL peut contenir des espaces réservés :

| Espace réservé | Type de paramètre | Exemple |
| --- | --- | --- |
| `%s` | Chaîne | ID de l'application, ID de fichier fournisseur, guid historique, ID utilisateur fournisseur |
| `%d` | Nombre | ID utilisateur interne |

Si le SQL contient des espaces réservés, un **Remplissez SQL Paramètres** Une fenêtre s'ouvrira lors de l'exécution.

1. Remplissez chaque paramètre selon l'invite.
2. Pour les paramètres de type chaîne, remplissez l'ID complet sans ajouter de guillemets supplémentaires.
3. Remplissez les paramètres numériques avec des nombres purs.
4. Cliquez **Exécuter la requête**.

Les plus couramment utilisés SQL incluent actuellement principalement les scénarios suivants :

| Scénario | Description |
| --- | --- |
| Requête de certificat | Interroger les certificats et les ID d'application. |
| Requête par appid spécifié | Interroger les détails d'une application par ID d'application. |
| Requête par guid de fichier client spécifié | Interroger les détails du fichier par `provider_file_id`. |
| Requête par guid de fichier interne spécifié | Interroger les détails du fichier par `history_guid`. |
| Requête par ID utilisateur interne spécifié | Interroger les détails utilisateur par ID utilisateur interne. |
| Requête par ID utilisateur client spécifié | Interroger les détails utilisateur par `provider_user_id`. |

> Même avant d'utiliser les paramètres courants SQL, il est nécessaire de confirmer l'environnement cible et les valeurs des paramètres. Les paramètres courants SQL visent uniquement à réduire le coût de la saisie manuelle et ne garantissent pas que les résultats de la requête répondront aux objectifs de cette investigation.

## 5. Exécution des requêtes

1. Remplissez l'instruction de requête dans la SQL zone de saisie.
2. Préférez utiliser `SELECT` des requêtes et ne pas exécuter de instructions d'insertion, de mise à jour ou de suppression.
3. La requête par défaut LIMIT est 10 et peut être ajustée manuellement.
4. Cliquez **Exécuter la requête**.
5. Préférez effectuer EXPLAIN, et **la confirmation d'exécution** avant de démarrer la requête.

Exemple :

```sql
SELECT *
FROM example_table
WHERE id = 'example-id';
```

## 6. Consultation des résultats de la requête

1. Voir les enregistrements retournés dans la zone de résultats.
2. Vérifiez si les champs clés répondent aux attentes.
3. Si le résultat est vide, vérifiez la base de données, le nom de la table, les conditions de la requête et la plage de temps.
4. S'il y a trop de résultats, ajoutez des conditions de filtrage plus précises et interrogez à nouveau.

## 7. Utilisation de l'historique des requêtes

"L'historique des requêtes" est utilisé pour voir les SQL instructions qui ont été exécutées sur la page actuelle, ce qui permet de réutiliser facilement les instructions de dépannage, de vérifier les résultats de l'exécution et de copier SQL."

> [!NOTE]
>
> L'historique des requêtes est enregistré localement dans le navigateur actuel et ne sera pas stocké de manière persistante. Chaque dimension base de données/table conserve jusqu'aux 100 derniers enregistrements, et il n'y a actuellement aucune expiration automatique basée sur le temps ; effacer les données de site du navigateur, changer de navigateur, changer de périphérique ou passer à une autre base de données/table entraînera l'affichage de différents enregistrements historiques.

1. Basculer vers **Historique des requêtes** dans la zone des résultats.
2. Voir le statut d'exécution, l'heure, la base de données, la table, SQLle nombre de lignes renvoyées et le temps écoulé dans les enregistrements de l'historique.
3. Pour exécuter à nouveau une SQL instruction, cliquez sur **Insérer dans l'éditeur et exécuter** dans la colonne d'opération de cet enregistrement.
4. Si vous avez seulement besoin de réutiliser l'instruction, cliquez sur **Copier SQL**.

Description du champ de l'historique des requêtes :

| Champ | Description |
| --- | --- |
| Statut | Si SQL exécuté avec succès ; en cas d'échec, dépannez en fonction des messages d'erreur. |
| Temps | Le temps d'exécution de la requête actuelle. |
| Base de données | La base de données sélectionnée lors de SQL l'exécution. |
| Table | La table associée lors de SQL l'exécution. |
| SQL | l'instruction de requête effectivement exécutée. |
| Renvoie le nombre de lignes | Nombre de lignes de données renvoyées par cette requête. |
| Temps écoulé | SQL Le temps nécessaire à l'exécution et peut être utilisé pour déterminer s'il existe un risque de requêtes lentes. |
| Opération | Prend en charge la réinsertion et l'exécution, ou la copie SQL. |

Lors du dépannage des problèmes liés à l'historique des requêtes, il est recommandé de se concentrer sur : 

| Situation | Suggestions de manipulation |
| --- | --- |
| Échec du statut | Tout d'abord, vérifiez si le SQL syntaxe, table de base de données existe, et si les champs sont corrects. |
| Prend beaucoup de temps | Ajoutez des conditions de filtre, ou vérifiez d'abord la structure de la table et les champs d'index. |
| Renvoie trop de lignes | Ajouter 'WHERE' condition et 'LIMIT'. |
| Les résultats de plusieurs requêtes sont incohérents | Confirmer si la base de données, la table ou l'environnement a été changé. |

> L'historique des requêtes est utilisé pour aider à examiner le processus de dépannage actuel. Avant de réexécuter l'historique SQL, vous devez toujours confirmer le SQL contenu, la base de données cible et l'environnement actuel. 

## 8. Scénarios courants de dépannage

| Scénario | Suggestions d'opération |
| --- | --- |
| Confirmer si les registres d'entreprise existent | Utilisez l'ID de l'entreprise pour une requête précise. |
| Vérifier le statut de la tâche | Interrogez le champ de statut et la date de mise à jour par ID de tâche. |
| Dépannez les configurations inefficaces | Interrogez la valeur actuelle et la date de mise à jour dans la table de configuration. |
| Vérifiez les changements récents | Interrogez par ordre décroissant selon le champ de temps et limitez le nombre d'entrées retournées. |
| Interrogez les informations de l'application ou du certificat | Privilégiez l'utilisation des requêtes de certificat ou des requêtes appid dans “Commun SQL”. |
| Réutilisez les instructions de dépannage | Copier SQL depuis “Historique des requêtes”, vérifiez les paramètres et exécutez à nouveau. |

## 9. Précautions

1. Les requêtes inconditionnelles sur de grandes tables sont interdites dans l'environnement de production.
2. Si vous n'êtes pas sûr de l' SQL impact, vérifiez d'abord dans un environnement à faible risque.
3. Ne modifiez pas directement les données commerciales via RDB outils sauf s'il y a un plan de changement clair et une approbation.
4. Paramètres communs SQL doivent être remplis avec les valeurs réelles de l'environnement actuel pour éviter des requêtes incorrectes entre les environnements.
5. SQL dans l'historique des requêtes peuvent contenir des identifiants sensibles, confirmez la portée avant de copier ou de transférer.
6. Lorsque les résultats de la requête impliquent des informations sensibles, ne partagez pas à l'extérieur des captures d'écran complètes ou des données en texte clair.
