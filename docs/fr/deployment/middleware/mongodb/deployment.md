# Déployer avec MongoDB

[← ShimoDocs Suite documentation de déploiement](../../README.md)

Cet article présente comment désactiver le MongoDB intégré dans le ShimoDocs et configurer celui du client MongoDB en tant que base de données de documents tierce. MongoDB Après la configuration, l'installateur vérifiera MongoDB la connectivité réseau et les permissions telles que la connexion et l'authentification. Une fois les vérifications réussies, le déploiement peut continuer. 

# 1. Préparatifs avant la configuration 
Avant de commencer, veuillez confirmer : 
- MongoDB Le serveur est installé et fonctionne normalement. 
- Le nœud de déploiement peut accéder à l'hôte et au port de la MongoDB Serveur. 
- Informations de connexion et PASSWORD pour l'authentification avec le MongoDB Serveur sont préparés. 

> [!TIP] 
> 
> L'IP, le port et le compte dans cet article sont des exemples. Veuillez utiliser les informations réelles de l'environnement pour la configuration et ne pas enregistrer le véritable PASSWORD dans des documents publics ou des captures d'écran. 
> 

# 2. Entrer dans la Configuration avancée 
Dans l'étape "Configuration" de l'installateur, après avoir terminé la configuration du réseau, de l'environnement cible et des informations sur le nœud, développez la "Configuration avancée" en bas de la page. 

# 3. Annuler l'installation de l'intégration MongoDB
Dans la zone 'Services Middleware', décochez MongoDB

Après avoir décoché, l'installateur n'installera plus le intégré MongoDB, et utiliser à la place un MongoDB préparé à l'extérieur. Pour les autres middleware, le choix d'utiliser les services intégrés doit se faire en fonction du plan de déploiement réel.

# 4. Ouvrir la Configuration du Middleware Tiers
Dans la zone "Middleware tiers", cliquez sur "Configurer".

# 5. Configurer MongoDB Base de données de documents
1. Sélectionnez "MongoDB Base de données de documents" sur la gauche.
2. Activez "Utiliser la messagerie tierce MongoDB Base de données de documents.
3. Entrez l'hôte, le port, USERNAME, PASSWORD, Remplacement de la chaîne de connexion
4. Vérifiez et enregistrez

> [!WARNING]
>
> Attention : Si un tiers MongoDB crée un compte dédié pour l'application et suit le « principe du moindre privilège », où un compte n'a que les autorisations pour accéder à une base de données spécifiée, il est nécessaire d'assigner un utilisateur et PASSWORD pour chaque base de données professionnelle

# 6. Confirmer les résultats de la vérification
L'installateur vérifiera les éléments suivants :
- connexion : Le compte peut être authentifié normalement
- connectivité : l'environnement de déploiement peut accéder MongoDB
- permission : Le compte a des permissions pour la connexion, l'authentification, l'exécution de commandes, etc.

Après que tous les éléments de vérification affichent 'Succès', fermez la fenêtre de configuration et retournez à la page 'Configuration' de l'installateur.

S'il y a des échecs, veuillez vérifier selon les indications sur la page :
- Si l'hôte et le port sont correctement remplis.
- Si le réseau entre le nœud de déploiement et le MongoDB Le serveur est connecté.
- Si USERNAME et PASSWORD sont corrects.
- Si le compte dispose des permissions requises (connexion et authentification, permissions de commande, etc.).

# 7. Continuer à initialiser le déploiement
Après être revenu à la page 'Configuration', assurez-vous MongoDB reste non vérifié, cliquez alors sur 'Initialiser le déploiement' pour continuer à compléter l'aperçu du déploiement, les vérifications et les étapes d'exécution.

> [!TIP]
>
> Avant d'initialiser le déploiement, veuillez confirmer une fois de plus que la MongoDB la configuration a été enregistrée et tous les éléments de validation ont été dépassés.
