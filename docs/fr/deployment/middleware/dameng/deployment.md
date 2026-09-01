# Déployer avec Dameng V8

[← ShimoDocs Suite documentation de déploiement](../../README.md)

Cet article explique comment désactiver le MySQL intégré dans le ShimoDocs programme d'installation et configurer Dameng DM8 comme une base de données relationnelle tierce. Après que la configuration est terminée, l'installateur vérifiera la connexion à la base de données, la connectivité réseau, et les permissions de création de tables. Une fois les vérifications passées, le déploiement peut continuer.

## 1. Préparations avant la configuration

Avant de commencer, veuillez confirmer :

- Dameng DM8 est installé et fonctionne normalement.
- Le nœud de déploiement peut accéder à l'hôte et au port de la Dameng Base de données.
- L'hôte de la base de données, le port, USERNAME, et PASSWORD sont prêts.
- Le compte de la base de données dispose des permissions pour se connecter, créer des tables et supprimer des tables.
- Dameng La base de données a terminé la MySQL configuration du mode de compatibilité selon les besoins. Pour des instructions détaillées, veuillez consulter ["Dameng le Guide de Configuration de l'Intégration de la Base de Données](requirements.md).

> [!TIP]
>
> L'IP, le port et le compte mentionnés dans cet article ne sont que des exemples. Veuillez utiliser les informations réelles de l'environnement pour la configuration, et ne pas enregistrer le PASSWORD réel dans des documents externes ou des captures d'écran.

## 2. Entrer dans les Paramètres Avancés

Dans l'étape 'Configuration' de l'installateur, après avoir complété la configuration du réseau, de l'environnement cible et des informations sur le nœud, développez 'Configuration Avancée' en bas de la page.

## 3. Désélectionner l'installation intégrée MySQL

Dans la section "Services Middleware", désélectionnez **MySQL**.

Après désélection, l'installateur n'installera plus le MySQLintégré, et il utilisera la Dameng base de données préparée à l'avenir. Pour les autres middlewares, l'utilisation des services intégrés doit être choisie selon le plan de déploiement réel.

## 4. Ouvrir la Configuration du Middleware Tiers

Dans la zone 'Middleware Tiers', cliquez sur 'Configurer'.

## 5. Configurer Dameng Base de données

1. Sélectionnez "RDB Base de données relationnelle" à gauche.
2. Activez "Utiliser la base de données relationnelle Tierce".
3. Dans la section "Dialecte", sélectionnez **DM (Dameng)**.
4. Remplissez les informations de connexion à la base de données.

| Élément de configuration | Description |
| --- | --- |
| Hôte | L'adresse IP ou le nom d'hôte accessible de la Dameng Base de données |
| Port | Le port d'écoute de la Dameng Base de données, généralement 5236 par défaut, sous réserve de la configuration réelle |
| USERNAME | Le compte utilisé pour se connecter à la base de données |
| PASSWORD | Le PASSWORD correspondant au compte de base de données |
| DSN | Généré automatiquement par l'installateur en fonction des informations ci-dessus, aucune saisie manuelle requise |

5. Après avoir confirmé que les informations sont correctes, cliquez sur "Valider et enregistrer."

## 6. Confirmer les résultats de la vérification

L'installateur vérifiera les éléments suivants :

- **connexion**: Le compte de base de données peut se connecter normalement.
- **connectivité**: L'environnement de déploiement peut accéder à la base de données.
- **permission de création de table**: Le compte de base de données a l'autorisation de créer et de supprimer des tables.

Après que toutes les vérifications indiquent "Succès", fermez la fenêtre de configuration et revenez à la page "Configuration" de l'installateur.

S'il y a des éléments échoués, veuillez vérifier selon les indications de la page :

- Si l'hôte et le port sont correctement remplis.
- Si le réseau entre le nœud de déploiement et la base de données est connecté.
- Si le USERNAME et PASSWORD sont corrects.
- Si le compte de la base de données dispose des autorisations requises.
- Si le Dameng Service de base de données et MySQL les configurations de compatibilité ont été appliquées normalement.

## 7. Continuer l'initialisation du déploiement

Après être revenu à la page "Configuration", confirmez que MySQL reste non coché, puis cliquez sur "Initialiser le déploiement" pour continuer à compléter la vue d'ensemble du déploiement, les vérifications et les étapes d'exécution.

> [!TIP]
>
> Avant d'initialiser le déploiement, veuillez confirmer à nouveau que la Dameng configuration a été enregistrée et que tous les éléments de vérification ont réussi.
