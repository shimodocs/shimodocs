# Redis Outils

[← ShimoDocs Suite documentation de déploiement](../../../README.md)

> [!TIP]
>
> Redis est utilisé dans la plateforme d'opérations pour visualiser Redis instances, bases de données, une liste de clés et les détails des clés. Il est couramment utilisé pour dépanner les caches, les sessions, les verrous distribués, les compteurs de limitation de débit et les états à court terme.
>
> La page prend en charge la recherche par clé ou préfixe de clé, et affiche le type de clé, TTL, et la valeur actuelle.

## 1. Accès Redis

1. Connectez-vous à **MDP Plateforme d'Opérations**.
2. Sélectionner **Services Systèmes** en haut.
3. Développer **Outils Middleware** dans la barre de navigation gauche.
4. Sélectionner **Redis**.

Le côté gauche de la page est la zone de requête de clé, et le côté droit est la zone de détails de la clé.

## 2. Sélection Redis Instance et base de données

1. Dans le premier menu déroulant en haut à gauche, sélectionnez l' Redis instance.
2. Dans le deuxième menu déroulant, sélectionnez la base de données, par exemple `db0`.
3. La page chargera la liste des clés en fonction de l'instance et de la base de données actuelles.

Si la liste des bases de données est vide ou si la page signale une erreur, veuillez d'abord vérifier si Redis la configuration de l'instance est normale.

## 3. Rechercher une clé

1. Entrez le nom de la clé ou le préfixe de clé dans la zone de recherche.
2. Cliquez sur le bouton de recherche ou appuyez sur Entrée pour exécuter la requête.
3. Affichez la liste des clés sur le côté gauche.
4. Si vous devez recharger la liste dans les conditions actuelles, cliquez sur l'icône de rafraîchissement.

L'invite de la zone de recherche est "Veuillez entrer le nom de la clé, recherche floue prise en charge." La page affichera le type de clé correspondant et TTL.

## 4. Voir la liste des clés

La liste des clés contient les informations suivantes :

| Informations | Description |
| --- | --- |
| Type | Le Redis type de la Clé, tel que `string`, `hash`, `list`, `set`, `zset`. |
| Nom de la Clé | La Clé complète actuellement correspondante. |
| TTL | Le temps restant avant l'expiration de la Clé ; la page affiche "permanente" si la Clé actuelle n'a pas de date d'expiration. |

## 5. Voir les Détails de la Clé

1. Cliquez sur la Clé cible dans la liste des Clés à gauche.
2. La zone de détail à droite affiche le nom de la Clé, le type, TTL, et la valeur spécifique.
3. Pour rafraîchir les détails de la Clé actuelle, cliquez sur le bouton de rafraîchissement dans la zone de titre des détails.

Les différents types de méthodes d'affichage sont les suivants :

| Type | Méthode d'Affichage |
| --- | --- |
| `string` | Afficher la valeur complète dans une zone de texte. |
| `hash` | Afficher le champ Clé et Valeur dans un tableau. |
| `list` / `set` | Afficher la liste des éléments dans un tableau. |
| `zset` | Afficher Score et Membre dans un tableau. |

## 6. Copie des Valeurs des Champs

1. Trouvez le champ ou la valeur que vous souhaitez copier dans le tableau Détails de la clé.
2. Cliquez sur le contenu correspondant.
3. La page copiera ce contenu dans le presse-papiers.

> `string` Le type est affiché dans une zone de texte et peut être copié directement en sélectionnant le texte ; les types de table prennent en charge le clic sur la valeur pour copier.

## 7. Scénarios courants de dépannage

| Scénario | Action suggérée |
| --- | --- |
| Confirmez si le cache existe | Après avoir sélectionné l'instance et la BD, recherchez par clé complète ou préfixe. |
| Vérifiez si le cache a expiré | Vérifiez le TTL dans la liste des clés ou dans les détails. |
| Afficher les champs Hash | Cliquez sur la clé pour afficher les champs et les valeurs dans le tableau à droite. |
| Voir les données triées ZSet | Cliquez sur la `zset` clé pour afficher Score et Membre. |
| Vérifiez le dernier état de la même clé | Cliquez sur le bouton Actualiser dans la zone de détails. |
