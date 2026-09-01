# Recherche d'informations sur les fichiers

[← ShimoDocs Suite documentation de déploiement](../../../README.md)

## Aperçu des fonctions

La requête d'information sur les fichiers est utilisée pour interroger les enregistrements de base des fichiers dans le système en fonction du fichier interne GUID ou de l'ID de fichier côté client, facilitant la vérification des identifiants de fichiers, des applications associées, des types de fichiers, du statut et de la taille du contenu.

Cette page est en lecture seule et ne modifiera pas le contenu ou le statut des fichiers.

## Accéder à la page

Après vous être connecté à l'interface de gestion, sélectionnez **Requête d'information sur les fichiers** dans la navigation de gauche pour accéder à la page.

## Interroger des fichiers

La page prend en charge les conditions de requête suivantes :

- **Fichier interne GUID**: le fichier `history_guid`.
- **Fournisseur de fichier client GUID**: le côté client `provider_file_id`.
- **ID de l'application**: optionnel, recommandé de remplir en même temps qu'avec le Fournisseur GUID pour spécifier l'application associée.

Au moins un des fichiers internes GUID ou le Fournisseur de Fichier Client GUID doit être rempli, puis cliquez sur **Requête**.

Si seul le Fournisseur GUID est rempli et l'ID de l'application n'est pas rempli, le système renverra tous les enregistrements correspondant au Fournisseur GUID, donc plusieurs résultats peuvent apparaître. 

### Obtention du Fichier GUID 
1. Dans le cas de ShimoDocs Suite, vous devez seulement utiliser l'adresse de la suite dans le navigateur en tant que **Fournisseur de fichier client GUID**. 

## Résultats de la requête

Les résultats de la requête incluent principalement :

- **id**: L'ID de clé primaire de l'enregistrement du fichier.
- **app_id**: L'ID de l'application associée.
- **provider_fichier_id**: ID du fichier côté client.
- **history_guid**: Fichier historique interne du système GUID.
- **created_at**: Heure de création de l'enregistrement.
- **type**: Type de fichier, tel que document, feuille de calcul, présentation, PDF, image ou vidéo.
- **created_by**: ID de l'utilisateur créateur.
- **status**: Valeur actuelle du statut du fichier.
- **fichier_content_taille**: Taille du contenu du fichier, en octets.

Les types de fichiers dans les résultats afficheront à la fois le numéro de type et le nom correspondant pour faciliter l'identification.

## Situations courantes

- **Invite : Les conditions de requête doivent être saisies**: Veuillez remplir au moins le fichier interne GUID ou le Fournisseur GUID.
- **Fichier introuvable**: Veuillez vérifier si l'identifiant est complet, ou confirmer si le fichier appartient à l'environnement actuel.
- **Plusieurs enregistrements retournés**: Le Fournisseur GUID peut être dupliqué dans plusieurs applications ; veuillez ajouter l'ID de l'application et rechercher à nouveau.
- **Le type de fichier apparaît comme inconnu**: Le numéro de type de l'enregistrement actuel peut ne pas encore avoir de nom correspondant configuré. Vous pouvez fournir le numéro au support technique pour confirmation.
- **Impossible de déterminer la valeur du statut**: Le champ de statut est une valeur d'enregistrement sous-jacente et nécessite une analyse supplémentaire combinée aux phénomènes et journaux métier spécifiques.

> Les identifiants de fichiers appartiennent aux données métier ; veuillez éviter de les partager directement dans des chats publics ou des tickets externes.
