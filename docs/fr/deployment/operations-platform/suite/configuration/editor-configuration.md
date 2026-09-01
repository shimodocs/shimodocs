# Configuration de l'éditeur

[← ShimoDocs Suite documentation de déploiement](../../../README.md)

## 1. Instructions du manuel

Ce manuel présente la fonctionnalité « Configuration de l'éditeur » de ShimoDocs Suite, adaptée aux administrateurs système et aux implémenteurs utilisant cette fonctionnalité pour la première fois. Vous pouvez suivre les étapes de ce document pour trouver les éléments de configuration, modifier les interrupteurs de fonctionnalités ou les quotas, vérifier s'ils prennent effet, et restaurer les paramètres d'origine si nécessaire.

**Règle de portée la plus importante** Lorsque l'ID de l'équipe est laissé vide, la configuration par défaut de l'application est consultée et modifiée ; lorsqu'un ID d'équipe est saisi, la configuration de l'équipe correspondante est consultée et modifiée. Modifier la configuration par défaut de l'application peut affecter plusieurs équipes, veuillez donc confirmer à nouveau la portée de configuration avant de sauvegarder.

### 1.1 Point d'accès

Backend Admin > ShimoDocs Suite > Gestion de la configuration > Configuration de l'éditeur

### 1.2 Préparations avant utilisation

- Confirmez que votre compte de connexion dispose de l'autorisation pour afficher et modifier ShimoDocs Suite les configurations de l'éditeur.
- Confirmez d'abord si la portée cible est l'application par défaut ou une équipe spécifique, et obtenez l'ID de l'équipe précis.
- Confirmez les noms des éléments de configuration à partir des exigences de configuration ou de l'Annexe A. Les noms des éléments de configuration sont des identifiants uniques ; ne devinez pas uniquement à partir des noms de fonctionnalités chinois.
- Enregistrez la source, les valeurs effectives et l'état de restriction avant la modification ; pour les configurations importantes, préparez également les valeurs de restauration.
- Les configurations par défaut de l'application ont un impact considérable ; il est recommandé de les modifier pendant les périodes de faible activité et de prévenir à l'avance les personnes responsables concernées.

## 2. Portée et priorité de la configuration

La configuration de l'éditeur prend en charge deux portées : Par défaut de l'application et Équipe. Avant d'apporter des modifications, vous devez vérifier à la fois l'ID de l'équipe et la 'Dimension actuelle' en haut de la page.

| **Domaine fonctionnel** | **Portée par défaut de l'application** | **Portée de l'équipe** | **Indicateur de reconnaissance de page** |
| --- | --- | --- | --- |
| Configuration de l'éditeur | Laisser l'ID de l'équipe vide | Saisir un ID d'équipe entier positif | La Dimension actuelle affiche 'Par défaut de l'application' ou 'Équipe' |

*Figure 1 Correspondance entre l'ID de l'équipe et la Dimension actuelle*

### 2.1 Portée par défaut de l'application

- Laisser l'ID de l'équipe vide.
- Le haut de la page affiche « Dimension actuelle : Par défaut de l'application. »
- La valeur par défaut de l'application est la valeur de base lorsqu'aucun remplacement de l'équipe n'est défini ; la modifier peut affecter plusieurs équipes.
- Avant de sauvegarder, confirmez à nouveau que l'ID de l'équipe est bien vide afin d'éviter d'écrire par erreur les exigences de l'équipe comme paramètres par défaut de l'application.

### 2.2 Portée de l'équipe

- Entrez l'ID entier positif de l'équipe cible dans le champ ID de l'équipe, puis cliquez sur « Rechercher ».
- Le haut de la page affiche « Dimension actuelle : Équipe ».
- La configuration au niveau de l'équipe n'affecte que l'équipe correspondant à l'ID de l'équipe saisi et ne modifie pas directement la configuration des autres équipes.
- Lorsqu'un élément de configuration dispose d'un paramètre au niveau de l'équipe, la valeur effective de l'équipe prend le dessus sur la valeur par défaut de l'application.

### 2.3 Remplacement, Héritage et Restauration

- Si l'équipe actuelle n'a pas de remplacement personnalisé, la configuration par défaut de l'application ou la valeur par défaut du système est utilisée.
- La colonne « Source » dans la liste peut aider à déterminer si la valeur actuelle provient de la valeur par défaut du système, d'un remplacement de l'application ou d'un remplacement de l'équipe.
- Après avoir supprimé le remplacement du niveau actuel, la configuration hérite généralement de la valeur du niveau supérieur ; confirmez le résultat après l'héritage avant la suppression.
- Après modification ou restauration, effectuez une nouvelle recherche de l'ID de l'équipe et de l'élément de configuration pour confirmer que la source et la valeur effective répondent aux attentes.

**Avertissement de risque** Ne sauvegardez pas directement sans confirmer la dimension actuelle. Lorsque l'ID de l'équipe est vide, les opérations seront écrites dans la portée par défaut de l'application, ce qui peut affecter plusieurs équipes. 

## 3. Configuration de l'éditeur 

La configuration de l'éditeur est utilisée pour afficher et ajuster les interrupteurs de fonctionnalités, les quotas d'utilisation et la configuration structurée de ShimoDocs Suite éditeur. Vous pouvez filtrer par type ou développer le "Filtre Avancé" et entrer le nom de l'élément de configuration dans la "Liste Blanche des Noms" pour une recherche précise. 

### 3.1 Champs de Page 

| **Champ** | **Description** | 
| --- | --- | 
| ID de l'Application | Actuel ShimoDocs Suite identifiant de l'application, utilisé uniquement pour confirmer le contexte. | 
| Dimension Actuelle | "Par Défaut de l'Application" lorsque l'ID d'Équipe est vide ; "Équipe" après que l'ID d'Équipe est rempli. | 
| ID d'Équipe | Identifiant de l'équipe ; n'accepte que des entiers positifs. | 
| Type | Les options incluent Tous, Fonctionnalité, Quota à Valeur Unique, Quota de Plage, ou JSON Configuration. | 
| Filtre Avancé | Développez la zone de saisie pour les noms des éléments de configuration. | 
| Liste blanche des noms | Saisissez les noms des éléments de configuration ; un par ligne ou séparés par des virgules anglaises. | 
| Source | Indique si la valeur actuelle provient de la valeur par défaut du système, de la substitution de l'application ou de la substitution de l'équipe. | 
| Valeur effective | L'interrupteur, le quota ou la configuration structurée réel utilisé dans le périmètre actuel. | 
| Action | Icône crayon pour modifier ; icône de suppression pour retirer la substitution de la couche actuelle. | 

*Figure 2 Zone de requête de configuration de l'éditeur, liste des résultats et colonne d'action*

### 3.2 Recherche précise

1. Décidez de remplir l'ID de l'équipe en fonction du périmètre de configuration : laissez vide pour le périmètre par défaut de l'application, ou remplissez pour l'équipe correspondante.
2. Sélectionnez « Type » selon les besoins ; si vous n'êtes pas sûr du type, laissez sur « Tous ».
3. Cliquez sur « Filtre avancé » pour développer la « Liste blanche des noms ».
4. Saisissez le nom complet de l'élément de configuration. Pour plusieurs noms, saisissez-en un par ligne ou séparez-les par des virgules.
5. Cliquez sur « Requête » pour vérifier les noms, types, sources et valeurs effectives dans les résultats.
6. Cliquez sur l'icône en forme de crayon à l'extrême droite de la ligne cible pour ouvrir la fenêtre de modification.

*Figure 3 Remplissez la liste blanche des noms après avoir cliqué sur 'Filtre avancé'*

*Figure 4 Résultat unique après une recherche précise pour modification_limite_mosheet_taille*

**Conseils de fonctionnement** Si vous ne voyez pas les icônes d'action, faites défiler la liste horizontalement jusqu'à l'extrême droite ou agrandissez la zone visible du navigateur.

### 3.3 Recherche directe dans la liste

- Tout d'abord, confirmez que les filtres ID d'équipe et type sont corrects, puis faites défiler les résultats de la requête.
- Utilisez à la fois le “Nom” et le “Type” pour confirmer la configuration cible ; ne vous fiez pas uniquement à la valeur actuelle.
- Le nombre d’enregistrements affichés sur la page peut changer en fonction de la version déployée et des éléments de configuration pris en charge par l’application actuelle.
- Après avoir trouvé la ligne cible, cliquez sur l'icône en forme de crayon à l'extrême droite pour entrer en mode édition.

### 3.4 Modification de la configuration

#### 3.4.1 Fonction

Le type de fonction est utilisé pour contrôler si une capacité est disponible. Après avoir ouvert la fenêtre de modification, sélectionnez le statut fourni sur la page tel que « Support » ou « Masquer » dans la liste déroulante « Valeur effective », puis cliquez sur « Enregistrer ». Certains noms d'éléments de configuration incluent une sémantique inversée comme non supporté ou désactivé, veuillez donc juger la signification réelle en fonction du nom de l'élément et de la description.

*Figure 5 Paramètres de valeur effective des éléments de configuration de type fonction*

#### 3.4.2 Quota à valeur unique

Un quota à valeur unique inclut généralement un commutateur de « vérification de limite » et une « valeur maximale ». Lorsque la vérification de limite est activée, le système validera selon la valeur maximale ; lorsque désactivée, elle est généralement affichée comme « illimitée ». La valeur maximale doit être comprise dans la plage autorisée du paramètre et être cohérente avec l'unité dans le nom du paramètre, telle que MB, GB, pages, éléments ou caractères.

*Figure 6 Validation du quota à valeur unique et définition de la valeur maximale*

#### 3.4.3 Quota de plage

- Les quotas de plage fournissent généralement à la fois une valeur minimale et une valeur maximale.
- La valeur minimale ne peut pas être supérieure à la valeur maximale, et la valeur saisie doit être dans la plage autorisée donnée sur la page ou dans l’annexe.
- Si la page propose une option « Pas de vérification » ou « Sans limite », confirmez d’abord si la fonction actuelle prend en charge ce paramètre.
- Après avoir enregistré, vérifiez les valeurs limites dans la fonction métier réelle afin d’éviter de ne vérifier que l’affichage dans le back-end de configuration.

#### 3.4.4 JSON Configuration

- JSON La configuration doit maintenir une structure valide, y compris les guillemets appariés, virgules, crochets et types de données corrects.
- Enregistrez la valeur originale complète avant d’effectuer des modifications ; ne consignez pas seulement un champ.
- Lorsque la signification du champ n’est pas claire, n’ajoutez, ne supprimez ni ne renommez les champs de manière arbitraire.

### 3.5 Enregistrer et supprimer

- Avant d’enregistrer, reconfirmez la dimension actuelle, l’ID de l’équipe, le nom de l’élément de configuration, le type, l’unité et la nouvelle valeur.
- Après avoir enregistré, interrogez à nouveau la même plage et le même élément de configuration pour confirmer que les valeurs source et effectives ont été mises à jour.
- L'icône de suppression est généralement utilisée pour supprimer l'enregistrement de remplacement de la plage actuelle, et non pour supprimer l'élément de configuration lui-même.
- Ce manuel ne répertorie que les éléments de configuration qui peuvent être consultés et modifiés sur la page actuelle ; les éléments réellement affichés peuvent varier en fonction de la version déployée et des capacités de support de l'application actuelle.

### 3.6 Description de l'élément de configuration

L'annexe A ne comprend que les éléments de configuration de l'éditeur qui peuvent être consultés et modifiés sur la page actuelle ; la plage visible spécifique est soumise à la version de déploiement actuelle et à l'affichage réel de la page.

## 4. Vérification de l'effet et restauration

### 4.1 Vérification après enregistrement

- Sur la page de configuration de l'éditeur, réinterrogez le même ID d'équipe et le même élément de configuration pour confirmer la source, la valeur effective et le statut de restriction.
- Entrez dans l'éditeur ou la page de fonction qui utilise réellement cette configuration pour vérifier si la fonction est visible, si le quota est effectif ou si la restriction est levée.
- Lors de l'application de la configuration par défaut, vérifiez avec au moins une équipe qui n'a pas de configuration au niveau de l'équipe ; pour les configurations au niveau de l'équipe, ne vérifiez que l'ID d'équipe cible.
- Actualisez la page, réentrez dans l'éditeur, reconnectez-vous ou attendez la mise à jour du cache si nécessaire.
- Enregistrez l'heure de modification, l'opérateur, la portée de la configuration, l'ID d'équipe, le nom de l'élément de configuration, les valeurs avant et après modification, et les résultats de la vérification.

### 4.2 Restauration

- Si la valeur d'origine a été enregistrée, rééditez et réécrivez la valeur d'origine.
- Si seul le remplacement de champ actuel doit être supprimé, utilisez l'icône de suppression et confirmez la valeur héritée du niveau supérieur après la suppression.
- Après restauration, interrogez à nouveau la source et la valeur effective, et entrez sur la page métier pour vérifier à nouveau.
- Si l'application de la modification de configuration par défaut provoque des anomalies, restaurez d'abord la valeur par défaut, puis vérifiez si une équipe a des remplacements indépendants.

**Note importante** Après avoir supprimé le remplacement actuel, la valeur héritée peut apparaître immédiatement sur la page. Avant la suppression, la configuration du niveau supérieur doit être confirmée pour répondre aux attentes, et un enregistrement de l'état avant modification doit être conservé.

## 5. Foire Aux Questions

| **Problème** | **Solution** |
| --- | --- |
| Impossible de trouver la zone de saisie du nom de l'élément de configuration | Cliquez sur « Filtre Avancé » pour développer la « Liste Blanche des Noms ». |
| Impossible de trouver l'icône d'édition ou de suppression | Faites défiler horizontalement la liste jusqu'à l'extrême droite, ou agrandissez la fenêtre du navigateur. |
| La recherche exacte ne renvoie aucun résultat | Vérifiez la casse du nom, les underscores, l'ID de l'équipe et les filtres de type ; supprimez les conditions de filtre trop strictes et essayez à nouveau. |
| Après avoir saisi l'ID de l'équipe, il n'est toujours pas dans la dimension de l'équipe | L'ID de l'équipe doit être un entier positif valide ; après saisie, cliquez à nouveau sur « Interroger » et vérifiez « Dimension actuelle » en haut de la page. |
| Après la sauvegarde, la page métier ne change pas | Vérifiez si le mauvais scope est sélectionné, si l'équipe l'a remplacé, si un rafraîchissement ou une reconnexion est nécessaire, et si l'élément de configuration s'applique à la fonction actuelle. |
| L'icône de suppression n'est pas disponible | La portée actuelle peut ne pas avoir de remplacements personnalisés et utilise la valeur par défaut du système ou une valeur héritée d'un niveau supérieur. |
| Échec de l'enregistrement du quota | Vérifiez la plage de valeurs, l'unité, la relation entre les valeurs minimale et maximale, et confirmez si « Illimité » est autorisé. |
| JSON Échec de l'enregistrement de la configuration | Utiliser valide JSON; vérifiez les guillemets, les virgules, les crochets et les types de champs ; si vous n'êtes pas sûr, restaurez la valeur originale complète avant modification. |

## Annexe A : Index des éléments de configuration de l'éditeur

L'index suivant liste uniquement les éléments de configuration de l'éditeur qui peuvent être interrogés et modifiés sur la page actuelle ; la portée visible spécifique dépend de la version actuellement déployée.

| **Nom de l'élément de configuration** | **Catégorie / Description de la fonction** | **Type** | **Valeur par défaut / Plage optionnelle** | **Méthode de configuration** |
| --- | --- | --- | --- | --- |
| exporter_modoc_docx | Exporter | Commutateur de fonctionnalité | Activé | Configurable sur la page |
| exporter_modoc_img | Exporter | Commutateur de fonctionnalité | Activé | Configurable sur la page |
| exporter_modoc_pdf | Exporter | Commutateur de fonctionnalité | Activé | Configurable sur la page |
| exporter_modoc_pdf_img | Exporter | Commutateur de fonctionnalité | Activé | Configurable sur la page |
| exporter_modoc_wps | Exporter | Commutateur de fonctionnalité | Activé | Configurable sur la page |
| exporter_mosheet_img | Exporter | Commutateur de fonctionnalité | Activé | Configurable sur la page |
| exporter_mosheet_pdf_img | Exporter | Commutateur de fonctionnalité | Activé | Configurable sur la page |
| exporter_mosheet_unique_feuille_csv | Exporter | Commutateur de fonctionnalité | Activé | Configurable sur la page |
| exporter_mosheet_unique_feuille_pdf_img | Exporter | Commutateur de fonctionnalité | Activé | Configurable sur la page |
| exporter_mosheet_unique_feuille_xlsx | Exporter | Commutateur de fonctionnalité | Activé | Configurable sur la page |
| exporter_mosheet_xlsx | Exporter / Table | Commutateur de fonctionnalité | Activé | Configurable sur la page |
| exporter_mosheet_zip | Exporter | Commutateur de fonctionnalité | Activé | Page configurable |
| exporter_Présentation_img | Exporter | Commutateur de fonctionnalité | Activé | Page configurable |
| exporter_Présentation_pdf | Exporter | Commutateur de fonctionnalité | Activé | Page configurable |
| exporter_Présentation_pdf_img | Exporter | Commutateur de fonctionnalité | Activé | Page configurable |
| exporter_Présentation_pptx | Exporter | Commutateur de fonctionnalité | Activé | Page configurable |
| exporter_rdoc_docx | Exporter | Commutateur de fonctionnalité | Activé | Page configurable |
| exporter_rdoc_img | Exporter | Commutateur de fonctionnalité | Activé | Page configurable |
| exporter_rdoc_md | Exporter | Commutateur de fonctionnalité | Activé | Page configurable |
| exporter_rdoc_pdf | Exporter | Commutateur de fonctionnalité | Activé | Page configurable |
| exporter_Table_xlsx | Exporter / Table des applications | Commutateur de fonctionnalité | Activé | Page configurable |
| Formulaire_Notification | Édition de formulaire / Définir les alertes de notification (Alertes de réponse, Mises à jour d'abonnement) | Commutateur de fonctionnalité | Activé | Page configurable |
| Importer_Convertir_svg | Importer / Télécharger / Type de format de pièce jointe de conversion forcée | Commutateur de fonctionnalité | Activé | Page configurable |
| Importer_Carte mentale_xmind | Importer/Télécharger / Carte mentale | Commutateur de fonctionnalité | Activé | Page configurable |
| Importer_modoc_doc | Importer/Télécharger | Commutateur de fonctionnalité | Activé | Page configurable |
| Importer_modoc_docx | Importer/Télécharger | Commutateur de fonctionnalité | Activé | Page configurable |
| Importer_modoc_wps | Importer/Télécharger | Commutateur de fonctionnalité | Activé | Page configurable |
| Importer_modoc_wpt | Importer/Télécharger | Commutateur de fonctionnalité | Activé | Page configurable |
| Importer_mosheet_csv | Importer/Télécharger | Commutateur de fonctionnalité | Activé | Page configurable |
| Importer_mosheet_xls | Importer/Télécharger | Commutateur de fonctionnalité | Activé | Page configurable |
| Importer_mosheet_xlsm | Importer/Télécharger | Commutateur de fonctionnalité | Activé | Page configurable |
| Importer_mosheet_xlsx | Importer/Télécharger | Commutateur de fonctionnalité | Activé | Page configurable |
| Importer_Présentation_ppt | Importer/Télécharger | Commutateur de fonctionnalité | Activé | Page configurable |
| Importer_Présentation_pptx | Importer/Télécharger | Commutateur de fonctionnalité | Activé | Page configurable |
| Importer_rdoc_doc | Importer/Télécharger | Commutateur de fonctionnalité | Activé | Page configurable |
| Importer_rdoc_docx | Importer/Télécharger | Commutateur de fonctionnalité | Activé | Page configurable |
| Importer_rdoc_md | Importer/Télécharger | Commutateur de fonctionnalité | Activé | Page configurable |
| Importer_rdoc_txt | Importer/Télécharger | Commutateur de fonctionnalité | Activé | Page configurable |
| Importer_Table_csv | Importer/Télécharger | Commutateur de fonctionnalité | Activé | Page configurable |
| Importer_Table_xls | Importer/Télécharger | Commutateur de fonctionnalité | Activé | Page configurable |
| Importer_Table_xlsx | Importer/Télécharger | Commutateur de fonctionnalité | Activé | Page configurable |
| Importer_Non pris en charge_Pièce jointe_svg | Importer/Télécharger | Commutateur de fonctionnalité | Activé | Page configurable |
| Importer_Non pris en charge_Pièce jointe_xml | Importer/Télécharger | Commutateur de fonctionnalité | Activé | Page configurable |
| Mosheet_Combiner_Feuilles | Édition de feuille de calcul / Combiner les feuilles | Commutateur de fonctionnalité | Caché/Éteint | Page configurable |
| Mosheet_date_mention | Édition de feuille de calcul / Rappel de date | Commutateur de fonctionnalité | Activé | Page configurable |
| Mosheet_suivre_mode | Édition de feuille de calcul / Mode de suivi | Commutateur de fonctionnalité | Activé | Page configurable |
| Mosheet_suivre_sélection | Édition de feuille de calcul / Suivre la sélection | Commutateur de fonctionnalité | Cacher/Fermer | Page configurable |
| Mosheet_importer_plage | Édition de feuille de calcul / Référence entre feuilles | Commutateur de fonctionnalité | Cacher/Fermer | Page configurable |
| Mosheet_indépendant_zone d'affichage | Édition de feuille de calcul / Vue indépendante | Commutateur de fonctionnalité | Cacher/Fermer | Page configurable |
| présentation_à distance_démo | Édition de diapositive / Présentation à distance | Commutateur de fonctionnalité | Cacher/Fermer | Page configurable |
| aperçu_non pris en charge_ofd | Aperçu | Commutateur de fonctionnalité | Cacher/Fermer | Page configurable |
| aperçu_non pris en charge_pdf | Aperçu | Commutateur de fonctionnalité | Cacher/Fermer | Page configurable |
| aperçu_non pris en charge_rtf | Aperçu / Texte (Aperçu non pris en charge RTF) | Commutateur de fonctionnalité | Cacher/Fermer | Page configurable |
| rdoc_suivre_mode | Édition de document / Mode de suivi | Commutateur de fonctionnalité | Activé | Page configurable |
| rdoc_Notification | Édition de document / Alertes de notification | Commutateur de fonctionnalité | Activé | Page configurable |
| rdoc_large_papier | Édition de document / Papier large | Commutateur de fonctionnalité | Activé | Page configurable |
| sdk_éditeur_à propos_marque_visible | Entrée de marque de l'éditeur | Commutateur de fonctionnalité | Activé | Configurable sur la page |
| sdk_éditeur_à propos_entrée_visible | Entrée de marque de l'éditeur | Commutateur de fonctionnalité | Activé | Configurable sur la page |
| sdk_éditeur_officiel_site web_entrée_visible | Entrée de marque de l'éditeur | Commutateur de fonctionnalité | Activé | Configurable sur la page |
| table_association_référence_ou_formule | Édition de tableau d'application / Champ - Référence associée & Formule associée | Commutateur de fonctionnalité | Caché/Éteint | Configurable sur la page |
| table_Notification | Édition de tableau d'application / Rappel de date | Commutateur de fonctionnalité | Activé | Configurable sur la page |
| table_référer_données | Édition de tableau d'application / Table de données de référence (Feuilles fusionnées) | Commutateur de fonctionnalité | Caché/Éteint | Configurable sur la page |
| téléverser_image_gif | Format d'image téléversée | Commutateur de fonctionnalité | Activé | Configurable sur la page |
| téléverser_image_jpeg | Format d'image téléversée | Commutateur de fonctionnalité | Activé | Configurable sur la page |
| téléverser_image_png | Format d'image téléversée | Commutateur de fonctionnalité | Activé | Configurable sur la page |
| téléverser_image_tiff | Format d'image téléversée | Commutateur de fonctionnalité | Activé | Configurable sur la page |
| téléverser_image_webp | Format d'image téléversée | Commutateur de fonctionnalité | Activé | Configurable sur la page |
| joindre_limite_tous_img_taille | Paramètre de pièce jointe / Taille maximale pour les images téléversées (MB) | Quota | Par défaut 512; 0–512 | Configurable sur la page |
| joindre_limite_tous_taille | Paramètre de pièce jointe / Taille maximale pour les pièces jointes téléversées (GB) | Quota | Par défaut 2048; 0–2048 | Configurable sur la page |
| modifier_limite_formulaire_taille | Modifier le paramètre / Taille maximale des données modifiables (MB) | Quota | Par défaut 100 ; 0–100 | Configurable sur la page |
| modifier_limite_formulaire_soumettre | Modifier le paramètre / Nombre maximum de soumissions par formulaire | Quota | Par défaut 50000 ; 0–50000 | Configurable sur la page |
| modifier_limite_modoc_taille | Modifier le paramètre / Taille maximale des données modifiables (MB) | Quota | Par défaut 100 ; 0–100 | Configurable sur la page |
| modifier_limite_mosheet_calcul_cellules | Modifier le paramètre / Formule - Référence inter-feuilles - Nombre maximum de cellules référencées | Quota | Par défaut 1500000 ; 0–1500000 ; Non vérifié | Configurable sur la page |
| modifier_limite_mosheet_calcul_complexité | Modifier le paramètre / Formule - Référence inter-feuilles - Complexité des formules référencées | Quota | Par défaut 6000000 ; 0–6000000 ; Non vérifié | Configurable sur la page |
| modifier_limite_mosheet_fonction_référence | Modifier les paramètres / Formule - Nombre maximum de fonctions de référence inter-feuilles pouvant être saisies (unités) | Quota | Par défaut 4000 ; 0–4000 | Paramétrable sur la page |
| modifier_limite_mosheet_feuille_cellule | Modifier les paramètres / Nombre maximum de cellules dans une seule feuille de calcul | Quota | Par défaut 0 ; 0–0 ; Non validé | Paramétrable sur la page |
| modifier_limite_mosheet_feuille_fc | Modifier les paramètres / Nombre maximum de formules pouvant être saisies dans une seule feuille de calcul | Quota | Par défaut 0 ; 0–0 ; Non validé | Paramétrable sur la page |
| modifier_limite_mosheet_taille | Modifier les paramètres / Volume maximum de données modifiables (MB) | Quota | Par défaut 100 ; 0–100 | Paramétrable sur la page |
| modifier_limite_mosheet_vue | Modifier les paramètres / Nombre maximum de vues distinctes qu'un utilisateur peut créer dans une seule feuille de calcul (unités) | Quota | Par défaut 100 ; 0–100 | Paramétrable sur la page |
| modifier_limite_Présentation_page | Modifier les paramètres / Nombre de diapositives | Quota | Par défaut 2000 ; 0–2000 | Paramétrable sur la page |
| modifier_limite_Présentation_taille | Modifier les paramètres / Volume maximum de données modifiables (MB) | Quota | Par défaut 100 ; 0–100 | Paramétrable sur la page |
| modifier_limite_rdoc_taille | Modifier les paramètres / Volume maximum de données modifiables (MB) | Quota | Par défaut 100 ; 0–100 | Paramétrable sur la page |
| modifier_limite_Table_calendrier_vue | Modifier le paramètre / Nombre maximum de vues calendrier par fichier unique | Quota | Par défaut 200 ; 0–200 | Paramétrable sur la page |
| modifier_limite_Table_compter | Modifier le paramètre / Nombre maximum de tableaux de données | Quota | Par défaut 200 ; 0–200 | Paramétrable sur la page |
| modifier_limite_Table_gantt_vue | Modifier le paramètre / Nombre maximum de vues Gantt par fichier unique | Quota | Par défaut 200 ; 0–200 | Paramétrable sur la page |
| modifier_limite_Table_verrouiller_vue | Modifier le paramètre / Nombre maximum de vues verrouillées par tableau de données unique | Quota | Par défaut 50 ; 0–50 | Paramétrable sur la page |
| modifier_limite_Table_manuel_version | Modifier le paramètre / Nombre de versions enregistrées manuellement | Quota | Par défaut 10000 ; 0–10000 | Paramétrable sur la page |
| modifier_limite_Table_fusion_Table_référence | Modifier le paramètre / Nombre maximum de tableaux de données qu'une seule feuille de calcul fusionnée peut référencer | Quota | Par défaut 20 ; 0–20 | Paramétrable sur la page |
| modifier_limite_Table_fusion_Table_résumé | Modifier le paramètre / Nombre maximum de feuilles de calcul fusionnées | Quota | Par défaut 20 ; 0–20 | Paramétrable sur la page |
| modifier_limite_Table_personnel_vue | Modifier le paramètre / Nombre maximum de vues personnelles par tableau de données unique | Quota | Par défaut 50 ; 0–50 | Paramétrable sur la page |
| modifier_limite_Table_unique_colonne | Modifier le paramètre / Colonnes totales d'un tableau de données unique | Quota | Par défaut 50 ; 0–50 | Configurable sur la page |
| modifier_limite_Table_unique_ligne | Modifier le paramètre / Lignes totales d'un tableau de données unique | Quota | Par défaut 20000 ; 0–20000 | Configurable sur la page |
| modifier_limite_Table_unique_vue | Modifier le paramètre / Nombre maximum de vues d'un tableau de données unique | Quota | Par défaut 200 ; 0–200 | Configurable sur la page |
| modifier_limite_Table_taille | Modifier le paramètre | Quota | Par défaut 100 ; 0–100 | Configurable sur la page |
| exporter_limite_rdoc_pixel_hauteur | Paramètre d'exportation / Hauteur maximale de l'image exportée (px) | Quota | Par défaut 66000 ; 0–66000 | Configurable sur la page |
| exporter_taille_limite | Paramètre d'exportation / Taille maximale du fichier exporté (GB) | Quota | Par défaut 3072 ; 0–3072 | Configurable sur la page |
| historique_limite_tous_temps | Paramètre d'historique / Nombre de jours de conservation de l'historique des fichiers | Quota | Par défaut 10000000000000000 ; 0–10000000000000000 ; Non validé | Configurable sur la page |
| historique_limite_mosheet_cellule_temps | Paramètre d'historique / Nombre de jours de conservation de l'historique des cellules du tableau | Quota | Par défaut 10000000000000000 ; 0–10000000000000000 ; Non validé | Configurable sur la page |
| historique_limite_revenir_num | Paramètre d'historique / Nombre d'enregistrements historiques récents pouvant être restaurés pour un fichier unique | Quota | Par défaut 2000 ; 0–2000 | Paramétrable sur la page |
| historique_limite_Table_cellule_temps | Paramètre d'historique / Nombre de jours de conservation de l'historique pour les cellules des tableaux d'application | Quota | Par défaut 10000000000000000 ; 0–10000000000000000 ; non validé | Paramétrable sur la page |
| historique_limite_Table_ligne_temps | Paramètre d'historique / Nombre de jours de conservation de l'historique dynamique pour les lignes des tableaux d'application | Quota | Par défaut 10000000000000000 ; 0–10000000000000000 ; non validé | Paramétrable sur la page |
| historique_limite_version_num | Paramètre d'historique / Nombre de versions (instantanés) pouvant être sauvegardées/restaurées pour un fichier unique | Quota | Par défaut 100 ; 0–100 | Paramétrable sur la page |
| Importer_exportation_timeout | Paramètre d'importation / Temps d'importation maximal (min) | Quota | Par défaut 10 ; 0–10 | Paramétrable sur la page |
| Importer_limite_modoc_taille | Paramètre d'importation / Taille maximale du fichier (MB) | Quota | Par défaut 300 ; 0–300 | Paramétrable sur la page |
| Importer_limite_modoc_mot | Paramètre d'importation / Nombre maximum de caractères (Caractère) | Quota | Par défaut 2000000 ; 0–2000000 | Paramétrable sur la page |
| Importer_limite_mosheet_tous_feuille_cellule | Paramètre d'importation / Nombre maximum de cellules valides dans une feuille | Quota | Par défaut 5 000 000 ; 0–5 000 000 | Configurable sur la page |
| Importer_limite_mosheet_tous_xml_taille | Paramètre d'importation / Taille totale de tous XML Fichiers dans la feuille (MB) | Quota | Par défaut 300 ; 0–300 | Configurable sur la page |
| Importer_limite_mosheet_converti_taille | Paramètre d'importation / ShimoDocs Volume de données (MB) | Quota | Par défaut 100 ; 0–100 | Configurable sur la page |
| Importer_limite_mosheet_unique_feuille_cellule | Paramètre d'importation / Nombre maximum de cellules valides dans une seule feuille de calcul | Quota | Par défaut 2 000 000 ; 0–2 000 000 | Configurable sur la page |
| Importer_limite_mosheet_unique_xml_taille | Paramètre d'importation / Taille maximale d'un seul XML Fichier dans la feuille (MB) | Quota | Par défaut 20 ; 0–20 | Configurable sur la page |
| Importer_limite_mosheet_taille | Paramètre d'importation / Taille de fichier maximum (MB) | Quota | Par défaut 300 ; 0–300 | Configurable sur la page |
| Importer_limite_Présentation_page | Paramètre d'importation / Nombre maximum de diapositives (Pages) | Quota | Par défaut 2000 ; 0–2000 | Configurable sur la page |
| Importer_limite_Présentation_taille | Paramètre d'importation / Taille de fichier maximum (MB) | Quota | Par défaut 100 ; 0–100 | Paramétrable sur la page |
| Importer_limite_rdoc_taille | Paramètre d'importation / Taille de fichier maximum (MB) | Quota | Par défaut 50 ; 0–50 | Paramétrable sur la page |
| Importer_limite_rdoc_mot | Paramètre d'importation / Nombre maximum de caractères (Caractère) | Quota | Par défaut 300000 ; 0–300000 | Paramétrable sur la page |
| Importer_limite_Table_unique_colonne | Paramètre d'importation / Nombre maximum de colonnes effectives par feuille de calcul (Colonnes) | Quota | Par défaut 50 ; 0–50 | Paramétrable sur la page |
| Importer_limite_Table_unique_ligne | Paramètre d'importation / Nombre maximum de lignes effectives par feuille de calcul (Lignes) | Quota | Par défaut 20000 ; 0–20000 | Paramétrable sur la page |
| coller_limite | Paramètre de collage / Volume de données maximum par collage (MB) | Quota | Par défaut 9 ; 0–9 | Paramétrable sur la page |
| coller_limite_modoc | Paramètre de collage / Nombre maximum de caractères par collage (Caractère) | Quota | Par défaut 200000 ; 0–200000 | Paramétrable sur la page |
| coller_limite_mosheet | Paramètre de collage / Nombre maximum de cellules par collage (Unités) | Quota | Par défaut 2000000 ; 0–2000000 | Paramétrable sur la page |
| coller_limite_présentation | Paramètres de collage / Nombre maximum de diapositives pouvant être collées en une seule fois | Quota | Par défaut 200 ; 0–200 | Configurable sur la page |
| coller_limite_rdoc | Paramètres de collage / Nombre maximum de caractères pouvant être collés en une seule fois | Quota | Par défaut 200000 ; 0–200000 | Configurable sur la page |
| coller_limite_table | Paramètres de collage / Nombre maximum de lignes pouvant être collées en une seule fois | Quota | Par défaut 2000 ; 0–2000 | Configurable sur la page |
| aperçu_timeout | Paramètres d'aperçu / Temps d'aperçu maximum (min) | Quota | Par défaut 10 ; 0–10 | Configurable sur la page |

## Annexe B : Terminologie et correspondance des champs de page

| **Terme** | **Signification** |
| --- | --- |
| Nom de l'élément de configuration / Liste blanche des noms | Le nom unique de l'élément de configuration, par exemple, rdoc_notification, édition_limite_mosheet_taille. |
| ID d'Équipe | Identifiant de l'équipe ; saisissez un entier positif pour accéder à la portée de configuration au niveau de l'équipe. |
| Par défaut de l'application | La portée de configuration lorsque l'ID de l'équipe est laissé vide ; appelée dans ce manuel configuration globale. |
| Configuration au niveau de l'équipe | Remplacer les configurations effectives uniquement pour l'ID d'équipe spécifié. |
| Par défaut du système | Lorsqu'il n'y a pas de surcharge à ce niveau, la valeur par défaut intégrée du produit est utilisée. |
| Couverture de l'application / Couverture de l'équipe | Des configurations personnalisées existent au niveau actuel, prenant le pas sur les valeurs du niveau supérieur. |
| Commutateur de fonctionnalité | Paramètre de type interrupteur ou état. |
| Quota à valeur unique | Une valeur maximale et un commutateur de validation de limite optionnel. |
| Quota de plage | Un paramètre de plage qui inclut à la fois des valeurs minimum et maximum. |
| JSON Configuration | Paramètre structuré qui doit rester valide JSON; certains éléments de configuration ne sont pas affichés sur la page actuelle. |
| Pas de validation / Pas de limite | Ne réalise pas la validation de la limite selon le maximum saisi. |
