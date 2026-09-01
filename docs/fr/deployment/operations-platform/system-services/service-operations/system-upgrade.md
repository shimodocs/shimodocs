# Mise à niveau du système

[← ShimoDocs Suite documentation de déploiement](../../../README.md)

## Aperçu des fonctions

La mise à jour du système est utilisée pour télécharger et appliquer un nouveau MDP package d'installation. Avant la mise à jour officielle, le système vérifiera automatiquement le package de mise à jour et l'environnement actuel, et affichera les modifications de configuration et de service impliquées dans cette mise à jour, vous aidant à compléter la mise à niveau de version ou la maintenance courante.

La page conserve également l'historique des mises à jour, facilitant la consultation des enregistrements de mise à jour passés, de l'état d'exécution et des journaux associés.

**Remarque**: Les mises à niveau majeures peuvent mettre à jour le schéma de la base de données. La mise à jour implique des modifications de configuration, des redémarrages de service et des changements d'interface fonctionnelle, ce qui peut affecter l'expérience utilisateur. Elle doit être effectuée pendant les heures creuses.

## Entrer dans la page

Après vous être connecté à l'interface de gestion, sélectionnez **Mise à niveau du système** dans la navigation de gauche pour entrer dans la page.

Les mises à jour système sont uniquement disponibles pour les administrateurs. Si vous ne voyez pas ce menu, veuillez contacter votre administrateur système pour confirmer les permissions du compte actuel.

## Préparations avant la mise à jour

Avant de commencer la mise à jour, il est recommandé de confirmer les éléments suivants :

- Utiliser le package de mise à jour fourni officiellement qui correspond au type de produit et à la méthode de déploiement actuels.
- Le package de mise à jour est en `.tar.gz` format. Veuillez ne pas extraire ni modifier les fichiers à l'intérieur du paquet.
- Il est recommandé d'effectuer la mise à niveau pendant les heures creuses ou les fenêtres de maintenance.
- Confirmez que le service actuel fonctionne normalement et informez les utilisateurs concernés à l'avance.
- Si la mise à niveau implique un changement de Licence, veuillez préparer le contenu de la nouvelle Licence à l'avance.

## Étapes de mise à niveau

### 1. Télécharger le paquet de mise à niveau

Cliquez sur la zone de téléchargement sur la page de mise à niveau du système, ou faites glisser le paquet de mise à niveau dans la zone de téléchargement. Une fois le téléchargement terminé, le système analysera et vérifiera automatiquement le paquet de mise à niveau.

La vérification comprend principalement :

- Le format et l'intégrité du contenu du paquet d'installation.
- Si la signature du paquet d'installation est valide.
- La version du paquet de mise à niveau et le plan de mise à niveau.
- Si le type de produit et l'architecture de déploiement correspondent.
- Si la licence actuelle prend en charge cette mise à niveau.

Les résultats de la vérification sont divisés en statuts suivants :

- **Réussi**: La vérification est normale et vous pouvez continuer.
- **Modification**: Il y a des changements attendus dans cette mise à niveau ; veuillez confirmer le contenu avant de continuer.
- **Non concordant**: Il y a des problèmes qui empêchent la mise à niveau ; vous devez remplacer le paquet de mise à niveau ou gérer la configuration concernée avant de le re-téléverser.

### 2. Entrer la Licence

Si le système détermine que cette mise à niveau nécessite une Licence mise à jour, la page affichera les produits nécessitant une mise à jour et des informations telles que le code machine actuel du serveur.

Après avoir collé le contenu de la nouvelle Licence, cliquez sur **Vérifier et Enregistrer temporairement**. La mise à niveau ne peut continuer que lorsque la licence a été vérifiée avec succès. La licence temporairement sauvegardée prendra effet automatiquement après l'application réussie de la mise à jour. Pour référence de la licence, voir [Gestion des licences]

Si la page indique que la licence n'a pas besoin d'être mise à jour, vous pouvez passer directement à l'étape suivante.

### 3. Confirmer le contenu du paquet de mise à niveau

La page affichera les fichiers de configuration et les ressources de service contenus dans le paquet de mise à niveau. Vous pouvez sélectionner des fichiers spécifiques pour en afficher le contenu et confirmer que le paquet de mise à niveau correspond à la cible de mise à niveau actuelle.

### 4. Confirmer les changements

Le système comparera l'environnement actuel avec le paquet de mise à niveau et affichera les ressources qui seront ajoutées, modifiées, supprimées ou redémarrées lors de cette mise à niveau.

Veuillez porter une attention particulière pour confirmer : 

- S'il y a des suppressions de ressources inattendues. 
- Si des services importants doivent être redémarrés. 
- Si les modifications dans les fichiers de configuration sont conformes aux attentes. 

### 5. Appliquer la mise à jour 

Après avoir confirmé que les informations ci-dessus sont correctes, cliquez sur **Confirmer pour démarrer la mise à jour**. Le système créera un instantané pré-mise à niveau et commencera l'application du paquet de mise à jour. 

Pendant la mise à niveau, la page affichera en continu les journaux d'exécution, y compris des informations sur la mise à jour des ressources, le redémarrage des services et les vérifications de statut en cours. Lorsque certains composants redémarrent, la page de gestion peut être temporairement inaccessible. Veuillez patienter un moment puis rouvrir la page pour vérifier la progression. 

Si l'exécution échoue, vous pouvez traiter les problèmes en fonction des journaux puis cliquer sur **Réappliquer**. 

### 6. Terminer la mise à niveau 

Après l'exécution réussie de la tâche de mise à niveau, cliquez sur **Terminer** pour finaliser ce processus de mise à niveau. 

La page de fin de mise à niveau affichera le nom et la version du package de mise à niveau et proposera les opérations suivantes : 

- **Afficher le journal d'exécution**: Afficher le processus complet de cette mise à niveau. 
- **Restauration à la version pré-Mise à Niveau**: Entrez le snapshot avant la mise à niveau et suivez les instructions de la page pour effectuer la restauration. 
- **Retour à la mise à jour de l'application**: Retour à la page d'accueil de la mise à niveau du système. 

## Historique des mises à niveau 

Le bas de la page d'accueil de la mise à niveau du système affichera l'historique des mises à niveau, y compris le nom du package de mise à niveau, la version, la date de création et le statut d'exécution. 

Cliquez sur l'enregistrement de mise à niveau pour ré-entrer dans le processus correspondant et voir la progression de la mise à niveau ou les résultats d'exécution historiques.

## Situations courantes

- **Échec de la vérification du package de mise à niveau**: Veuillez confirmer que la source du package de mise à niveau, l'intégrité du fichier, le type de produit et l'architecture de déploiement sont corrects.
- **Incohérence de version**: Veuillez vérifier la version actuelle du système et la version du package de mise à niveau pour vous assurer que le chemin de mise à niveau correct est utilisé.
- **Mise à jour de licence requise**: Obtenez une nouvelle licence compatible avec la version cible et l'environnement d'exécution actuel, validez-la et stockez-la temporairement avant de continuer.
- **Page du processus de mise à niveau temporairement indisponible**: Le MDP service peut être en cours de mise à jour ou de redémarrage, veuillez patienter un instant et actualiser la page.
- **Échec de la tâche de mise à niveau**: Vérifiez les journaux d'exécution pour identifier la cause ; après avoir résolu le problème, utilisez **Réappliquer**.
- **Anomalie de service après la mise à niveau**: Vérifiez d'abord les journaux d'exécution et l'état du service ; si une récupération est nécessaire, vous pouvez revenir en arrière en utilisant un instantané pré-mise à niveau.

> Les mises à niveau du système modifieront les configurations des services et peuvent déclencher des redémarrages de services. Veuillez procéder uniquement après avoir confirmé que le package de mise à niveau et les modifications sont corrects.
