# Surveillance des ressources statiques

[← ShimoDocs Suite documentation de déploiement](../../../README.md)

La surveillance des ressources statiques est utilisée pour vérifier les JS et CSS ressources référencées par les pages Web, ce qui vous aide à comprendre l'état d'accès aux ressources, les protocoles de transmission, la configuration du cache et CDN l'utilisation.

> Le nom de la fonction sur la page système est 'Détection des ressources statiques'.

## 1. Comment utiliser

Connectez-vous à la plateforme de gestion, sélectionnez **Services Systèmes** en haut, puis choisissez **Collection d'outils > Détection des ressources statiques** dans la barre de navigation gauche.

Cette fonctionnalité est uniquement disponible pour les administrateurs. Si vous ne voyez pas l'entrée, veuillez vérifier les autorisations de votre compte et la version actuelle du produit.

1. Saisissez la page complète URL, par exemple `https://example.com/recent`.
2. Si la page nécessite une connexion, développez 'En-têtes de requête personnalisés' et remplissez les informations nécessaires telles que `Cookie` et `Authorization`.
3. Cliquez sur 'Démarrer la détection' et attendez que les résultats reviennent.

> Les en-têtes de requête seront également utilisés pour accéder aux ressources statiques référencées par la page. Veuillez utiliser uniquement des identifiants temporaires et vous assurer que les domaines des ressources cross-origin sont fiables. L'adresse de la page, les en-têtes de requête et les résultats de détection les plus récents seront enregistrés dans le navigateur actuel.

## 2. Portée de la détection

Le système identifiera les JS et CSS directement référencés dans la page HTML, mais il ne détecte pas les images, les polices, le code inline ni les ressources chargées dynamiquement après l'exécution du script.

- Jusqu'à 3 JS du même domaine et CSS fichiers seront détectés pour chaque domaine ;
- Jusqu'à 50 ressources cross-domain peuvent être détectées à la fois ;
- Les URL en double ne sont comptées qu'une seule fois.

Les ressources du même domaine qui ne sont pas détectées seront marquées comme « échantillonnage du même domaine ignoré », et cela n'indique pas une erreur de ressource.

## 3. Consultation des résultats

Une fois la détection terminée, la page affichera :

- **Informations récapitulatives**: Nombre de HTML ressources, nombre détecté, nombre de problèmes, utilisation du cache, CDN, et HTTP/2;
- **Réponse de la page**: Code d'état, protocole et informations de cache de la page cible ;
- **Liste des ressources**: URL, code d'état, protocole, cache, CDNproblèmes et en-têtes de réponse de chaque ressource.

La liste des ressources prend en charge le filtrage par « Détecté », « Tout » et « Problématique ». 

Le système indique principalement les problèmes suivants : 

- HTTP 4xx/5xx ; 
- Aucun cache valide détecté ; 
- HTTP/2 non utilisé ; 
- Les ressources cross-origin ne montrent pas CDN les caractéristiques ; 
- La requête a expiré ou la résolution du nom de domaine a échoué. 

## 4. Problèmes courants 

### Échec de la détection de la page 

Veuillez vérifier la page URL, la connectivité réseau, HTTPS le certificat et l'état de connexion. Le service de détection ne réutilise pas automatiquement les informations de connexion du navigateur, donc si nécessaire, veuillez fournir des en-têtes de requête personnalisés. 

### Ressource non reconnue 

Veuillez vous assurer que la page retourne une HTMLréponse normale. Les ressources chargées dynamiquement par des scripts ne seront pas reconnues. 

### CDN Affiche 'Non détecté' 

Ce résultat indique seulement qu'aucune CDN caractéristique n'a été détectée dans la réponse et ne signifie pas que la ressource n'utilise définitivement pas un CDN. Veuillez vérifier avec la CDN console et l'architecture réseau. 

## 5. Notes 

- Les résultats de détection reflètent ce qui a été observé depuis le réseau de la plateforme de gestion lors de cette requête et peuvent différer de l'expérience réelle de l'utilisateur. 
- CDN, le cache et l'état du problème sont des résultats déterminés automatiquement et ne servent qu'au diagnostic auxiliaire. 
- 'Aucun problème trouvé' ne signifie pas que la page a passé une évaluation complète de la performance, de la sécurité ou de l'utilisabilité. 
- Après la publication de la page, le CDN est actualisé, ou que l'environnement réseau change, il est recommandé de refaire le test.
