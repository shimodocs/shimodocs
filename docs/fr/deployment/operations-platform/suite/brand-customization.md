# Personnalisation de la marque

[← ShimoDocs Suite documentation de déploiement](../../README.md)

> [!TIP]
>
> La personnalisation de la marque est utilisée pour unifier l'identité de la marque et le style de l'interface de ShimoDocs Suite. Ici, vous pouvez définir votre logo d'entreprise, les icônes du navigateur, les extensions de marque des onglets, les couleurs du thème, les coins arrondis des boutons, l'entrée de la page et le filigrane du système. 
>
> Lors de l'utilisation de cette fonctionnalité, il est recommandé de d'abord confirmer la portée effective de la configuration, puis de compléter la configuration dans l'ordre de l'identité de la marque, du style de l'interface, de l'entrée et du filigrane. 

> Lorsque les locataires ne sont pas sélectionnés, la configuration s'applique globalement à ShimoDocs Suite. La priorité pour le même élément de configuration est : ** Configuration du locataire > Configuration globale**. 

## 1. Accéder à la personnalisation de la marque 

1. Connectez-vous à **MDP Plateforme d'Opérations**. 
2. En haut, sélectionnez **ShimoDocs Suite**. 
3. Dans la barre de navigation à gauche, sélectionnez **Personnalisation de la marque**. 

## 2. Sélectionnez la portée effective de la configuration 

La personnalisation de la marque prend en charge la portée globale, locataire ou utilisateur. Avant de configurer, veuillez sélectionner la portée correspondante en fonction de vos besoins réels. 

| Portée de configuration | Comment choisir | Effet |
| --- | --- | --- |
| Configuration globale | Ne sélectionnez pas de locataires ou d’utilisateurs. | Effectif à l’échelle mondiale pour ShimoDocs Suite. |
| Configuration du locataire | Sélectionnez le locataire spécifié. | Effectif uniquement pour les locataires sélectionnés. |
| Configuration de l’utilisateur | Sélectionnez l’utilisateur spécifié. | Effectif uniquement pour les utilisateurs sélectionnés. |

Par exemple : si la couleur de thème globale est définie sur bleu et que la couleur de thème d’un certain locataire est définie sur vert, alors ce locataire utilisera le vert, tandis que les autres locataires non configurés individuellement utiliseront toujours le bleu.

## 3. Configurer l’identité de la marque

### 1. Logo de l’entreprise

En configurant « Modifier le logo principal de l’entreprise », vous pouvez contrôler si l’option de modification du logo de l’entreprise est affichée sur le **Gestion de l’entreprise > Informations de base sur l’entreprise** page dans ShimoDocs Suite.

Une fois activé, les administrateurs peuvent modifier le logo de l’entreprise sur la page des informations de base de l’entreprise.

### 2. Icône du navigateur

Grâce au paramètre « Modifier l’icône de la page du navigateur », vous pouvez remplacer l’icône affichée par ShimoDocs Suite sur l’onglet du navigateur.

Après configuration, vous pouvez voir l’effet d’affichage réel sur l’onglet du navigateur.

### 3. Suffixe de la marque sur l’onglet du navigateur

Grâce à la configuration « Suffixe de marque sur l’onglet du navigateur », vous pouvez définir le suffixe du nom de la marque affiché par ShimoDocs Suite dans l'onglet du navigateur.

Après la configuration, vous pouvez voir l'effet dans le titre de l'onglet du navigateur.

## 4. Style de l'interface de configuration

### 1. Couleur du thème

Grâce à la configuration "Couleur du thème", vous pouvez ajuster uniformément la couleur des boutons principaux, des états sélectionnés et du contenu mis en évidence dans ShimoDocs Suite.

Après avoir changé la couleur, vous pouvez prévisualiser l'effet réel de la couleur du thème sur la page.

### 2. Rayon des coins des boutons

Ajustez l'effet du rayon des coins des boutons dans ShimoDocs Suite grâce à la "Configuration du rayon des coins".

Après avoir ajusté les valeurs, la forme des coins du bouton changera en conséquence.

## 5. Configurer l'accès à la page et les informations de marque

### 1. Accès au site officiel

En configurant "Activer l'entrée sur le site officiel principal", vous pouvez contrôler si ShimoDocs l'accès au site officiel est affiché sur les cartes de visite personnelles.

Après activation, les utilisateurs peuvent voir l'accès au site officiel dans leur profil personnel.

### 2. Entrée « À propos »

Grâce au paramètre "Activer l'entrée À propos sur le site principal", vous pouvez contrôler si l'entrée "À propos" est affichée sur les profils personnels.

Après activation, les utilisateurs peuvent voir l'entrée 'À propos' sur leur profil personnel.

### 3. Informations de marque

Grâce au paramètre « Afficher les informations de la marque », vous pouvez contrôler si les informations de la marque sont affichées aux utilisateurs sur les pages concernées.

**Effet d'affichage :**

**Effet masqué :**

## 6. Configurer le filigrane du système

### 1. Filigrane du collaborateur

En configurant « Activer le filigrane intégré du collaborateur », vous pouvez contrôler le contenu du filigrane affiché lorsque les utilisateurs modifient ou prévisualisent les fichiers.

Le contenu du filigrane variera en fonction de si le visiteur est anonyme et du choix « afficher ou non les informations de l'utilisateur ». 

#### Accès non anonyme 

| Option de configuration | Contenu affiché du filigrane |
| --- | --- |
| Afficher/Masquer | Afficher le filigrane intégré du système, y compris les informations d'autorisation de base et les informations utilisateur. |
| Personnalisé | Afficher selon le contenu du filigrane du collaborateur défini dans ShimoDocs Suite l'entreprise. |

#### Accès anonyme

| Option de configuration | Contenu affiché du filigrane |
| --- | --- |
| Afficher/Masquer | Afficher le filigrane intégré du système, y compris les informations d'autorisation de base et les informations utilisateur. |
| Personnalisé | Afficher uniquement le texte personnalisé configuré pour les utilisateurs anonymes. |

Après avoir activé le filigrane intégré du collaborateur, le filigrane correspondant sera affiché de manière permanente lorsque les utilisateurs modifient ou prévisualisent le fichier.

### 2. Filigrane de la barre inférieure de l'éditeur

Grâce à la configuration « Modifier le filigrane intégré de la barre inférieure de l'éditeur », vous pouvez ajuster le filigrane intégré du système affiché en bas de l'éditeur.

Après la configuration, vous pouvez voir l'effet réel d'affichage en bas de l'éditeur.

## 7. Vérifier les résultats de la configuration

Après avoir terminé la configuration, il est recommandé de vérifier dans l'ordre suivant :

1. Confirmer si la portée de configuration actuellement sélectionnée est globale ou locataire.
2. Ouvrir le ShimoDocs Suite page dans la plage correspondante.
3. Actualisez la page et vérifiez le logo, l'icône du navigateur, le nom de l'onglet, la couleur du thème et l'état d'affichage de l'entrée.
4. Ouvrez le fichier en utilisant à la fois les méthodes non anonymes et anonymes, et confirmez que le contenu du filigrane s'affiche comme prévu.
5. Si l'effet réel ne correspond pas à l'attendu, veuillez d'abord vérifier s'il existe une configuration de locataire ayant une priorité plus élevée.
