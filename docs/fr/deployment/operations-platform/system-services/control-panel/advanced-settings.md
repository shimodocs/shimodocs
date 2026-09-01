# Paramètres avancés

[← ShimoDocs Suite documentation de déploiement](../../../README.md)

## Aperçu des fonctions

Les paramètres avancés sont utilisés pour gérer les personnalisations du système `pd-config` directement via YAML. Cela convient pour gérer des paramètres avancés non fournis sur la page de paramètres standard ou pour une configuration en masse.

Le système fusionnera les configurations personnalisées avec les configurations par défaut de l'usine. Les valeurs personnalisées au même chemin remplaceront les valeurs par défaut, tandis que les configurations non remplies continueront à utiliser les valeurs par défaut de l'usine.

## Entrer dans la Page

Après vous être connecté à l'interface de gestion, sélectionnez **Paramètres avancés** dans la navigation à gauche pour accéder à la page.

Les paramètres avancés sont uniquement disponibles pour les administrateurs. Cette page peut affecter l'ensemble du système, elle doit donc être utilisée par du personnel familier avec la MDP structure de configuration.

## Description de la Page

La page est divisée en deux parties :

- **Configuration pd par défaut de l'usine**: La configuration par défaut fournie par le package d'installation, en lecture seule.
- **Configuration pd personnalisée**: La configuration personnalisée client actuellement enregistrée, qui peut être modifiée.

La configuration personnalisée n'a pas besoin de copier tout le contenu par défaut ; en général, seuls les éléments de configuration à remplacer ou à ajouter sont conservés.

## Modifier et Publier la Configuration

Il est recommandé de suivre ces étapes :

1. Cliquez **Actualiser** pour s'assurer que la dernière configuration personnalisée est chargée.
2. Comparer avec la configuration par défaut de l'usine à gauche, et modifier le YAML contenu à droite.
3. Cliquez **Publier**.
4. Vérifier le contenu ajouté, supprimé et modifié dans la fenêtre de confirmation des différences.
5. Utiliser les boutons précédent/suivant pour vérifier les changements élément par élément.
6. Après avoir confirmé qu'il n'y a pas d'erreurs, cliquez **Confirmer la Publication**.

Après une publication réussie, le système créera une tâche d'application de configuration et ouvrira le journal de la tâche dans une nouvelle fenêtre. Selon les modifications et les paramètres du système, les services associés peuvent redémarrer automatiquement.

## Historique de configuration

Cliquez **Historique** pour afficher les configurations personnalisées publiées précédemment, y compris l'ID de l'enregistrement, l'heure de création, et MD5.

- Cliquez **Afficher** pour voir le complet YAML d'une version historique.
- Après avoir sélectionné deux enregistrements, vous pouvez effectuer une comparaison des différences.

La page actuelle ne fournit pas de bouton de restauration en un clic. Pour restaurer des configurations historiques, veuillez vérifier la version correspondante, vérifier le contenu, le copier manuellement dans la zone d'édition, et republier.

## Notes

- YAML la syntaxe doit rester correcte ; faites attention à l'indentation, aux deux-points et aux types de données.
- Ne supprimez pas arbitrairement les éléments de configuration que vous ne comprenez pas.
- Avant de publier, vérifiez pleinement les différences pour éviter d’écraser les modifications récemment soumises par d'autres administrateurs.
- Il est recommandé d’effectuer les modifications importantes pendant les heures creuses, et de consigner la configuration originale à l'avance.
- Après la publication, vérifiez le journal des tâches pour vous assurer que l'application de la configuration et la vérification de l'état du service sont terminées.

## Situations courantes

- **Échec de la publication**: Veuillez vérifier YAML le format, les noms de champs et les types de valeurs de configuration.
- **Redémarrage du service après publication**: Les modifications de configuration peuvent nécessiter le redémarrage des services associés, c'est normal.
- **Page temporairement inaccessible après publication**: MDP ou les services associés peuvent être en cours de redémarrage ; veuillez actualiser après avoir attendu un moment.
- **La configuration n'atteint pas l'effet attendu**: Veuillez confirmer que le chemin de configuration est correct et vérifier le résultat final fusionné ainsi que le journal des tâches.
- **Modification de configuration incorrecte**: Trouvez la version correcte dans l'historique, copiez le contenu et republiquez.

> Les paramètres avancés affecteront les configurations au niveau du système et les opérations de service, ne publiez pas directement des configurations non vérifiées dans l'environnement de production.

## Exemple d'interface d'opération

La figure ci-dessous montre l'interface de comparaison entre la configuration par défaut de l'usine et la configuration personnalisée.

