# MongoDB Outils

[← ShimoDocs Suite documentation de déploiement](../../../README.md)

> [!TIP]
>
> MongoDB est utilisé dans la plateforme d'opérations pour visualiser MongoDB les bases de données, collections et contenus de documents. Il est adapté au dépannage des données basées sur des documents, des états intermédiaires, des enregistrements de tâches et des données commerciales avec des structures flexibles.
>
> La page permet de rechercher par base de données ou collection, et après avoir sélectionné une collection, MongoDB JSON des requêtes conditionnelles peuvent être utilisées.

## 1. Accès MongoDB

1. Connectez-vous à **MDP Plateforme d'Opérations**.
2. Sélectionner **Services Systèmes** en haut.
3. Développer **Outils Middleware** dans la barre de navigation gauche.
4. Sélectionner **MongoDB**.

Le côté gauche de la page montre l'arborescence des bases de données et des collections, tandis que le côté droit affiche les conditions de requête et les résultats de la requête.

## 2. Recherche de bases de données ou collections

1. Entrer DATABASE_NAME ou des mots-clés de nom de collection dans la boîte de recherche en haut à gauche.
2. Afficher la liste filtrée de l'arborescence.
3. Effacer la boîte de recherche pour restaurer l'affichage de toutes les bases de données.

## 3. Développer la base de données et sélectionner une collection

1. Trouver la base de données cible dans l'arborescence à gauche.
2. Cliquer sur l'icône de développement à gauche de la base de données pour charger la liste des collections.
3. Sélectionner la collection cible.
4. La page de droite exécutera automatiquement une requête une fois avec la condition par défaut `{}`.

> Sélectionner uniquement la base de données n'exécutera pas une requête de collection ; vous devez d'abord sélectionner une collection spécifique, puis la zone de requête sera affichée à droite.

## 4. Remplir les conditions de la requête

1. Remplissez les MongoDB JSON conditions de requête dans la boîte de saisie de requête à droite.
2. Sélectionnez le nombre de résultats à retourner, supportant `limit: 10`, `limit: 20`, `limit: 50`.
3. Cliquez **Requête**.

Exemple de requête : 

```json
{
  "_id": "task-123"
}
```

Exemple de requête par champ :

```json
{
  "status": "running"
}
```

## 5. Consultation des résultats de la requête

1. Après avoir complété la requête, vérifiez les documents retournés dans la zone de résultats à droite.
2. Par défaut, les résultats sont affichés en JSON format.
3. Cliquez **Développer** pour développer le document actuel.
4. Cliquez **Réduire** pour réduire le document actuel.
5. Cliquez **Copier** pour copier le document actuel JSON.

## 6. Scénarios courants de dépannage

| Scénario | Suggestion opérationnelle |
| --- | --- |
| Confirmer si le document existe | Après avoir sélectionné la collection, interroger en utilisant `_id` ou l'identifiant de l'entreprise. |
| Vérifier le statut de la tâche | Interroger par ID de tâche et vérifier le champ de statut et le champ de date de mise à jour. |
| Trouver un type d'enregistrement | Utiliser une combinaison de champs tels que statut, type et date de création pour interroger. |
| Le résultat est vide | Vérifier si la base de données correcte, la collection, les noms de champs et les types de champs sont sélectionnés. |
| Besoin de récupérer les résultats de dépannage | Cliquer **Copier** sur un seul résultat. |
