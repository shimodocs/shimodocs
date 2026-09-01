# Configuration du système

[← ShimoDocs Suite documentation de déploiement](../../../README.md)

## 1. Instructions

Ce manuel présente la fonction « Configuration du système » de ShimoDocs Suite, adaptée aux administrateurs système et aux implémenteurs utilisant cette fonction pour la première fois. Vous pouvez suivre les étapes de ce document pour trouver des éléments de configuration, modifier les configurations, vérifier si les changements prennent effet et restaurer les paramètres d'origine si nécessaire.

> [!TIP]
>
> Si vous n'êtes pas sûr de la signification ou de l'impact d'un élément de configuration, veuillez contacter ShimoDocs support technique pour confirmation avant d'apporter des modifications.

**Règle de portée la plus importante** Lorsque l'identifiant de l'entreprise est laissé vide, la requête et la modification s'appliquent à la configuration globale ; lorsque qu'un identifiant d'entreprise est sélectionné, la requête et la modification s'appliquent à la configuration de l'entreprise sélectionnée. La modification de la configuration globale peut affecter plusieurs entreprises, veuillez donc reconfirmer le périmètre de configuration avant de sauvegarder.

### 1.1 Point d'accès

Backend Admin > ShimoDocs Suite > Gestion de la configuration > Configuration du système

### 1.2 Préparation avant utilisation

- Confirmez que le compte de connexion dispose des autorisations de visualisation et de modification pour la ShimoDocs Suite configuration du système.
- Confirmez d'abord si la portée cible est globale ou pour une entreprise spécifique, et obtenez l'ID exact de l'entreprise.
- Confirmez les noms des clés de configuration à partir des exigences de configuration ou de l'Annexe A. Le nom de la clé de configuration est un identifiant unique ; veuillez ne pas deviner uniquement à partir du nom chinois.
- Enregistrez la source, le statut et la valeur effective avant la modification ; pour les configurations importantes, préparez également les valeurs de restauration.
- Les configurations globales ayant un impact important doivent être modifiées pendant les périodes de faible activité, et le personnel concerné doit être informé à l'avance.

## 2. Portée et priorité de la configuration

La configuration du système prend en charge à la fois le niveau global et le niveau entreprise. Avant toute modification, le champ ID de l'entreprise et l'invite de portée en bas de la page doivent être vérifiés.

| **Domaine fonctionnel** | **Portée Globale** | **Portée Entreprise** | **Signal d'identification de la page** |
| --- | --- | --- | --- |
| Configuration du Système | Laisser l'ID de l'entreprise vide | Sélectionner l'ID de l'entreprise | Invite en bas « Substitution de version globale » ou « Résultat final effectif de l'entreprise » |

*Figure 1  Emplacement pour sélectionner l'ID de l'entreprise dans la configuration du système*

### 2.1 Configuration globale

- Laisser l'ID de l'entreprise non sélectionné.
- La page indique que la requête et la modification en cours concernent le « Remplacement de version globale ».
- Les valeurs globales sont les valeurs de base utilisées lorsqu'aucun remplacement d'entreprise n'est défini ; les modifier peut affecter plusieurs entreprises.
- Avant de sauvegarder, vérifiez au moins une fois de plus pour vous assurer que le champ ID de l'entreprise est bien vide.

### 2.2 Configuration au niveau de l'entreprise

- Sélectionnez l'entreprise cible dans le menu déroulant ID de l'entreprise.
- La page affiche le résultat final effectif après la fusion des valeurs par défaut avec la configuration personnalisée de l'entreprise actuelle.
- La configuration au niveau de l'entreprise n'affecte que l'entreprise sélectionnée et ne modifie pas directement la configuration des autres entreprises.
- Lorsque qu'une configuration au niveau de l'entreprise existe pour le même élément, la valeur finale effective de l'entreprise prévaut sur la valeur globale.

### 2.3 Remplacement, héritage et restauration

- Lorsque l'entreprise actuelle n'a pas de remplacement, la configuration globale ou les valeurs par défaut du système sont utilisées.
- Les opérations telles que « Restaurer la valeur par défaut du système » ou la suppression du remplacement actuel signifient généralement la suppression du remplacement de la portée actuelle et la ré-héritage de la valeur de niveau supérieur.
- Les invites sur la page telles que « Valeur par défaut du système », « Remplacement de version globale » et « Résultat final effectif de l'entreprise » peuvent être utilisées pour déterminer de quelle couche provient la valeur actuelle.
- Avant d'effectuer une restauration ou une suppression, enregistrez la valeur actuelle et confirmez que le résultat hérité répond aux attentes.

**Avertissement de risque** Ne pas enregistrer directement sans confirmer le périmètre de l'entreprise. Si l'ID de l'entreprise est vide, l'opération peut écrire une substitution globale et affecter plusieurs entreprises.

## 3. Configuration du système

La configuration du système est utilisée pour visualiser et ajuster les fonctions générales, les quotas et les paramètres de fonctionnement de ShimoDocs Suite.

### 3.1 Méthode de recherche Un : Recherche exacte

- ID de l'entreprise : Laisser vide pour global ; sélectionner un ID d'entreprise pour une configuration au niveau de l'entreprise.
- Critères de recherche : sélectionner le type, le type de valeur et la fin effective selon les besoins ; conserver "Tous" si vous n'êtes pas sûr.
- Nom de clé : entrer le nom de la clé de configuration ; une clé par ligne ou utiliser des virgules pour séparer plusieurs clés.
- Cliquez sur "Rechercher" pour confirmer le nom, la clé, la source, le statut et la valeur actuelle dans les résultats.
- Cliquez sur « Modifier » à l'extrémité droite de la ligne cible pour ouvrir la fenêtre contextuelle de modification.

*Figure 2  Zone de recherche précise de la configuration système*

*Figure 3  Résultat unique après recherche précise par nom de clé de configuration*

**Indice de champ** Lorsque « Lorsque aucune entreprise n'est sélectionnée, la requête et la modification actuelles concernent la version globale de remplacement » apparaît en bas de la page, cela indique que la portée actuelle est globale. Après avoir sélectionné une entreprise, la page affiche le résultat final effectif combinant les valeurs par défaut avec la configuration personnalisée de l'entreprise actuelle.

### 3.2 Deuxième méthode de recherche : localiser directement dans la liste

- Maintenez la portée de l'entreprise et les conditions de filtrage correctes, et utilisez le défilement de la page pour parcourir la liste.
- Confirmez la configuration cible par "Nom" ou "Nom de la clé", ne vous fiez pas uniquement à la valeur actuelle.
- Affichez le type, la fin effective, la valeur actuelle, la source et le statut dans la même ligne.
- Cliquez sur "Modifier" à l'extrême droite. Si vous ne voyez pas la colonne d'opération, faites défiler le tableau horizontalement vers la droite ou agrandissez la fenêtre du navigateur.

### 3.3 Modification de différents types de configurations système

#### 3.3.1 Type clé-valeur

La partie supérieure de la fenêtre contextuelle de modification affiche des métadonnées en lecture seule, y compris le nom de la clé, le nom, la description, le type et la fin effective. Lorsqu'il est activé, remplissez les valeurs dans les cases de saisie telles que "Valeur de chaîne" et enregistrez. Si la chaîne contient JSON, URL, chemin ou liste, le format original doit être maintenu.

*Figure 4  Fenêtre contextuelle d'édition de type clé-valeur de la configuration système*

#### 3.3.2 Type quota

La fenêtre contextuelle de quota inclut généralement Statut, Valeur minimale, Valeur maximale et un interrupteur "Pas de validation". Après activation de la configuration, remplissez la plage selon les exigences métier ; activer "Pas de validation" signifie que le système n'effectue pas de contrôles de restriction selon la plage saisie. Les valeurs doivent être cohérentes avec les unités de la fenêtre contextuelle, telles que "pièces", "Mo", etc.

*Figure 5 Fenêtre contextuelle d'édition de type quota de la configuration système*

#### 3.3.3 Types de fonction

Le type de fonction est principalement basé sur un basculement de statut. L'activer indique que l'élément de configuration est activé dans la portée actuelle ; le désactiver indique qu'il est désactivé ou non activé. Certaines clés ont une sémantique inverse et doivent être déterminées en fonction du nom et de la description de l'élément de configuration. Par exemple, les clés dont le nom contient 'unsupport' ou 'disable' peuvent représenter 'non supporté' ou 'désactivé' lorsqu'elles sont activées.

### 3.4 Enregistrer, Supprimer et Restaurer

- Avant de sauvegarder, reconfirmez le périmètre de l'entreprise, le nom clé, le type, l'unité et les valeurs modifiées.
- Après la sauvegarde, recherchez à nouveau le même élément de configuration pour confirmer que la source, le statut et la valeur effective ont changé.
- Lorsqu'il y a une substitution dans le périmètre actuel, l'opération 'Supprimer' peut être disponible ; après avoir supprimé la substitution, elle reviendra à la valeur héritée du niveau précédent.
- Lorsqu'un retour en arrière est nécessaire, réécrivez d'abord la valeur enregistrée initialement, ou supprimez la substitution actuelle après avoir confirmé la relation d'héritage.
- Ne comprenez pas 'supprimer' comme supprimer l'élément de configuration lui-même ; la suppression sur la page s'applique généralement uniquement à l'enregistrement de substitution dans la portée actuelle.

## 4. Vérification de l'efficacité et retour en arrière

### 4.1 Vérification après enregistrement

- Dans la page de configuration du système, interrogez à nouveau la même portée d'entreprise et le même élément de configuration pour confirmer la source, le statut et la valeur effective.
- Allez à la page fonctionnelle qui utilise réellement cette configuration pour vérifier la performance de la fonction, plutôt que de simplement regarder le backend de configuration.
- Pour la configuration globale, au moins une entreprise sans configuration de niveau entreprise doit être contrôlée ; la configuration de niveau entreprise n'est vérifiée que pour l'entreprise cible.
- Pour les paramètres liés au compte, aux permissions ou au cache, actualisez la page, reconnectez-vous ou attendez que le cache se mette à jour si nécessaire.
- Enregistrer l'heure de modification, l'opérateur, la portée de l'entreprise, le nom de la clé, les valeurs avant et après la modification, et les résultats de la vérification.

### 4.2 Restauration

- Il y a une valeur originale définie : rééditer et réécrire la valeur originale.
- Il suffit de supprimer la substitution de plage actuelle : utilisez 'Restaurer les paramètres système par défaut' ou supprimez la substitution actuelle.
- Après le retour en arrière, interrogez à nouveau les valeurs source et effectives, et entrez de nouveau sur la page métier pour vérification.
- Si les changements globaux causent des anomalies généralisées, la priorité doit être donnée à la restauration de la couverture globale, puis à l'investigation des différences de couverture entre les entreprises individuelles.

**Note importante** Les suppressions sur la page s'appliquent généralement aux enregistrements de substitution dans la portée actuelle ; l'élément de configuration lui-même existe toujours. Il est nécessaire de confirmer que la valeur héritée répond aux attentes avant la suppression.

## 5. Foire Aux Questions

| **Question** | **Méthode de traitement** |
| --- | --- |
| Impossible de trouver le bouton modifier ou action | Lorsque le tableau est large, faites défiler horizontalement jusqu'à l'extrémité droite ; vous pouvez également agrandir la zone visible du navigateur. |
| Aucun résultat pour une recherche exacte | Vérifiez la casse et les traits de soulignement du nom de la clé ; confirmez la plage d'ID d'entreprise ; supprimez les filtres trop stricts de type, de type de valeur ou de fin effective. |
| Après enregistrement, la page métier n'a pas de changements | Vérifiez si le mauvais périmètre de l'entreprise a été sélectionné, si la source est en train d'être écrasée par l'entreprise, si un rafraîchissement ou une reconnexion est nécessaire, et confirmez si les éléments de configuration sont applicables à la fonction actuelle. |
| Bouton Restaurer les paramètres par défaut du système indisponible | Le périmètre actuel n'a pas de remplacements, utilise actuellement des valeurs héritées ou des valeurs par défaut du système. |
| JSON ou URL Erreur de configuration | Conserver valide JSON ou URL format, ne pas omettre les guillemets, les virgules ou les protocoles ; vérifier d'abord dans l'entreprise de test. |
| La valeur effective finale de l'entreprise diffère de la valeur globale | L'entreprise actuelle peut avoir des remplacements. Vérifiez les enregistrements source et de remplacement pour confirmer s'il faut conserver les différences de l'entreprise ou restaurer l'héritage. |

## Annexe A : Index des éléments de configuration du système

L'index suivant ne répertorie que les éléments de configuration du système pouvant être consultés et modifiés sur la page actuelle ; l'étendue visible spécifique dépend de la version de déploiement actuelle et de l'affichage réel de la page.

| **Clé de configuration** | **Nom de l'élément de configuration** | **Type/Règle** | **Support de page** |
| --- | --- | --- | --- |
| autoriser_équipe_admin_obtenir_invité_utilisateur_mot de passe | L'administrateur d'entreprise obtient le initial PASSWORD des utilisateurs invités | Chaîne vide | Paramétrable sur la page |
| automatique_connexion_activé_non_permission_page | L'accès anonyme sans permission redirige vers la page de connexion | Chaîne vide | Paramétrable sur la page |
| lot_supprimer_fichier_compte_limite | Nombre maximum de fichiers pour suppression en lot | 0–500 | Paramétrable sur la page |
| lot_télécharger_fichiers | Nombre maximum de fichiers pour téléchargement en lot unique | 0–500 | Paramétrable sur la page |
| lot_télécharger_taille | Taille totale maximale pour téléchargement en lot unique | 0–21474836480 | Paramétrable sur la page |
| lot_déplacer_fichier_compte_limite | Nombre maximum de fichiers pour déplacement en lot | 0–500 | Paramétrable sur la page |
| marque | Nom de marque front-end | Chaîne vide | Paramétrable sur la page |
| changer_dossier_collaborateur | Collaboration sur le dossier | Chaîne vide | Paramétrable sur la page |
| classification_marque_config_limite | Nombre maximum de politiques d'approbation de rétrogradation | 0–30 | Paramétrable sur la page |
| classification_marque_limite | Nombre maximum d'étiquettes de classification | 0–20 | Paramétrable sur la page |
| classification_marque_règle_limite | Nombre maximum de règles d'étiquettes de classification | 0–30 | Paramétrable sur la page |
| nuage_équipe_espace_télécharger_fichier_taille | Taille maximum d'un fichier téléchargé (Mo) | 0–3072 | Paramétrable sur la page |
| nuage_équipe_espace_téléverser_fichier_taille | Limite de taille de téléchargement de fichiers dans l'espace d'équipe | 0–300 | Paramétrable sur la page |
| jour_décompresser_fichier_compte_limite | Nombre maximum de fichiers à décompresser par jour | 0–2000 | Paramétrable sur la page |
| par défaut_avatar | Avatar par défaut URL | Chemin | Paramétrable sur la page |
| par défaut_entreprise_corbeille_quota | Quota par défaut de la corbeille de l'entreprise | 0–0 | Paramétrable sur la page |
| par défaut_espace_quota | Quota par défaut de l'espace d'équipe | 0–107374182400 | Paramétrable sur la page |
| par défaut_équipe_utilisateur_quota | Limite de capacité par défaut pour les membres de l'entreprise | 0–0 | Paramétrable sur la page |
| par défaut_utilisateur_fichier_étiquettes | Étiquettes par défaut pour les fichiers utilisateur | JSON tableau | Paramétrable sur la page |
| par défaut_utilisateur_quota | Quota d'espace personnel par défaut dans l'équipe (Mon Bureau) | 0–107374182400 | Paramétrable sur la page |
| département_compte_limite | Nombre maximum de départements pouvant être créés dans l'entreprise | 0–500 | Paramétrable sur la page |
| département_profondeur_limite | Nombre maximum de niveaux de départements imbriqués | 0–20 | Paramétrable sur la page |
| désactiver_lot_télécharger | Désactiver le téléchargement par lot | Chaîne vide | Paramétrable sur la page |
| désactiver_entreprise_corbeille | Masquer la corbeille de l'entreprise | Chaîne vide | Paramétrable sur la page |
| affichage_ip_emplacement | Afficher l'emplacement IP | Chaîne vide | Paramétrable sur la page |
| disque_éditeur_à propos_marque_visible | Afficher les informations de marque sur ShimoDocs Suite page À propos de l'éditeur | Chaîne vide | Paramétrable sur la page |
| disque_éditeur_à propos_entrée_visible | Afficher l'entrée « À propos » dans ShimoDocs Suite éditeur | Chaîne vide | Paramétrable sur la page |
| disque_éditeur_officiel_site web_entrée_visible | ShimoDocs Suite Affichage de l'entrée du site officiel de l'éditeur | Chaîne vide | Page configurable |
| activer_lien_rapport | Rapport de lien externe | Chaîne vide | Page configurable |
| activer_extérieur | Collaborateurs externes | Chaîne vide | Page configurable |
| activer_pc_système_thème | activer_pc_système_thème | Chaîne vide | Page configurable |
| activer_rdoc_md_image_exportation_options | activer_rdoc_md_image_exportation_options | Chaîne vide | Page configurable |
| activer_risques | Identification des risques | Chaîne vide | Page configurable |
| activer_partager_expirer_temps | Durée d'expiration du lien de partage | Chaîne vide | Page configurable |
| activer_partager_mot de passe | Mot de passe de partage | Chaîne vide | Page configurable |
| fichier_collaborateur_limite | Nombre maximal de collaborateurs par fichier | 0–100 | Page configurable |
| dossier_enfants_compte_limite | Nombre maximal de fichiers au même niveau | 0–2000 | Page configurable |
| gratuit_utilisateur_créer_limite | Limite du nombre de modèles que les utilisateurs gratuits peuvent créer | 0–5 | Page configurable |
| frontend_runtime_fonctionnalités | Liste des éléments de configuration du runtime frontend | JSON tableau | Paramétrable sur la page |
| Importer_utilisateur_lignes_limite | Nombre maximal d'utilisateurs à importer en une seule fois | 0–500 | Paramétrable sur la page |
| inviter_mobile_limite_expiré | Fenêtre d'expiration pour le nombre d'invitations à collaborer sur des fichiers envoyées par téléphone mobile | 0–3600 | Paramétrable sur la page |
| inviter_mobile_limite_max | Limite du nombre d'invitations à collaborer sur des fichiers par téléphone mobile | 0–20 | Paramétrable sur la page |
| est_ouvert_rôle_appliquer | Demande d'autorisation de fichier | Chaîne vide | Paramétrable sur la page |
| connexion_appareil_limite | Nombre maximal d'appareils connectés simultanément par compte | 0–0 | Paramétrable sur la page |
| max_créateur_équipes_par_compte | Nombre maximal d'entreprises pouvant être créées par compte | 0–3 | Paramétrable sur la page |
| max_dossier_profondeur | Profondeur maximale de la hiérarchie des dossiers | 0–50 | Paramétrable sur la page |
| max_rejoint_équipes_par_compte | Nombre maximal d'entreprises qu'un compte peut rejoindre | 0–100 | Paramétrable sur la page |
| max_corbeilles_liste_taille | Nombre d'enregistrements retournés par l'interface de liste de la corbeille | 0–500 | Paramétrable sur la page |
| multipart_téléverser_activer | Téléversement multipart | Chaîne numérique | Paramétrable sur la page |
| une fois_décompresser_fichier_compte_limite | Nombre maximal de fichiers par extraction | 0–500 | Paramétrable sur la page |
| seulement_propriétaire_peut_supprimer | Seul le propriétaire peut supprimer | Chaîne vide | Paramétrable sur la page |
| premium_utilisateur_créer_limite | Nombre maximal de modèles qu'un utilisateur peut créer | 0–50 | Paramétrable sur la page |
| privé_déployer_page_icône | Configuration de l'icône de la page | Chaîne vide | Paramétrable sur la page |
| public_partager | Partage public | Chaîne vide | Paramétrable sur la page |
| rag_recherche_règle | RAG règles de recherche | JSON objet | Paramétrable sur la page |
| sdkCheckpointCacheTTL | Durée du cache de configuration de l'éditeur | 0–600 | Paramétrable sur la page |
| sdk_point de contrôle_liste blanche | Liste blanche de configuration de l'éditeur | JSON objet | Paramétrable sur la page |
| recherche_ia_activer | recherche_ia_activer | Chaîne vide | Paramétrable sur la page |
| partager_mot de passe_longueur | Longueur du mot de passe de partage | 0–6 | Paramétrable sur la page |
| unique_fichier_téléverser_taille_limite | Taille maximale d'un seul fichier téléchargé (Go) | 0–1 | Paramétrable sur la page |
| unique_téléverser_fichier_compte_limite | Téléversement par lot | Chaîne vide | Configurable sur la page |
| équipe_changer | Changement d'équipe | Chaîne vide | Configurable sur la page |
| équipe_rôle_gérer | Gestion des rôles | Chaîne vide | Configurable sur la page |
| thème_couleur | Couleur du thème de l'interface | Chaîne vide | Configurable sur la page |
| thème_couleur_btn | Couleur du thème du bouton | HEX Valeur de la couleur | Configurable sur la page |
| ui_rayon_config | Configuration du rayon de bordure de l'interface | Chaîne vide | Configurable sur la page |
| téléverser_lot_max | Nombre maximum de fichiers par téléchargement | 0–500 | Configurable sur la page |

## Annexe B : Terminologie et correspondance des champs de page

| **Terme** | **Signification** |
| --- | --- |
| Clé de configuration / Nom de clé | La clé unique de l'élément de configuration, par exemple batch_télécharger_fichiers. |
| ID d'entreprise | Identifiant de l'entreprise. Le sélectionner entre dans le champ de configuration au niveau de l'entreprise. |
| Configuration globale | Le champ par défaut consulté et modifié lorsque l'ID d'entreprise est laissé vide. |
| Configuration au niveau de l'entreprise | Remplacements effectifs uniquement pour l'entreprise sélectionnée. |
| Par défaut du système | Si aucun remplacement personnalisé n'existe dans le champ actuel, la valeur par défaut intégrée est utilisée. |
| Remplacement global de version | L'élément de configuration actuel a des paramètres personnalisés au niveau global. |
| Résultat effectif de l'entreprise | Le résultat effectif réel après fusion de la valeur par défaut de l'entreprise avec le remplacement de l'entreprise. |
| Clé-Valeur | Un paramètre à valeur unique stocké sous forme de chaîne, qui peut contenir du texte, URL, chemin, ou JSON. |
| Quota | Une plage numérique qui inclut les valeurs minimales, maximales ou un interrupteur de limite. |
| Fonction | Un paramètre de type interrupteur ou état. |
| Pas de validation | Ne réalise pas de contrôles de validation basés sur la limite supérieure spécifiée. |
