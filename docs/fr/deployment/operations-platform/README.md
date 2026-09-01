# Aperçu de la plateforme d'opérations

[← ShimoDocs Suite documentation de déploiement](../README.md)

## Aperçu des fonctions

- **ShimoDocs Suite**: Utilisé pour gérer les autorisations, les locataires, les utilisateurs, l'image de marque et les configurations d'IA liées à ShimoDocs Suite.
- **Services Systèmes**: Utilisé pour les tâches générales d'exploitation et de maintenance telles que la configuration globale, la gestion des clusters, la visualisation des journaux, l'inspection des fonctionnalités, la requête de problèmes, la réparation de documents et **les mises à niveau du système**.

> **Remarque**: Les fonctionnalités spécifiques affichées dépendent de la version de déploiement actuelle et des fonctionnalités activées.

## Connexion à la Plateforme d'Opérations

Accédez à l'adresse suivante dans votre navigateur :
> **Exigences du navigateur**: Veuillez utiliser Google Chrome version 111 ou supérieure pour accéder à la Plateforme d'Opérations. Il est recommandé de mettre à jour d'abord vers la dernière version stable.

```text
http(s)://<OPERATIONS_PLATFORM IP OR_DOMAIN_NAME>/mdp/user/login
```

Entrez le compte administrateur et PASSWORD, puis cliquez sur "Connexion."

## Découverte de la page d'accueil de la Plateforme d'Opérations

Après la connexion, vous pouvez accéder aux fonctions de gestion correspondantes via le menu sur le côté gauche de la page. Le menu affiché dépend des produits et des versions qui sont déployés et autorisés dans l'environnement actuel.

## Réinitialisation de l'Administrateur PASSWORD En cas d'oubli

Si l'administrateur de la Plateforme d'Opérations PASSWORD est oublié, vous pouvez vous connecter au nœud de déploiement et exécuter la commande suivante pour le réinitialiser.

```bash
kubectl exec -it $(kubectl get pods -l app=mdp -o jsonpath='{.items[0].metadata.name}') -- reset-admin-password Aa1234567.
```

L'exemple ci-dessus réinitialise le PASSWORD à `Aa1234567.`. Dans le fonctionnement réel, veuillez remplacer l'exemple PASSWORD à la fin de la commande par un nouveau PASSWORD qui répond aux exigences de sécurité.

Une fois la réinitialisation terminée, retournez à la page de connexion, connectez-vous en utilisant le nouveau PASSWORDet confirmez que le menu peut être accessible normalement.
