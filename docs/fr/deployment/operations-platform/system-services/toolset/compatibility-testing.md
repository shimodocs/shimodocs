# Tests de compatibilité

[← ShimoDocs Suite documentation de déploiement](../../../README.md)

## 1. Aperçu de la page

La page de test de compatibilité est utilisée pour vérifier la configuration du stockage d'objets, la connectivité, la compatibilité de téléchargement et les performances de téléchargement. La page est divisée en :

1. Configuration du stockage ;
2. Test de compatibilité ;
3. Test de performance.

## 2. Configuration du stockage 

### 2.1 Description de l'élément de configuration 

| Élément de configuration | Fonction |
| --- | --- |
| Clé d'accès | Identifiant d'accès au stockage d'objets, c'est-à-dire AK |
| Clé secrète | La clé d'accès accompagnant l'AK, c'est-à-dire SK |
| Point de terminaison | Adresse du service de stockage d'objets |
| Nom du bucket | Bucket cible à détecter |
| Région | Zone de localisation du bucket de stockage |
| Point de terminaison public | Le domaine public utilisé par le navigateur pour accéder au stockage d'objets, facultatif |
| Style de chemin | Utilisation du format 'endpoint/bucket/object' pour accéder aux objets, des services comme MinIO nécessitent généralement d'activer |

### 2.2 Remplir la configuration 
1. Cliquez sur « Remplir le bucket pièces jointes » ou « Remplir le bucket contenus » selon le besoin. 
2. Le système remplit automatiquement les configurations telles que AK, SK, Endpoint, Bucket, Région, etc., correspondant à l'environnement actuel. 
3. Si vous n'utilisez pas le remplissage automatique, vous pouvez également remplir manuellement toutes les configurations. 
4. Vérifiez si le point de terminaison contient le protocole et le port corrects. 
5. Vérifiez si le nom du Bucket est le bucket qui doit être détecté cette fois-ci. 
6. Vérifiez si la région correspond à la région réelle du stockage d'objets.
7. Si vous avez besoin que le navigateur accède directement au stockage d'objets, remplissez le point de terminaison public.
8. Décidez d'activer le mode Path Style selon le type de stockage d'objets.

Après avoir rempli automatiquement le bucket des pièces jointes cette fois-ci :

- Bucket : `shimo-attachments`;
- Point de terminaison : rempli automatiquement par le système ;
- Région : `cn-north-1`;
- Point de terminaison public : rempli automatiquement par le système ;
- Mode Path Style : activé.

Après avoir cliqué sur « Remplir le bucket Contents », le Bucket peut basculer automatiquement vers `file-contents`.

> La clé secrète est une information sensible ; ne l'affichez pas en texte clair sur des captures d'écran, chats ou tickets.

## 3. Test de compatibilité

Le test de compatibilité vérifiera successivement le téléchargement backend, le téléchargement direct via le navigateur et le téléchargement multipart.

### 3.1 Démarrer le test

1. Complétez la configuration du stockage.
2. Cliquez sur l'onglet « Test de compatibilité ».
3. Confirmez à nouveau que la configuration du Bucket, du Point de terminaison, de la Région, du Point de terminaison public et du mode Path Style est correcte.
4. Cliquez sur « Démarrer le test de compatibilité ».
5. Attendez que la page indique « Test de compatibilité terminé ».
6. Vérifiez si le statut récapitulatif sur la page est « Tout réussi ».
7. Vérifiez séparément le statut, la durée et les résultats des trois tests.

### 3.2 Test de téléchargement backend

Ce test est utilisé pour vérifier la connectivité réseau et les autorisations d'écriture du service backend vers le stockage d'objets.

1. Le backend génère un fichier texte de test.
2. Le backend écrit le fichier dans le Bucket cible.
3. La page affiche la durée de téléchargement et le chemin de l'objet de test.
4. Un statut de succès vert indique que le réseau backend et les permissions d'écriture sont normaux.

Résultat actuel : Téléversement réussi, durée `157 ms`.

### 3.3 Test de téléversement direct via navigateur

Ce test est utilisé pour vérifier le lien permettant au navigateur de téléverser directement vers le stockage d'objets via PostPolicy.

1. La page demande au backend le PostPolicy nécessaire pour le téléversement direct.
2. Le navigateur utilise le point de terminaison public pour téléverser directement les fichiers vers le stockage d'objets.
3. La page vérifie le HTTP code de statut renvoyé par le stockage d'objets.
4. HTTP 204 indique que le téléversement direct via navigateur a réussi.

Résultat cette fois : Téléversement direct depuis le navigateur réussi, durée `61 ms`, code de statut `204`.

### 3.4 Test de téléversement multi-parties

Ce test est utilisé pour vérifier le processus complet de téléversement multi-parties de fichiers volumineux.

1. Initialiser la tâche de téléversement multi-parties.
2. Diviser le fichier de test en plusieurs parties.
3. Téléverser chaque partie séquentiellement.
4. Appeler l'interface de fusion pour compléter la création de l'objet.
5. La page affiche `multipart upload succeeded`, indiquant que l'ensemble du processus a été réussi.

Résultat cette fois : Téléversement multi-parties réussi, durée `1746 ms`.

### 3.5 Description des objets de test

Le test de compatibilité effectuera des écritures réelles dans le Bucket cible, et les chemins des objets de test backend sont généralement similaires à :

```text
compatibility-tests/<RANDOM UUID>.txt
```

1. Confirmer que le Bucket cible est correct avant de lancer les tests.
2. Ne pas exécuter les tests de manière indiscriminée dans un mauvais Bucket de production.
3. Après le test, vous pouvez vérifier et nettoyer les objets de test selon la politique de nettoyage de l'environnement.

## 4. Test de performance

Les tests de performance sont utilisés pour mesurer le temps de téléchargement et le débit pour différentes tailles de fichiers.

### 4.1 Configuration des tests de performance

1. Cliquez sur l'onglet « Tests de performance ».
2. Sélectionnez le mode de test ; la page utilise par défaut « Navigateur Direct ».
3. Sélectionnez le type de contenu ; vous pouvez utiliser `application/octet-stream` par défaut.
4. Choisissez la taille de fichier que vous souhaitez tester.
5. La page prend en charge 1, 5, 8, 10, 12, 15, 20 et 25 Mo.
6. Dans l'environnement de test, vous pouvez d'abord sélectionner 1 Mo pour une validation rapide.
7. Pour une comparaison de performance formelle, sélectionnez plusieurs tailles de fichiers pour le test.

### 4.2 Démarrer le test de performance

1. Confirmez que la configuration du stockage est correcte.
2. Confirmez qu'au moins une taille de fichier a été sélectionnée.
3. Cliquez sur 'Démarrer le test de performance'.
4. Attendez que tous les fichiers aient terminé le téléchargement.
5. Vérifiez le débit moyen, le temps le plus court et le temps le plus long.
6. Vérifiez le statut, le temps écoulé, le débit et les messages d'erreur pour chaque taille de fichier.
7. En cas d'échec, vérifiez d'abord le réseau du navigateur, le point de terminaison public, la configuration cross-origin et l'état du stockage d'objets.

### 4.3 Résultats de ce test

Ce test utilise uniquement des fichiers de 1 Mo pour effectuer un test léger de téléchargement direct via le frontend :

| Métrique | Résultat |
| --- | ---: |
| Taille du fichier | 1,0 Mo |
| Statut | Succès |
| Temps | 874 ms |
| Débit | 1,14 Mo/s |
| Message d'erreur | Aucun |

Résultats réels : Téléchargement réussi, et la page peut générer correctement les métriques de temps écoulé et de débit.

> Les résultats de performance peuvent être affectés par le réseau du navigateur, la charge du cluster, les liens proxy et la charge du stockage d’objets. Un seul test ne peut vérifier que la disponibilité fonctionnelle ; l’acceptation formelle des performances doit être testée de manière répétée dans le même environnement et les statistiques pour P50, P95, et les taux d’échec doivent être enregistrés.

## 5. Précautions
1. Confirmez le Bucket cible avant d’effectuer les tests afin d’éviter d’écrire des fichiers de test dans le mauvais bucket de stockage.
2. Ne pas afficher la Clé Secrète en texte clair dans les documents ou les captures d’écran.
3. Le téléchargement direct via navigateur dépend du Point de terminaison public et de la configuration CORS.
4. S3Les services compatibles comme MinIO requièrent généralement l’activation du Style de chemin.
5. Les tests de performance génèrent un trafic réseau réel et des écritures dans le stockage ; évaluez l’impact sur l’environnement avant de tester de gros fichiers.
6. L’acceptation formelle des performances doit être effectuée en plusieurs tours ; un seul résultat de test de navigateur n’est pas suffisant.
