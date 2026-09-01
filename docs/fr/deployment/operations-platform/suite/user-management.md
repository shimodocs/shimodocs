# Gestion des utilisateurs de la suite

[← ShimoDocs Suite documentation de déploiement](../../README.md)

> [!TIP]
>
> La gestion des utilisateurs est utilisée pour afficher les utilisateurs dans chaque locataire de ShimoDocs Suite et pour activer ou désactiver les comptes utilisateurs en masse. 
>
> Pour utiliser cette fonctionnalité, vous devez d'abord sélectionner le locataire où se trouve l'utilisateur, puis saisir la liste d'utilisateurs correspondante pour la gestion. 

> La page de gestion des utilisateurs affiche la plage de locataires où se trouve l'utilisateur en utilisant le nom de "l'Équipe". 

## 1. Entrer dans la gestion des utilisateurs 

1. Connectez-vous à **MDP Plateforme d'Opérations**. 
2. En haut, sélectionnez **ShimoDocs Suite**. 
3. Dans la barre de navigation à gauche, sélectionnez **Gestion des utilisateurs**. 

## 2. Sélectionnez l'équipe (locataire) que vous souhaitez gérer 

Après être entré dans la page de gestion des utilisateurs, le système affichera d'abord la liste des équipes. Veuillez sélectionner l'équipe (locataire) à laquelle appartient l'utilisateur dans la liste. 

### Trouver l'équipe (Locataire)

1. Dans la boîte de recherche en haut de la page, saisissez le nom de l'équipe, le créateur ou l'ID. 
2. Dans les résultats de recherche, confirmez le nom de l'équipe, le créateur, le nombre d'activateurs, la capacité et la date d'expiration. 
3. Cliquez sur le nom de l'équipe pour entrer dans la liste des utilisateurs de cette équipe. 

### Description du champ de la liste des équipes

| Champ | Description |
| --- | --- |
| ID | L'identifiant unique de l'équipe dans le système. |
| Nom de l'équipe | Le nom de l'équipe. Cliquez pour accéder à la liste des utilisateurs de cette équipe. |
| Créateur | Le compte qui a créé cette équipe. |
| Activé / Capacité | Le nombre actuel d'utilisateurs activés et le nombre de places disponibles pour l'équipe. |
| Date d'expiration | La date d'expiration du service actuel pour cette équipe. |

> Si le contenu de la liste n'est pas mis à jour à temps, vous pouvez cliquer sur « Actualiser » à droite de la zone de recherche.

## 3. Voir les utilisateurs du locataire

Après avoir cliqué sur le nom du locataire, la page affichera la liste des utilisateurs de ce locataire.

### Description du champ de la liste des utilisateurs

| Champ | Description |
| --- | --- |
| ID | L'identifiant unique de l'utilisateur dans le système. |
| USERNAME | Le nom affiché pour l'utilisateur dans ShimoDocs Suite. |
| Email | L'email lié au compte utilisateur. |
| Rôle | Le rôle de l'utilisateur dans le locataire actuel, tel qu'Administrateur ou Membre. |
| Statut | Indique si le compte utilisateur est actuellement activé. |

## 4. Rechercher des utilisateurs

Lorsqu'il y a de nombreux utilisateurs dans le locataire, vous pouvez utiliser la fonction de recherche pour trouver rapidement l'utilisateur cible.

1. Saisissez l'adresse USERNAME, email ou identifiant utilisateur dans la zone de recherche au-dessus de la liste des utilisateurs.
2. Vérifiez le USERNAME, email et rôle dans les résultats de recherche pour vous assurer d'avoir trouvé l'utilisateur cible.
3. Pour recharger la liste, cliquez sur « Actualiser » à droite de la zone de recherche.

## 5. Activation en masse des utilisateurs

Lorsqu'un compte utilisateur est désactivé et doit être restauré, vous pouvez activer des utilisateurs en masse.

### Étapes

1. Confirmez que la page actuelle affiche le locataire cible.
2. Cochez un ou plusieurs utilisateurs à activer sur le côté gauche de la liste des utilisateurs.
3. Confirmez que le nombre affiché comme « Sélectionné » en haut à droite de la page est correct.
4. Cliquez sur « Activer en masse ».
5. Confirmez l'opération selon les indications de la page.
6. Après l'opération, actualisez la liste et confirmez que le statut de l'utilisateur est passé à « Activé ».

## 6. Désactivation en lot des utilisateurs

Lorsque les utilisateurs n'ont temporairement pas besoin d'utiliser ShimoDocs Suite, leurs comptes peuvent être désactivés en lot.

### Étapes

1. Confirmez que la page actuelle affiche le locataire cible.
2. Cochez un ou plusieurs utilisateurs à désactiver sur le côté gauche de la liste des utilisateurs.
3. Confirmez que le nombre affiché dans « Sélectionné » en haut à droite de la page est correct.
4. Cliquez sur « Désactiver en lot ».
5. Confirmez l'opération selon les indications de la page.
6. Après l'opération terminée, actualisez la liste et confirmez que le statut de l'utilisateur a été mis à jour.

> Désactiver des utilisateurs affectera l'utilisation normale de ShimoDocs Suite pour ce compte. Veuillez vérifier le locataire, USERNAME, l'e-mail et le rôle avant d'opérer afin d'éviter d'affecter par erreur d'autres utilisateurs.

## 7. Retour à la liste des locataires

Si vous avez besoin de passer à un autre locataire :

1. Cliquez sur "Retour à la liste des équipes" en haut à gauche de la page.
2. Recherchez et sélectionnez à nouveau le locataire cible dans la liste des locataires.
