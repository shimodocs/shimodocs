# Déployer avec le stockage d'objets

[← ShimoDocs Suite documentation de déploiement](../../README.md)

Cet article explique comment désactiver le MinIO intégré dans le ShimoDocs et configurer celui du client S3 stockage d'objets en tant que stockage d'objets tiers. S3 Une fois la configuration terminée, l'installateur vérifiera le S3 connectivité réseau de stockage d'objets, informations d'authentification et permissions de lecture/écriture des buckets. Une fois les vérifications effectuées, le déploiement peut continuer. 

# 1. Préparation avant la configuration 
Avant de commencer, veuillez confirmer : 
- S3 le stockage d'objets est installé et fonctionne normalement. 
- K8s les nœuds du cluster peuvent accéder à l'hôte et au port du S3 stockage d'objets. 
- L'authentification AK/SK pour la connexion au S3 stockage d'objets est prête. 
- S3 le stockage doit supporter l'accès depuis le navigateur client. 
- Il est recommandé d'utiliser une instance séparée. S3  
- Standard S3 des adresses d'accès aux protocoles pour les réseaux internes et externes doivent être fournies. 
- ShimoDocs business-standard S3 les buckets doivent être créés à l'avance. 
- S3 le stockage doit utiliser des SSD disques. 

## ShimoDocs Business Standard S3 Liste des buckets 

| Nom du bucket | Permission d'accès | Origines autorisées | Méthodes autorisées | Mode d'accès | Exposer_entêtes | 
| --- | --- | --- | --- | --- | --- | 
| mention automatique | Privé Lecture/Écriture | - | - | Réseau interne |  | 
| compose-payloads | Privé Lecture/Écriture | - | - | Réseau interne |  | 
| fc-task | Privé Lecture/Écriture | - | - | Intranet |   |
| fichier-changements | Privé Lecture/Écriture | - | - | Intranet |
| fichier-calculé | Privé Lecture/Écriture | * | GET/HEAD | Intranet Extranet | 
| contenu-fichier | Privé Lecture/Écriture | * | GET/HEAD | Intranet Extranet | x-amz-meta-head x-amz-meta-length x-amz-meta-bytes x-amz-meta-delta-version x-amz-meta-eek x-amz-meta-formula-cache x-amz-meta-compressor |
| modèles-fichiers | Privé Lecture/Écriture | - | - | Intranet Extranet |
| historiques-feuilles | Privé Lecture/Écriture | - | GET/HEAD | Intranet Extranet | Access-Control-Allow-Origin x-amz-meta-compressor |
| svc-doc-history | Privé Lecture/Écriture | * | GET/HEAD | Intranet | Access-Control-Allow-Origin x-amz-meta-compressor | 
| shimo-assets | Lecture publique Écriture privée | * | GET/HEAD | Intranet Extranet | 
| shimo-attachments | Privé Lecture/Écriture | * | GET/POST/PUT/HEAD | Intranet Extranet |  |  |  |
| shimo-images | Lecture/écriture privée | * | GET/POST/PUT/HEAD | Réseau interne Réseau externe |  |  |  |
| shimo-users | Lecture/écriture privée | - | - | Réseau interne Réseau externe |  |  |  |
| shimo-avatar | Lecture publique écriture privée | * | GET | Réseau interne Réseau externe  |  |  |  |
| aperçu | Lecture/écriture privée | * | GET/HEAD | Réseau interne Réseau externe |Accept-Ranges x-amz-meta-head x-amz-meta-length x-amz-meta-bytes x-amz-meta-delta-version x-amz-meta-eek x-amz-meta-formula-cache x-amz-meta-compressor  |  |  |
| svc-drive | Lecture/écriture privée | * | GET/POST/PUT | Réseau interne Réseau externe |Accept-Ranges|  |  |
| svc-table | Lecture/écriture privée | * | GET/HEAD |  Réseau interne Réseau externe |  |  |  |
| instantanés de fichiers |  Lecture/écriture privée|  - | - |  Réseau interne Réseau externe |  |  |  |

## Instructions de configuration du compartiment
- Exposer_Il est recommandé de spécifier les noms des en-têtes, et de ne pas utiliser le * symbole, car certains fournisseurs peuvent ne pas prendre en charge * le symbole, par exemple Huawei Cloud OBS, Alibaba OSS
- Le nom du compartiment peut être configuré avec un préfixe selon les besoins de l'entreprise pour éviter les duplications

# 2. Entrer dans les Paramètres Avancés
Dans l'étape 'Configuration' de l'installateur, après avoir complété la configuration du réseau, de l'environnement cible et des informations sur le nœud, développez 'Configuration Avancée' en bas de la page.

# 3. Annuler l'installation de l'intégration MinIO
Dans la zone 'Services Middleware', décochez MinIO

Après avoir décoché, l'installateur n'installera plus le intégré MinIO, et un externe S3 Le stockage d'objets qui a été préparé sera utilisé plus tard. Le choix d'utiliser les services intégrés d'autres middleware doit être fait en fonction du plan de déploiement réel.

# 4. Ouvrir la Configuration du Middleware Tiers
Dans la zone 'Middleware Tiers', cliquez sur 'Configurer'.

# 5. Configurer S3 Stockage d'objets
1. Sélectionnez "S3 Stockage d'objets" à gauche.
2. Activer "MinIO Stockage d'objets."
3. Pour le Service Principal/Interaction de l'Éditeur, entrez respectivement : AK/SK, point de terminaison interne, point de terminaison public, hôte, port, SSL, et autres informations
4. Vérifiez et enregistrez

> [!TIP]
>
> Service Principal : L'instance de stockage d'objets utilisée pour les services autres que l'édition collaborative
> Interaction de l'Éditeur : L'instance de stockage d'objets utilisée par le service d'édition collaborative
>
> Remarque : le service principal et l'interaction de l'éditeur peuvent utiliser la même instance de stockage d'objets, mais fournir une instance distincte pour l'interaction de l'éditeur peut offrir de meilleures performances pour l'édition collaborative

## Nommage des compartiments

> [!NOTE]
>
> Lorsque plusieurs applications métiers partagent la même S3 instance, les clients peuvent ajouter des préfixes selon ShimoDocsles règles de nommage du compartiment pour aider à distinguer les différentes activités et gérer les compartiments

# 6. Confirmer les résultats de la vérification
L'installateur vérifiera les éléments suivants :
- login : Le compte peut s'authentifier normalement
- connectivité : L'environnement de déploiement peut accéder au S3 stockage d'objets
- permission : Le compte dispose des autorisations pour la connexion, l'authentification, la lecture/écriture des buckets, etc.

Après que tous les éléments d'inspection affichent 'Succès', fermez la fenêtre de configuration et retournez à la page 'Configuration' de l'installateur.

S'il y a des éléments échoués, veuillez vérifier selon les indications de la page :
- Si l'hôte et le port sont correctement remplis.
- Si le réseau entre le nœud de déploiement et le S3 Le stockage d'objets est connecté.
- Si USERNAME et PASSWORD sont correctes. 
- Vérifiez si le compte dispose des autorisations requises (connexion et authentification, permissions de lecture/écriture des buckets, etc.). 

# 7. Continuer l'initialisation du déploiement 
Après être revenu à la page 'Configuration', assurez-vous que S3 Stockage d'objets reste décoché, puis cliquez sur 'Initialiser le déploiement' pour continuer à compléter l'aperçu du déploiement, les vérifications et les étapes d'exécution. 

> [!TIP] 
> 
> Avant d'initialiser le déploiement, veuillez confirmer à nouveau que la S3 La configuration du Stockage d'objets a été enregistrée et tous les éléments de validation ont été réussis.
