# Déployer avec Kafka

[← ShimoDocs Suite documentation de déploiement](../../README.md)

Cet article explique comment désactiver l'installateur intégré Kafka intégré dans le ShimoDocs et configurer celui du client Kafka comme une file de messages tierce. Après la configuration, l'installateur vérifiera la connectivité réseau de Kafkaet la permission de créer des sujets. Une fois vérifié, le déploiement peut commencer. 

# 1. Préparatifs avant la configuration 
Avant de commencer, veuillez confirmer : 
- Kafka Le serveur est installé et fonctionne normalement. 
- Le nœud de déploiement peut accéder à l'hôte Kafka et au port du serveur. 
- Informations utilisateur d'authentification préparées et PASSWORD pour se connecter à Kafka Sujet du serveur (si le cluster externe Kafka a activé l'authentification sécurisée). 
- Le compte authentifié doit utiliser un utilisateur administrateur et avoir l'autorisation de créer, supprimer, autoriser, et lire et écrire des sujets (si le externe Kafka a activé l'authentification sécurisée). 

> [!TIP]
>
> L'IP, le port et le compte dans cet article sont des exemples. Veuillez configurer en utilisant les informations de votre environnement réel ; ne pas enregistrer le vrai PASSWORD dans des documents externes ou des captures d'écran. 
>

# 2. Entrer dans la Configuration avancée 
Dans l'étape "Configuration" de l'installateur, après avoir terminé la configuration du réseau, de l'environnement cible et des informations sur le nœud, développez la section "Configuration avancée" en bas de la page. 

# 3. Annuler l'installation du intégré Kafka
Dans la zone 'Services Middleware', décochez Kafka.

Après avoir décoché, l'installateur n'installera plus le intégré Kafka, et un externe Kafka qui a été préparé sera utilisé plus tard. Pour les autres middleware, le choix d'utiliser des services intégrés doit être fait en fonction du plan de déploiement réel.

# 4. Ouvrir la Configuration du Middleware Tiers
Dans la zone "Middleware tiers", cliquez sur "Configurer".

# 5. Configurer Kafka Middleware de messagerie
## Kafka Serveur SASL Authentification non activée
1. Sélectionnez "Kafka File de messages" à gauche.
2. Activez "Utiliser la messagerie tierce Kafka File de messages".
3. Remplissez les Kafka informations de connexion du serveur.
5. Vérifiez et enregistrez

## Activation SASL de l'authentification sur Kafka Serveur 
Si Kafka Le serveur a SASL l'authentification activée, elle doit être activée simultanément dans l'interface Web : Activer uniquement lorsque le courtier nécessite un accès authentifié Bouton 
1. Activer SASL l'authentification 
2. Vérifier le mécanisme 
3. Entrer USERNAME et PASSWORD 
4. Vérifier et enregistrer 

# 6. Confirmer les résultats de la vérification
L'installateur vérifiera les éléments suivants :
- connexion : Le compte peut être authentifié normalement (si SASL est activé).
- connectivité : l'environnement de déploiement peut accéder Kafka.
- permission de créer un Topic : le compte dispose des permissions pour créer des Topics, autoriser et lire/écrire.

Après que tous les éléments de vérification affichent 'Succès', fermez la fenêtre de configuration et retournez à la page 'Configuration' de l'installateur.

S'il y a des échecs, veuillez vérifier selon les indications sur la page :
- Si l'hôte et le port sont correctement remplis.
- Si le réseau entre le nœud de déploiement et le Kafka serveur est connecté.
- Si USERNAME et PASSWORD sont corrects (Kafka Le serveur a activé SASL l'authentification).
- Si le compte possède les permissions requises (Kafka Le serveur a SASL authentification activée).

# 7. Continuer à initialiser le déploiement
Après être revenu à la page 'Configuration', assurez-vous Kafka reste non vérifié, puis cliquez sur 'Initialiser le déploiement' pour continuer avec la vue d'ensemble du déploiement, les étapes de vérification et d'exécution.

> [!TIP]
>
> Avant d'initialiser le déploiement, veuillez confirmer une fois de plus que la Kafka configuration a été enregistrée et que tous les éléments de vérification ont été validés.
