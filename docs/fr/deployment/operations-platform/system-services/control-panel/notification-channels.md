# Canaux de notification

[← ShimoDocs Suite documentation de déploiement](../../../README.md)

## Aperçu des fonctions

Les canaux de notification sont utilisés pour gérer de manière centralisée la réception des messages d'alerte du système, permettant l'inspection par le middleware et d'autres fonctions d'envoyer des notifications de panne et de récupération.

Les canaux actuellement pris en charge comprennent WeCom, DingTalk, Feishu et les Webhooks personnalisés.

## Accéder à la page

Après vous être connecté à la console d'administration, sélectionnez **Canaux de notification** dans la navigation de gauche pour accéder à la page.

Les canaux de notification ne sont disponibles que pour les administrateurs. Si vous ne voyez pas ce menu, veuillez contacter l'administrateur système pour confirmer les autorisations de votre compte.

## Créer un nouveau canal de notification

Cliquez **Créer un canal**, saisissez le nom du canal et sélectionnez le type de canal :

- **WeCom**: Entrez la clé Webhook du robot.
- **DingTalk**: Entrez le Webhook complet URL, et éventuellement saisissez le Secret de signature selon la configuration du robot.
- **Feishu**: Entrez le Webhook complet URL, et éventuellement saisissez le Secret de signature selon la configuration du robot.
- **Webhook personnalisé**: Saisissez la méthode de requête URL, HTTP et le modèle de corps.

Confirmez si vous souhaitez activer le canal, puis cliquez sur **Enregistrer**.

## Webhook personnalisé

Le modèle de corps d'un Webhook personnalisé prend en charge les variables suivantes : 

```text
{{title}}
{{body}}
{{level}}
```

Exemple de modèle par défaut : 

```json
{"title":"{{title}}","body":"{{body}}","level":"{{level}}"}
```

Lorsque le système envoie une notification, il remplacera les variables par le titre réel, le contenu et le niveau d'alerte. 

## Canal de test 

Après avoir enregistré, cliquez **Tester** sur le côté droit du canal. Le système enverra un message de test pour vérifier si l'adresse Webhook, la signature et la connexion réseau sont correctes. 

Il est recommandé de tester immédiatement après avoir créé ou modifié un canal, avant de le lier à l'inspection des intergiciels ou à d'autres fonctions métier. 

## Activer, Modifier et Supprimer 

- **Activer/Désactiver**: Ajustez l'état d'activation lors de la modification du canal. Lorsqu'il est désactivé, le canal ne recevra pas les notifications métier. 
- **Modifier**: Vous pouvez modifier le nom du canal, le type et la configuration du Webhook. 
- **Supprimer**: Supprimez les canaux qui ne sont plus utilisés. Les canaux référencés par les inspections du middleware doivent être détachés avant de pouvoir être supprimés. 

## Situations courantes

- **Échec de l'envoi du test**: Veuillez vérifier l'adresse Webhook, la clé, le secret, HTTP méthode, et autorisations d'accès au réseau.
- **Échec de l'enregistrement**: Veuillez vous assurer que tous les champs obligatoires sont remplis et que le Webhook URL Le format est correct.
- **Alertes commerciales non reçues**: Veuillez confirmer que le canal est activé et a été sélectionné sur la page commerciale correspondante.
- **Impossible de supprimer le canal**: Ce canal peut encore être utilisé par les inspections du middleware. Veuillez d'abord supprimer l'association et enregistrer la configuration de l'inspection.
- **Format de contenu reçu par Webhook personnalisé incorrect**: Veuillez vérifier si le modèle du corps répond aux exigences du système cible.

> L'adresse du webhook et la clé secrète de signature sont des informations sensibles. Veuillez limiter l'accès et éviter le partage public via des captures d'écran, des journaux ou des outils de chat.

## Interface d'opération exemple

La figure ci-dessous montre les types de canaux et le formulaire de configuration lors de la création d'un nouveau canal de notification.

