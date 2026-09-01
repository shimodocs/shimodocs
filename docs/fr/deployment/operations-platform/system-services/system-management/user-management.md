# Gestion des utilisateurs de la plateforme

[← ShimoDocs Suite documentation de déploiement](../../../README.md)

## Aperçu des fonctions

La gestion des utilisateurs du système est utilisée pour maintenir les comptes utilisateurs dans le MDP gestion du backend, y compris la création d'utilisateurs, la modification des informations de base, la réinitialisation PASSWORD, gérer l'authentification à deux facteurs, et supprimer des utilisateurs.

## Accéder à la page

Après s'être connecté avec un compte d'administrateur système, sélectionnez **Gestion des utilisateurs du système** dans la navigation de gauche pour accéder à la page.

Ce menu est accessible uniquement aux comptes d'administrateur système désignés. Si vous ne voyez pas ce menu, veuillez contacter votre administrateur système.

## Afficher les utilisateurs

La page affiche les pseudos des utilisateurs, les NOMS D'UTILISATEUR, les rôles, les adresses e-mail, les informations de contact, les dernières connexions et les dates d'inscription, et fournit des informations générales telles que le nombre total d'utilisateurs, les utilisateurs récemment actifs et les comptes administrateurs.

Vous pouvez voir tous les utilisateurs via la pagination de la liste.

## Créer un nouvel utilisateur

Cliquez **Créer un nouvel utilisateur** et remplissez les informations suivantes :

- **Surnom**: Obligatoire, utilisé pour l'affichage sur la page.
- **USERNAME**: Obligatoire, utilisé pour se connecter au système.
- **E-mail**: Obligatoire, doit fournir une adresse e-mail valide.
- **Informations de contact**: Facultatif.
- **Rôle**: Choisissez utilisateur ordinaire ou administrateur.

Après la création, le système générera un initial PASSWORD. Veuillez le copier immédiatement et le fournir à l'utilisateur par un moyen sécurisé, car le PASSWORD ne pourra peut-être plus être consulté une fois cette fenêtre fermée.

### Description du rôle utilisateur

- Administrateur
  - Peut utiliser toutes les pages en fonction des autorisations globales
    - ShimoDocs Suite
    - Centre de documents
    - Services Systèmes
- Utilisateur régulier
  - Portée d'utilisation des pages en fonction des autorisations globales
    - ShimoDocs Suite
    - Centre de documents
    - Services système (masqué)

## Modifier les informations de l'utilisateur

Cliquez sur le bouton de modification à droite de l'utilisateur pour modifier son pseudonyme, son e-mail et ses informations de contact. USERNAME ne peut pas être modifié sur cette page après sa création.

## Réinitialiser PASSWORD

Après avoir cliqué sur le bouton Réinitialiser PASSWORD et confirmé l'opération, le système générera un nouveau PASSWORD. L'original PASSWORD deviendra immédiatement invalide.

Veuillez copier et sauvegarder correctement le nouveau PASSWORD, le remettre à l'utilisateur correspondant par un canal sûr, et rappeler à l'utilisateur de se connecter et de changer le PASSWORD le plus rapidement possible.

## Gérer l'authentification à deux facteurs

- **Activer ou désactiver la 2FA**: Utilisez le bouton dans la ligne de l'utilisateur et continuez dans la fenêtre de confirmation.
- **Réinitialiser la 2FA**: Le système générera un nouveau code QR et un nouveau Secret, et les informations de vérification originales deviendront invalides.

Après la réinitialisation, les utilisateurs doivent utiliser des authentificateurs tels que Authenticator pour rescanner et lier. Les codes QR et les Secrets sont des informations sensibles et ne doivent pas être transmis par des canaux publics. 

Lier la 2FA

Ajouter en scannant avec Authenticator, et utiliser la 2FA dynamique à 6 chiffres pour les connexions suivantes

## Supprimer l'utilisateur

Après avoir cliqué sur le bouton de suppression et confirmé, le compte utilisateur sera supprimé. L'action de suppression ne peut pas être annulée, veuillez donc vous assurer que le compte n'est plus utilisé et compléter le transfert nécessaire des données et des autorisations avant d'exécuter.

## Situations courantes

- **Impossible de créer l'utilisateur**: Veuillez vérifier si le USERNAME est dupliqué, si le format de l'e-mail est correct et si tous les champs obligatoires sont complets.
- **L'utilisateur ne peut pas se connecter**: Vérifiez que USERNAME et PASSWORD sont corrects ; si nécessaire, réinitialisez le PASSWORD.
- **L'utilisateur ne peut pas compléter la vérification 2FA**: Assurez-vous que l'heure du système est correcte, ou réinitialisez la 2FA pour l'utilisateur et reliez à nouveau.
- **Le menu de gestion des utilisateurs n'est pas visible**: Le compte actuel peut ne pas être le compte administrateur système désigné.
- **Suppression accidentelle d'utilisateur**: L'action de suppression ne peut pas être annulée directement ; le compte doit être recréé et les autorisations pertinentes reconfigurées.

> Identifiants générés lors de la création, de la réinitialisation PASSWORDet de la réinitialisation de la 2FA doivent être sauvegardés rapidement et fournis uniquement au titulaire du compte.
