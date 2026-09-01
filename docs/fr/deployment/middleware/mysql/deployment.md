# Déployer avec MySQL 8

[← ShimoDocs Suite documentation de déploiement](../../README.md)

Cet article explique comment désactiver l'installateur intégré MySQL intégré dans le ShimoDocs installez et configurez le vôtre MySQL comme une base de données relationnelle tierce. Après la configuration, l'installateur vérifiera la connexion à la base de données, la connectivité réseau et les permissions de création de tables. Une fois vérifié, le déploiement peut se poursuivre. 

# 1. Préparation avant la configuration 
Avant de commencer, veuillez confirmer : 
- MySQL 8.0 est installé et fonctionne normalement. 
- Le nœud de déploiement peut accéder à l'hôte MySQL hôte et port de la base de données. 
- Hôte de la base de données, port, USERNAME, et PASSWORD sont préparés. 
- Le compte de base de données a la permission de se connecter, d'établir une connexion, de créer des tables et de supprimer des tables. 

> [!TIP]
>
> L'IP, le port et le compte dans cet article sont des exemples. Veuillez configurer en utilisant les informations de votre environnement réel ; ne pas enregistrer le vrai PASSWORD dans des documents externes ou des captures d'écran. 
>

# 2. Entrer dans la Configuration avancée 
Dans l'étape "Configuration" de l'installateur, après avoir terminé la configuration du réseau, de l'environnement cible et des informations sur le nœud, développez la section "Configuration avancée" en bas de la page. 

# 3. Annuler l'installation de l'intégration MySQL
Dans la zone 'Services Middleware', décochez MySQL.

Après avoir décoché, l'installateur n'installera plus le intégré MySQL, et un externe MySQL Un 8.0 déjà préparé sera utilisé ultérieurement. Le choix de savoir si les autres middlewares utilisent des services intégrés doit être fait en fonction du plan de déploiement réel.

# 4. Ouvrir la Configuration du Middleware Tiers
Dans la section « Middleware tiers », cliquez sur « Configurer ».

# 5. Configurer MySQL Base de données
1. Sélectionnez 'RDB Base de données relationnelle' à gauche.
2. Activez « Utiliser une base de données relationnelle tierce ».
3. Sélectionnez Standard MySQL sous « Dialecte ».
4. Remplissez les informations de connexion à la base de données.
5. Vérifiez et enregistrez.

# 6. Confirmer les résultats de la vérification
L'installateur vérifiera les éléments suivants :

- Connexion : si le compte de base de données peut se connecter normalement.
- Connectivité : si l'environnement de déploiement peut accéder à la base de données.
- Permission de créer une table : si le compte de base de données a la permission de créer et de supprimer des tables.

Après que tous les éléments de vérification affichent 'Succès', fermez la fenêtre de configuration et retournez à la page 'Configuration' de l'installateur.

S'il y a des échecs, veuillez vérifier selon les indications sur la page :
- Si l'hôte et le port sont correctement remplis.
- Si le réseau entre les nœuds de déploiement et la base de données est connecté.
- Si USERNAME et PASSWORD sont corrects.
- Si le compte de la base de données dispose des autorisations requises.

# 7. Continuer à initialiser le déploiement
Après être revenu à la page 'Configuration', assurez-vous MySQL reste non vérifié, puis cliquez sur « Initialiser le déploiement » pour continuer à compléter l'aperçu du déploiement, les étapes de vérification et d'exécution.

> [!TIP]
>
> Avant d'initialiser le déploiement, veuillez confirmer une fois de plus que la MySQL configuration 8.0 a été enregistrée et que tous les éléments de vérification ont été validés.
