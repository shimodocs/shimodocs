# Centre de configuration

[← ShimoDocs Suite documentation de déploiement](../../../README.md)

## Vue d'ensemble des fonctions 

Le Centre de Configuration est utilisé pour consulter et modifier les configurations des applications de divers services. La page affiche à la fois la configuration du template d'usine et la configuration actuellement active, facilitant la compréhension des différences de configuration et les modifications de publication de manière contrôlée. 

Après la publication de la configuration, le système sauvegardera cette modification et pourra redémarrer automatiquement les services concernés en fonction de votre sélection pour appliquer la nouvelle configuration. 

## Accéder à la page 

Après vous être connecté à l'interface de gestion, sélectionnez **Centre de configuration** dans la navigation à gauche pour accéder à la page. 

Le Centre de configuration n'est disponible que pour les administrateurs. Si vous ne voyez pas ce menu, veuillez contacter l'administrateur système pour confirmer les permissions de votre compte. 

## Description de la page 

La page est principalement divisée en trois zones : 

- **Liste des applications et fichiers**: Affiche les fichiers configurables par application, avec possibilité de recherche par nom d'application. 
- **Configuration du modèle d'usine**: Affiche la configuration originale fournie dans le package d'installation, à titre de consultation et de référence uniquement. 
- **Configuration actuellement active**: Affiche la configuration actuellement utilisée par l'environnement, qui peut être modifiée directement. 

Les fichiers de configuration sont généralement au format JSON, YAML, ou TOML . Veuillez maintenir la syntaxe correcte du fichier et la structure des données. 

## Modifier et publier la configuration 

Il est recommandé de suivre les étapes ci-dessous : 

1. Sur le côté gauche, sélectionnez l'application et le fichier de configuration que vous devez modifier.
2. Reportez-vous au modèle d'usine et modifiez le contenu de la configuration dans la zone **Configuration effective** .
3. Après modification, la page affichera le statut **Modifié mais non publié**.
4. Cliquez **Modifié mais non publié**, ou utilisez `Ctrl S` (Windows) / `Command S` (macOS) pour ouvrir la fenêtre de confirmation.
5. Vérifiez les chemins des champs, les types de changements et les nouvelles valeurs à publier.
6. Choisissez si vous souhaitez activer **Redémarrez les services associés après la publication de la configuration** si nécessaire.
7. Cliquez **Publier la configuration** pour compléter la modification.

S'il y a des erreurs de formatage dans le contenu de la configuration, le système affichera une erreur et empêchera la publication. Veuillez la corriger et réessayer.

## Confirmation de changement

La fenêtre de confirmation avant la publication affichera les différences pour cette modification :

- **Chemin**: Le chemin de configuration qui a changé.
- **Op**: Type de modification, tel qu'ajouter, modifier ou supprimer.
- **Valeur**: La valeur de configuration après la modification.

Il est recommandé de confirmer chaque différence pour éviter de supprimer accidentellement une configuration ou de modifier des paramètres de service incorrects.

## Redémarrage du service

Certaines configurations ne prennent effet qu'après le redémarrage du service. Par défaut, la page active **Redémarrez les services associés après la publication de la configuration**, et après une publication réussie, les services associés à l'application seront automatiquement redémarrés.

Si cette option est désactivée, la configuration sera toujours publiée, mais les services concernés peuvent nécessiter un redémarrage manuel ultérieur pour appliquer les nouveaux paramètres.

Pendant le redémarrage du service, les fonctionnalités associées peuvent subir de brèves fluctuations ; il est recommandé d'effectuer les changements importants de configuration en dehors des heures de pointe.

## Situations courantes

- **Application non trouvée**: Veuillez effacer les critères de recherche ou confirmer que l'application cible a été correctement déployée.
- **Impossible de charger le fichier de configuration**: Veuillez vérifier l'état du service et les autorisations du compte actuel avant de réessayer.
- **Erreur de format de configuration**: Veuillez vérifier l'indentation, les crochets, les guillemets et le format des champs dans JSON, YAML, ou TOML.
- **Aucun changement à publier**: Le contenu réel de la configuration n'a pas changé de manière effective, aucune publication n'est requise.
- **Les modifications ne sont pas effectives après la publication**: Veuillez confirmer si les services associés ont été redémarrés et, si nécessaire, redémarrez manuellement et vérifiez à nouveau.
- **Échec de la publication**: Veuillez vérifier le contenu de la configuration ou le statut du service selon les indications de la page, et republier après traitement. 

> Les modifications de configuration peuvent affecter le démarrage des services et les fonctions métier, veuillez publier uniquement après avoir pleinement confirmé les changements. 

## Exemple d'interface opérationnelle 

Le schéma ci-dessous montre les zones pour la sélection des fichiers de configuration, la visualisation du contenu de la configuration et la modification. 

