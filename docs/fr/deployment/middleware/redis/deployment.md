# Déployer avec Redis

[← ShimoDocs Suite documentation de déploiement](../../README.md)

Cet article explique comment désactiver le Redis intégré dans le ShimoDocs et configurer celui du client Redis en tant que base de données de cache tierce. Après la configuration, l'installateur vérifiera Redis la connectivité réseau, la connexion, l'authentification, l'exécution des commandes, les autorisations de publication/abonnement, etc. Une fois les vérifications réussies, le déploiement peut continuer. 

# 1. Préparation avant la configuration 
Avant de commencer, veuillez confirmer : 
- Le Redis Le serveur est installé et fonctionne normalement. 
- Les nœuds de déploiement peuvent accéder au Redis hôte et port du serveur. 
- Informations utilisateur pour l'authentification et PASSWORD pour se connecter au Redis Serveur sont préparés. 

> [!TIP] 
> 
> L'IP, le port et le compte dans cet article sont des exemples. Veuillez utiliser les informations réelles de votre environnement pour la configuration et ne pas enregistrer de MOTS DE PASSE réels dans des documents publics ou des captures d'écran. 
> 

# 2. Entrer dans la Configuration avancée 
Dans l'étape "Configuration" de l'installateur, après avoir terminé la configuration du réseau, de l'environnement cible et des informations sur les nœuds, développez la "Configuration avancée" en bas de la page. 

# 3. Annuler l'installation intégrée Redis
Dans la section 'Services Middleware', décochez Redis

Après avoir décoché, l'installateur n'installera plus le intégré Redis, et utilisera l' Redis préparé externe à la place. Le choix d'utiliser ou non les services intégrés pour les autres middleware doit être fait selon le plan de déploiement réel.

# 4. Ouvrir la Configuration du Middleware Tiers
Dans la zone 'Middleware Tiers', cliquez sur 'Configurer'.

# 5. Configurer Redis Middleware Cache
## Redis Serveur Nœud Unique
1. Sélectionnez "Redis Cache" sur la gauche.
2. Activez "Utiliser la messagerie tierce Redis".
3. Cliquez sur "Nœud Unique"
4. Entrez l'hôte, le port, PASSWORD
5. Vérifiez et enregistrez

## Redis Serveur Cluster Sentinel
1. Sur le côté gauche, sélectionnez 'Redis Cache'.
2. Activez 'Utiliser un tiers Redis'.
3. Cliquez sur 'Cluster Sentinel'.
4. Entrez 'Nom du Master, SENTINEL PASSWORD, SENTINEL Nœuds'.
5. Vérifiez et enregistrez

# 6. Confirmer les résultats de la vérification
L'installateur vérifiera les éléments suivants :
- connexion : Le compte peut être authentifié normalement
- connectivité : l'environnement de déploiement peut accéder Redis
- autorisation : Le compte dispose des permissions pour se connecter, s'authentifier, exécuter des commandes, publier/s'abonner, etc.

Après que tous les éléments de vérification affichent 'Succès', fermez la fenêtre de configuration et retournez à la page 'Configuration' de l'installateur.

S'il y a des échecs, veuillez vérifier selon les indications sur la page :
- Si l'hôte et le port sont correctement remplis.
- Si le réseau entre le nœud de déploiement et le Redis serveur est connecté.
- Si USERNAME et PASSWORD sont corrects.
- Si le compte dispose des permissions requises (connexion et authentification, permissions de commandes, permissions de publication/abonnement, etc.).

# 7. Continuer à initialiser le déploiement
Après être revenu à la page 'Configuration', assurez-vous Redis reste non vérifié, puis cliquez sur 'Initialiser le déploiement' pour continuer avec la vue d'ensemble du déploiement, les étapes de vérification et d'exécution.

> [!TIP]
>
> Avant d'initialiser le déploiement, veuillez confirmer une fois de plus que la Redis La configuration a été enregistrée et tous les éléments de vérification ont été réussis.
