# Journaux d'audit

[← ShimoDocs Suite documentation de déploiement](../../../README.md)

## Aperçu des fonctions

Le journal des opérations est utilisé pour visualiser et suivre les opérations de gestion des utilisateurs dans le système, ce qui aide à la résolution des problèmes, à l'audit de sécurité et au suivi des changements.

Cette page est en lecture seule et ne permet pas de modifier ou de supprimer des enregistrements de journaux.

## Accéder à la page

Après vous être connecté à l'interface de gestion, sélectionnez **Journal des opérations** dans la navigation de gauche pour accéder à la page.

## Filtrage des journaux

Vous pouvez effectuer une requête en utilisant la combinaison suivante de conditions :

- **Source de l'événement**: Par exemple, Panneau de configuration, Centre de configuration des applications, Metteur à jour, Kubernetes Gestion des ressources ou Centre de gestion des utilisateurs.
- **Type d'opération**: Affiche les opérations correspondantes selon la source de l'événement, telles que les mises à jour de configuration, les mises à niveau de version, les redémarrages de service ou les actions de gestion des utilisateurs.
- **Utilisateur opérant**: Filtrer les enregistrements générés par un utilisateur spécifique.

Après avoir sélectionné la source de l'événement, la liste des types d'opérations s'ajustera automatiquement. Cliquez **Rechercher** pour appliquer les conditions de filtre, ou cliquez sur le bouton de réinitialisation pour effacer les conditions.

## Visualisation de la liste des journaux

La liste affiche principalement :

- ID du journal.
- Source de l'événement et type d'opération.
- Utilisateur opérant.
- Type, nom et ID de l'objet opéré.
- Heure de l'opération. 

Le nombre total de journaux sera affiché en haut de la page, et la liste prend en charge la pagination et l'ajustement du nombre d'éléments affichés par page. 

## Voir les détails du journal 

Cliquez **Détails** sur le côté droit de l'enregistrement pour afficher les informations complètes, y compris : 

- Le nom et l'identifiant interne de la source et le type d'opération. 
- L'utilisateur effectuant l'opération et l'ID utilisateur. 
- Heure de l'opération. 
- Type d'objet, ID de l'objet et nom de l'objet. 
- Métadonnées de l'événement. 

Pour les modifications apportées au centre de configuration de l'application, les détails peuvent également montrer les modifications de configuration, si un redémarrage automatique se produit après la publication, et les charges de travail qui ont été redémarrées.

## Cas d'utilisation courants

- Vérifier qui a exécuté une modification spécifique de configuration.
- Confirmer le moment des mises à niveau du système, des redémarrages de service ou des opérations de mise à l'échelle.
- Suivre les modifications associées en fonction des noms d'objets.
- Vérifier les différences de configuration et les résultats d'exécution à l'aide des métadonnées de l'événement.
- Enquêter sur les erreurs d'opération ou les modifications administratives inattendues.

## Situations courantes

- **Aucun enregistrement trouvé**: Essayez de supprimer les conditions du filtre ou confirmez si la source sélectionnée, le type d'opération et l'utilisateur correspondent.
- **La liste des types d'opération est vide**: Sélectionnez d'abord la source de l'événement ou rechargez la page pour obtenir la dernière énumération.
- **Les informations sur l'objet sont vides**: Certains événements système peuvent ne pas être associés à des objets spécifiques, ce qui est normal.
- **Les métadonnées ne sont pas du contenu formaté**: Certains événements historiques peuvent être stockés en texte brut, et la page affichera le contenu original.
- **La quantité de journaux ne correspond pas aux attentes**: Les journaux ne consignent que les opérations qui ont été auditées par le système et peuvent être affectés par la politique de conservation de l'environnement.

> Les journaux d'opération peuvent contenir des utilisateurs, des identifiants d'objets et des informations sur les modifications de configuration, et ne doivent être accessibles qu'au personnel autorisé.
