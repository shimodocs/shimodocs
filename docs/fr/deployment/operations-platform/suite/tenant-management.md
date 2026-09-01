# Gestion des locataires

[← ShimoDocs Suite documentation de déploiement](../../README.md)

> [!TIP]
>
> La gestion des locataires est utilisée pour la gestion centralisée des locataires dans ShimoDocs Suite.
> Les administrateurs peuvent consulter ici le nombre de locataires et l'utilisation des sièges, obtenir les identifiants d'intégration de tiers, gérer les configurations AI, ainsi que créer, modifier, activer ou désactiver des locataires.
>

## 1. Accéder à la gestion des locataires

1. Connectez-vous à **MDP Plateforme d'Opérations**.
2. Sélectionner **ShimoDocs Suite** en haut.
3. Sélectionner **Gestion des locataires** dans la barre de navigation gauche.

## 2. Comprendre la page de gestion des locataires

Les informations globales du système actuel sont affichées en haut de la page :

| Région | Description |
| --- | --- |
| API-KEY | Afficher et copier les `AppID` et `AppSecret` nécessaires pour l'intégration avec des tiers. |
| Paramètres AI | Vérifiez si les capacités AI sont activées et accédez aux configurations du modèle et de la recherche AI. |
| Nombre total de locataires | Le nombre de locataires qui ont été créés dans le système actuel. |
| Locataires activés | Le nombre de locataires actuellement activés. |
| Utilisation des sièges | Nombre de sièges utilisés, total des sièges dans le système et taux d'utilisation. |

La liste des locataires ci-dessous affiche le nom du locataire, la date d'activation, l'administrateur du locataire, l'utilisation des sièges et l'état actuel. Dans la colonne 'Actions', vous pouvez modifier ou désactiver le locataire correspondant.

## 3. Voir API Clé

`AppID` et `AppSecret` sont utilisés pour l'authentification lors de ShimoDocs Suite s'intègre avec des systèmes tiers.

### Étapes opérationnelles

1. Trouvez la **API-KEY** carte sur la page de gestion des locataires.
2. Cliquez sur l'icône de copie.
3. Le système copiera l'environnement actuel `AppID` et `AppSecret`.
4. Entrez les identifiants dans la configuration correspondante de l'intégration tierce.

> `AppSecret` est un identifiant sensible. Veuillez le garder en sécurité et ne pas l'écrire dans des documents publics, des conversations ou des dépôts de code accessibles au public.

## 4. Gestion de la configuration AI

La carte de configuration AI affiche l'état actuel de l'activation des capacités AI.

Après avoir cliqué sur la carte de configuration AI, vous pouvez consulter ou modifier le contenu suivant sur la page "Configuration du modèle et de la recherche AI" : 

### 1. Configuration de modèle de base

Utilisé pour configurer les modèles de langage général (LLM) et leurs modèles disponibles. Sur cette page, vous pouvez voir des informations telles que le fournisseur, la clé de requête, URL, API le modèle par défaut, l'ID du modèle, la fenêtre de contexte et les capacités d'entrée.

### 2. Configuration de modèle d'image

Utilisé pour configurer les modèles de génération ou d'édition d'images. Sur la page, vous pouvez voir le fournisseur, le nom du modèle, la clé de base URL, API et les capacités d'image prises en charge.

### 3. Configuration du moteur de recherche réseau

Utilisé pour configurer le service de recherche réseau AI. Sur la page, vous pouvez voir le fournisseur de service, l'adresse de l'interface, API la clé et le délai d'attente.

### 4. Configuration du fournisseur d'embedding

Utilisé pour configurer le service de vectorisation de texte. Sur la page, vous pouvez voir la clé de base, URL, API le modèle d'embedding et les dimensions du vecteur.

> Avant de modifier la configuration de l'IA, veuillez d'abord confirmer que l'adresse du service, API la clé, l'identifiant du modèle et les paramètres de capacité sont tous corrects. Après modification, il est recommandé d'utiliser une petite quantité de contenu test pour vérifier si l'appel du modèle fonctionne correctement.

### 5. Utilisation de l'IA dans ShimoDocs Suite
Après la configuration terminée, vous pouvez utiliser les fonctionnalités d'IA dans ShimoDocs Suite.

## 5. Gestion des locataires existants

Dans la liste des locataires, vous pouvez consulter les informations de base et l'utilisation des places de chaque locataire.

### Modifier le locataire

1. Trouvez le locataire qui nécessite un ajustement.
2. Cliquez sur 'Modifier' dans la colonne 'Actions'.
3. Modifiez les informations du locataire ou le nombre de places selon les instructions de la page.
4. Enregistrez les modifications et retournez à la liste pour confirmer que les informations ont été mises à jour.

### Désactiver ou Restaurer un Locataire

- Pour les locataires actuellement activés, vous pouvez cliquer sur 'Désactiver' dans la colonne 'Actions'.
- Pour restaurer un locataire désactivé, réactivez-le dans les éléments d'action du locataire correspondant.

> La désactivation d'un locataire affectera l'accès normal à ce locataire. Veuillez confirmer que le locataire ciblé est correct avant de procéder et planifier l'opération en fonction de l'utilisation réelle.

## 6. Activation d'un Nouveau Locataire

Avant d'activer le locataire, veuillez d'abord vérifier l'utilisation des sièges en haut de la page pour confirmer qu'il reste des sièges disponibles pour l'allocation.

### Étapes de fonctionnement

1. Cliquez sur "Activer un Nouveau Locataire" dans le coin supérieur droit de la page.
2. Entrez le nom utilisé pour identifier ce locataire dans "Nom du Locataire".
3. Confirmez l'adresse e-mail de l'administrateur du locataire générée par le système. Une fois le locataire créé avec succès, veuillez enregistrer ce compte administrateur et l'initial PASSWORD rapidement.
4. Vérifiez « Sièges attribuables » pour connaître le nombre maximum de sièges pouvant actuellement être attribués au nouveau locataire.
5. Saisissez le nombre de sièges attribués à ce locataire dans « Sièges attribués au locataire ».
6. Après avoir confirmé que les informations sont correctes, cliquez sur « Enregistrer ».

### Description du champ de siège

| Champ | Description |
| --- | --- |
| Sièges attribuables | Le nombre maximum de sièges pouvant actuellement être attribués au locataire par le système. |
| Sièges attribués au locataire | Le nombre total de sièges attribués à ce locataire. Ce nombre ne peut pas être inférieur au nombre de sièges déjà utilisés par le locataire. |
| Sièges utilisés par le locataire | Le nombre de membres d'entreprise actifs dans ce locataire. Chaque membre actif occupe un siège. |

> Un certain nombre de sièges doivent être attribués lors de la création d'un locataire. Le nombre de sièges peut être ajusté ultérieurement en fonction de l'utilisation réelle.

## 7. Première connexion et changement de l'identifiant initial PASSWORD

Après la création réussie du locataire, veuillez vous connecter à ShimoDocs Suite en utilisant le compte par défaut généré par le système ou le compte administrateur et changez immédiatement l'identifiant initial PASSWORD.

### 1. Ouvrez ShimoDocs Suite

Accédez dans le navigateur :

```text
http://<ACCESS_DOMAIN>/
```

Si HTTPS est déjà configuré, veuillez visiter : 

```text
https://<ACCESS_DOMAIN>/
```

### 2. Connectez-vous à ShimoDocs Suite

Entrez le compte administrateur et l'identifiant initial PASSWORD créé lors de la configuration du locataire pour compléter la connexion.

### 3. Changez l'identifiant initial PASSWORD

Après la première connexion, veuillez suivre les instructions de la page ou les paramètres de sécurité du compte pour changer l'identifiant initial PASSWORD. Une fois le nouvel PASSWORD est défini, veuillez le conserver correctement.

