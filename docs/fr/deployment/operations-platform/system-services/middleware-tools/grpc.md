# gRPC Outils

[← ShimoDocs Suite documentation de déploiement](../../../README.md)

> [!TIP]
>
> Le gRPC l'outil est utilisé pour se connecter aux services internes gRPC services, voir les Services et Méthodes, et lancer des appels de débogage de méthodes Unaires.
>
> La page prend en charge trois façons de sélectionner une cible : entrer l'adresse manuellement, sélectionner par Kubernetes Service, ou sélectionner par Pod.

## 1. Accès gRPC

1. Connectez-vous à la **MDP Plateforme d'Opérations**.
2. Sélectionner **Services Systèmes** en haut.
3. Développer **Outils Middleware** dans la barre de navigation gauche.
4. Sélectionner **gRPC**.

La page affiche d'abord la zone "Cible" pour sélectionner le gRPC service auquel se connecter.

## 2. Méthodes de sélection de la cible

La page propose trois modes de cible :

| Mode | Description |
| --- | --- |
| Manuel | Saisir manuellement l' gRPC adresse, par ex., `svc-user:50051`. |
| Service | Sélectionner la cible par Cluster, Namespace, Service et Port. |
| Pod | Sélectionner les cibles par Cluster, Namespace, Pod et Port. |

## 3. Connexion manuelle 

1. Sélectionner **Manuel**. 
2. Saisissez l'adresse gRPC adresse du service dans le **Adresse** champ de saisie. 
3. Cliquez **Connecter**. 
4. Après une connexion réussie, la page entrera dans l'espace de travail de débogage Service / Méthode. 

## 4. Connecter par Service

1. Sélectionner **Service**.
2. Sélectionnez le cluster cible et l'espace de noms.
3. Dans le **Service / Port** menu déroulant, sélectionnez le service et le port cible.
4. Si la liste des services n'est pas mise à jour, cliquez sur **Actualiser**.
5. Cliquez **Connecter**.

## 5. Connecter par Pod

1. Sélectionner **Pod**.
2. Sélectionnez le cluster cible et l'espace de noms.
3. Dans le **Pod / Port** menu déroulant, sélectionnez le Pod et le port cible.
4. Si la liste des Pods n'est pas mise à jour, cliquez sur **Actualiser**.
5. Cliquez **Connecter**.

## 6. Sélectionner le Service et la Méthode

Après une connexion réussie, la page est divisée en une liste de Services, une liste de Méthodes, une zone de requête et une zone de réponse.

1. Sélectionnez le service cible dans la liste des Services à gauche.
2. Vous pouvez utiliser la boîte de recherche de Services pour filtrer les services.
3. Sélectionnez la méthode cible dans la liste des Méthodes.
4. Options de filtrage des méthodes commutables : `Unary`, `Streaming`, `All`.
5. Vous pouvez utiliser la boîte de recherche de Méthodes pour filtrer les méthodes.

> La page actuelle ne supporte que l'appel des méthodes Unaires. Les méthodes de Streaming apparaîtront comme indisponibles.

## 7. Remplir les Paramètres de la Requête

La zone de requête supporte deux manières de remplir :

| Méthode | Description |
| --- | --- |
| Mode Formulaire | La page génère un formulaire basé sur les champs d'entrée de la méthode. |
| JSON Mode | Quand **JSON Mode** est activé, éditez directement le corps complet de la JSON requête. |

Étapes pour utiliser le Mode Formulaire :

1. Sélectionnez la Méthode cible.
2. Remplissez les champs de paramètres de la requête un par un.
3. Utilisez le menu déroulant pour sélectionner les champs énumérés.
4. Sélectionner `true` ou `false` pour les champs booléens.
5. Utilisez des virgules comme indiqué sur la page pour les champs répétés.

Étapes pour utiliser le JSON Mode :

1. Activez le **JSON Mode** commutateur.
2. Remplissez le corps complet JSON dans la zone de texte.
3. Assurez-vous que le JSON format est valide.

## 8. Remplir les Métadonnées

1. Développer **Métadonnées** dans la zone de requête.
2. Remplissez la Clé et la Valeur.
3. Pour ajouter plusieurs entrées de Métadonnées, cliquez sur **Ajouter**.
4. Pour supprimer une ligne, cliquez sur l'icône de suppression.

Les Métadonnées sont couramment utilisées pour transmettre des informations d'authentification, l'ID de la requête ou le contexte métier.

## 9. Initier l'appel et consulter la réponse

1. Confirmer la cible, le Service, la Méthode, le corps de la requête et les Métadonnées.
2. Cliquez **Invoquer** en haut à droite de la page.
3. Consulter le statut, le temps écoulé, les Métadonnées de la réponse et la réponse JSON dans la zone de réponse.
4. Si l'appel échoue, la zone de réponse affichera le statut d'erreur et le contenu de l'erreur.

## 10. Changer ou Reconnecter la Cible

1. Cliquez **Connecter** en haut pour recharger la définition du service de la cible actuelle.
2. Cliquez **Changer la Cible** pour revenir à la page de sélection de la cible.
3. Après avoir changé de cible, vous devez vous reconnecter et sélectionner à nouveau Service / Méthode.

## 11. Scénarios Courants de Dépannage

| Scénario | Suggestion d'opération |
| --- | --- |
| Vérifiez si le service est connectable | Sélectionnez la cible et cliquez **Connecter** pour voir si la liste de services peut être chargée. |
| Trouver les méthodes d'interface | Utilisez la recherche de Service et le filtrage de recherche de Méthode. |
| Déboguer l'interface de requête | Sélectionnez une méthode Unary, remplissez les paramètres de la requête, et cliquez **Invoquer**. |
| Nécessité de passer par le contexte | Développer les Métadonnées et remplir la Clé et la Valeur correspondantes. |
| La réponse est vide ou a échoué | Vérifiez le statut de la réponse, le contenu de l'erreur et les Métadonnées. |
