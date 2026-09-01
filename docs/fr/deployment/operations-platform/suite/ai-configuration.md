# Configuration de l'IA

[← ShimoDocs Suite documentation de déploiement](../../README.md)

La configuration de l'IA est utilisée pour se connecter ShimoDocs Suite avec les modèles de base, les modèles d'image, la recherche en ligne et les services d'Embedding. Après avoir terminé la configuration, les fonctions dans ShimoDocs Suite telles que les conversations AI, la génération de contenu, le traitement d'images et la récupération de connaissances peuvent accéder aux services correspondants. 

## 1. Comprendre les quatre types de capacités avant de configurer 

Les objectifs des quatre types de configurations sont différents, et vous n’avez pas forcément besoin de les configurer tous. Veuillez choisir en fonction des ShimoDocs Suite fonctionnalités que vous prévoyez d’activer. 

| Type de configuration | Objectif | Obligatoire à configurer | 
| --- | --- | --- |
| Modèle de base | Gère les conversations, l'écriture, le résumé, la réécriture, le Q&R et d'autres tâches textuelles ou multimodales | Généralement nécessaire lors de l'utilisation des fonctionnalités AI |
| Modèle d'image | Génère ou modifie des images | Seulement nécessaire lors de l'utilisation des fonctionnalités de génération ou de modification d'images |
| Recherche en ligne | Récupère des informations à partir de services de recherche externes pour compléter les références du modèle | Seulement nécessaire lors de l'utilisation des fonctionnalités de recherche en ligne |
| Embedding | Convertit le texte en vecteurs pour la récupération de bases de connaissances, la recherche sémantique et des fonctionnalités similaires | Seulement nécessaire lors de l'utilisation des fonctionnalités de récupération de connaissances ou de recherche vectorielle |

> [!TIP]
>
> La recherche en ligne est généralement un service de recherche indépendant et n'est pas la même que les capacités en ligne intégrées dans le modèle de base. 

## 2. Accéder à la configuration AI 

1. Connectez-vous à la **MDP Plateforme d'Opérations**. 
2. En haut, sélectionnez **ShimoDocs Suite**.
3. Dans la barre latérale gauche, sélectionnez **Gestion des locataires**.
4. Trouvez la **Configuration de l'IA** carte.
5. Cliquez sur la carte pour entrer sur la page « Configuration du modèle AI et de la recherche ».

## 3. Tout d'abord, choisissez le fournisseur de service de modèle à connecter

Veuillez d'abord confirmer le service de modèle que vous prévoyez d'utiliser, puis allez dans la section correspondante pour la configuration.

| Type de modèle | Fournisseur de services |
| --- | --- |
| Modèle de base | Fournisseurs compatibles avec le protocole OpenAI Responses |
| Modèle d'image | Fournisseurs compatibles avec le protocole OpenAI Image |
| Modèle de moteur de recherche Internet | Actuellement, seule Volcengine est prise en charge |
| Modèle d'intégration | Fournisseurs compatibles avec le protocole OpenAI embedding |

## 4. Configurations du modèle IA

Cette section est utilisée pour configurer GPTles services liés. Veuillez demander à l'ingénierie de confirmer s'il faut utiliser les services officiels OpenAI, Azure OpenAI, les services proxy ou d'autres interfaces compatibles, car l'adresse de requête et l'ID du modèle peuvent varier selon la méthode de connexion.

### 4.1 Modèle de base

Le modèle de base est utilisé pour le dialogue, la génération de contenu, le résumé, la réécriture et les fonctions de compréhension multimodale.

#### Configuration du fournisseur

| Élément de configuration | Valeur d'exemple | Description |
| --- | --- | --- |
| Fournisseur | Sélectionnez OpenAI (ou compatible avec le protocole OpenAI Responses) | Sélectionnez OpenAI (ou compatible avec le protocole OpenAI Responses) |
| Requête URL / De base URL | https://myai.com/v1 | Choisissez votre propre adresse de passerelle IA compatible avec le protocole OpenAI Responses |
| API Clé | sk-I••••haTO | Le API Clé qui vous a été attribuée par votre passerelle IA |
| Modèle par défaut | gpt-5.5 | Modèle compatible avec le protocole OpenAI Responses |

> [!TIP]
>
> Le fournisseur de modèle configuré ici doit prendre en charge le mode streaming. ShimoDocs L'IA, en tant que client, enverra toujours `stream: true` lors de la requête auprès du fournisseur de modèle. Si le fournisseur de modèle ne prend pas en charge le mode streaming, la requête échouera.

#### Configuration du modèle

| Élément de configuration | Valeur d'exemple | Notes de développement |
| --- | --- | --- |
| Statut | Activé | Doit être activé |
| ID du modèle | gpt-5.5 | ID de modèle valide |
| Nom du modèle | gpt-5.5 | Doit correspondre à l'ID du modèle |
| Fenêtre de contexte | 1024000 | Remplir selon les conditions réelles |
| Entrée de texte | Activé | Doit être activé |
| Entrée d'image | Activé | Doit être activé |

### 4.2 Modèle d'image

Les modèles d'image sont utilisés pour la génération ou l'édition d'images. Veuillez remplir les modèles et capacités réellement pris en charge par la version actuelle.

| Élément de configuration | Valeur d'exemple | Notes d'ingénierie |
| --- | --- | --- |
| Statut | Activé | Doit être activé |
| Fournisseur | OpenAI (ou compatible avec le protocole d'images OpenAI) | OpenAI (ou compatible avec le protocole d'images OpenAI) |
| Nom du modèle | gpt-image-2 | Doit être compatible avec le protocole d'images OpenAI |
| Requête URL / De base URL | https://myai.com/v1 | Sélectionnez votre propre adresse de passerelle AI compatible avec le protocole de réponses OpenAI |
| API Clé | sk-I••••haTO | Le API Clé qui vous a été attribuée par votre passerelle IA |
| Fonctionnalités | génération d'image, édition d'image | Conservez la génération d'image et l'édition d'image par défaut |

> [!TIP]
>
> Actuellement, ne prend en charge que OpenAI Image API protocole

### 4.3 Modèle de recherche Internet

La recherche sur le réseau prend actuellement uniquement en charge la configuration de Volcengine.

| Élément de configuration | Valeur d'exemple | Notes d'ingénierie |
| --- | --- | --- |
| Statut | Activé | Activer selon les besoins réels. Si activé, vous devez compléter les valeurs de toutes les autres entrées du groupe de configuration actuel |
| Fournisseur de services | Volcengine | Actuellement, seule Volcengine est prise en charge |
| API Point de terminaison | https://open.feedcoopapi.com/search_api/web_search | Adresse de recherche réseau par défaut de Volcengine |
| API Clé | mCmh•••••••• | À obtenir auprès du fournisseur de service de recherche réseau |
| Paramètre de délai d'attente | 120s | Si une seule requête de recherche réseau dépasse ce temps, elle échouera. Il est recommandé de la maintenir à 120 s |

### 4.4 Modèle d'embedding

Les modèles d'embedding sont utilisés pour la récupération dans la base de connaissances et la recherche sémantique. L'ID du modèle et la dimension doivent correspondre à la sortie vectorielle réelle.

| Élément de configuration | Valeur d'exemple | Notes de développement |
| --- | --- | --- |
| Fournisseur de services | OpenAI (ou modèle d'embedding compatible OpenAI) | OpenAI (ou modèle d'embedding compatible OpenAI) |
| Base URL | https://myai.com/v1 | Choisissez votre propre adresse de passerelle AI compatible avec le protocole de réponses OpenAI |
| API Clé | ak-•••••••• | À obtenir auprès du fournisseur de modèles d'embedding |
| Modèle d'intégration | qwen3-embedding:4b | ID du modèle |
| Dimension | Valeur entière | La dimension est liée au modèle d'intégration ; vous pouvez consulter le fournisseur pour les paramètres de dimension |

| Éléments de confirmation du développement | Contenu |
| --- | --- |
| Modèles d'intégration pris en charge | OpenAI (ou modèle d'embedding compatible OpenAI) |
| Dimension vectorielle recommandée | Lié au modèle d'intégration |
| Est-il nécessaire de reconstruire les données vectorielles pour différentes dimensions | Oui |

> [!TIP]
>
> Actuellement prend en charge uniquement OpenAI Embedding API protocole

### 4.5 GPT Vérification de l'achèvement de la configuration

| Élément de vérification | Résultat attendu | Résultat réel |
| --- | --- | --- |
| Conversation avec le modèle de base | Saisir une question simple dans la session AI | Le modèle renvoie le résultat correspondant |
| Traitement de texte long | Saisir un texte long | Le modèle renvoie le résultat correspondant en fonction du contenu du texte long |
| Entrée d'image ou traitement d'image | Saisir une image pour reconnaissance | Peut renvoyer le contenu reconnu |
| Recherche Internet | Lui demander de récupérer des informations sur les billets d'avion ou de train | Peut renvoyer les résultats des billets d'avion ou de train |
| Vectorisation d'intégration | Utiliser des mots-clés pour la recherche AI sur le site | Peut renvoyer le contenu correspondant attendu |

## 5. Signification commerciale de chaque élément de configuration

Cette section fournit une explication unifiée de l'objectif de chaque élément de configuration sur la page. Lors de la première configuration, vous pouvez remplir selon le modèle du fournisseur mentionné précédemment, puis revenir à cette section pour confirmer si chaque champ répond aux exigences commerciales réelles.

### 5.1 Éléments de configuration du fournisseur de modèle de base

| Élément de configuration | Signification commerciale | Impact courant d'une saisie incorrecte | Requis |
| --- | --- | --- | --- |
| Fournisseur | Indique au système quelle méthode d'adaptation du modèle utiliser. Même si deux services sont compatibles avec des interfaces similaires, l'option du fournisseur peut déterminer le format de la requête, la méthode d'authentification et la manière dont les résultats sont analysés. | Peut échouer à enregistrer, format de requête incompatible ou réponse impossible à analyser. | Oui |
| Requête URL / De base URL | L'adresse d'entrée ShimoDocs Suite à laquelle on accède lors de l'envoi de requêtes au service de modèle. | Impossible de se connecter au modèle si l'adresse est incorrecte ; l'interface peut sembler inexistante si le niveau du chemin est incorrect. | Oui |
| API Clé | Identifiant utilisé par le service de modèle pour identifier l'appelant et vérifier les permissions. | Provoque généralement un échec de l'authentification si incorrect, expiré ou permissions insuffisantes. | Oui |
| Modèle par défaut | Le modèle que le système privilégie d'appeler lorsque les fonctions métier ne spécifient pas explicitement un modèle. | Certaines fonctions d'IA peuvent être indisponibles si non configurées ou configurées sur un modèle indisponible. | Oui |

### 5.2 Éléments de configuration de base du modèle

| Élément de configuration | Signification commerciale | Impact courant d'une saisie incorrecte | Requis |
| --- | --- | --- | --- |
| Statut | Contrôle si le modèle est autorisé à être appelé par ShimoDocs Suite. Après fermeture, la configuration peut être conservée, mais l'activité métier ne peut généralement pas continuer à utiliser le modèle. | Même si la configuration du modèle est correcte, si le statut est fermé, l'activité métier peut toujours le voir comme indisponible. | Oui |
| ID du modèle | Le nom ou identifiant unique du modèle reconnu par l'interface du service de modèle. | Provoque généralement un message indiquant que le modèle n'existe pas si incohérent avec le nom du serveur. | Oui |
| Nom du modèle | Le nom affiché aux administrateurs dans la plateforme d'exploitation pour aider à distinguer différents modèles. | Si le nom est dupliqué ou peu clair, il est facile de sélectionner le mauvais modèle ; la participation aux requêtes réelles est confirmée par l'ingénierie. | Oui |
| Fenêtre de contexte | La quantité totale d'informations qu'un modèle peut traiter dans une seule requête, affectant généralement la longueur du texte d'entrée, l'historique des conversations et l'espace de sortie. | La définir à une valeur supérieure à la capacité réelle du modèle peut provoquer des échecs de requête ; la définir trop basse peut entraîner la troncature du contenu ou l'impossibilité de le soumettre. | Oui |
| Entrée de texte | Indique si le modèle peut accepter du contenu textuel. | Si elle est définie incorrectement sur désactivé, les fonctions liées au texte peuvent ne pas être capables de sélectionner ou d'appeler ce modèle. | Oui |
| Entrée d'image | Indique si le modèle de base peut comprendre les images téléchargées par l'utilisateur ; il s'agit d'une capacité d'entrée multimodale et ce n'est pas la même chose que la génération d'images. | L'activer pour un modèle qui ne prend pas en charge les images peut provoquer des échecs de requête ; si désactivé, la fonction de compréhension des images sera indisponible. | Oui |

### 5.3 Options de configuration du modèle d'image

| Élément de configuration | Signification commerciale | Effets courants des paramètres incorrects | Requis |
| --- | --- | --- | --- |
| Statut | Contrôle si le modèle d'image peut être appelé par les fonctions de génération ou de modification d'images. | Si le statut est désactivé, les fonctions d'image associées ne peuvent pas utiliser le modèle. | Oui |
| Fournisseur de services | Détermine la méthode d'adaptation de l'interface utilisée pour les requêtes d'image. | Un choix incorrect peut entraîner des paramètres de requête ou des formats de retour incompatibles. | Oui |
| Nom du modèle / ID du modèle | Spécifie le modèle d'image réel à appeler. Si ce champ correspond au nom affiché ou à l'ID de la requête doit être clarifié par l'ingénierie. | Si le nom est incohérent avec le serveur, cela peut indiquer que le modèle n'existe pas. | Oui |
| Base URL | L'adresse du service à laquelle les requêtes de génération ou d'édition d'image sont envoyées. | Si l'adresse ou le chemin est incorrect, le service d'image ne peut pas être appelé. | Oui |
| API Clé | Les informations d'authentification utilisées pour appeler le service d'image. | Les erreurs, l'expiration ou le manque d'autorisation entraîneront des échecs d'authentification. | Oui |
| Fonctionnalités | Déclare les capacités d'image prises en charge par le modèle, telles que la génération d'images, l'édition d'images, etc. | Si une capacité non prise en charge par le modèle est configurée, l'entrée commerciale peut être visible mais l'appel échouera. | Oui |

Remarque : Actuellement, seul le protocole OpenAI Image API est pris en charge

### 5.4 Configuration de la recherche Internet

| Élément de configuration | Signification commerciale | Impact courant en cas d'erreur | Requis |
| --- | --- | --- | --- |
| Statut | Contrôle si ShimoDocs Suite peut appeler le service de recherche actuel. | Lorsque le statut est désactivé, le modèle peut toujours être disponible, mais il ne peut pas obtenir de résultats de recherche Internet. | Non |
| Fournisseur de services | Spécifie le type de service de recherche à utiliser et sa méthode d'adaptation d'interface. | Si choisi incorrectement, les requêtes et l'analyse des résultats peuvent être incompatibles. | Non |
| Adresse de l'interface | Le point de terminaison du service accédé lors de l'initiation d'une requête de recherche. | Si l'adresse est incorrecte, la fonction Internet peut expirer ou échouer à se connecter. | Non |
| API Clé | Identifiant d'authentification utilisé par le service de recherche. | Si incorrect ou avec des permissions insuffisantes, les requêtes de recherche seront refusées. | Non |
| Paramètre de délai d'attente | Temps maximum d'attente pour une seule recherche ; si dépassé, le système cesse d'attendre et considère cela comme un échec ou aucun résultat. | Le définir trop court entraînera des expirations fréquentes ; le définir trop long augmente le temps d'attente de l'utilisateur. | Non |

### 5.5 Configuration de l'Embedding

Les modèles d'Embedding n'ont pas nécessairement besoin d'être activés, mais s'ils ne sont pas activés, le contenu des documents ne peut pas être vectorisé, et donc le système ne peut pas traiter les questions liées à la base de connaissances de l'utilisateur.

| Éléments de configuration | Signification commerciale | Conséquences courantes en cas de remplissage incorrect | Est-ce obligatoire |
| --- | --- | --- | --- |
| Base URL | L'adresse du service envoyée à la demande de vectorisation de texte. Si l'adresse est incorrecte, les données vectorielles ne peuvent pas être générées ou mises à jour. Non |
| API Clé | Les identifiants utilisés par le service d'Embedding. La vectorisation échoue en raison d'une erreur, d'une expiration ou d'un manque d'autorisation. Non |
| Modèle d'intégration | L'ID du modèle réellement responsable de la conversion du texte en vecteurs. Les vecteurs ne peuvent pas être générés lorsque le modèle n'existe pas ou ne correspond pas. Non |
| Dimension | La longueur finale du vecteur généré pour chaque ligne de texte doit correspondre à la sortie réelle du modèle et à la configuration de stockage des vecteurs. | Si les dimensions sont incohérentes, l'écriture ou la récupération n'est généralement pas possible ; après modification des dimensions, il peut être nécessaire de régénérer les vecteurs existants. Non |

Remarque : Actuellement, seul le protocole OpenAI Embedding API protocole 

## 6. Séquence de configuration recommandée 

Pour réduire les duplications, il est recommandé de configurer dans l'ordre suivant : 

1. Tout d'abord, confirmer quelles fonctionnalités d'IA doivent être activées dans ShimoDocs Suite. 
2. Sélectionner un modèle de base qui répond aux exigences du protocole. 
3. Configurer le fournisseur et ajouter au moins un modèle de base. 
4. Définir le modèle disponible validé comme modèle par défaut. 
5. Configurer les modèles d'image selon les besoins de l'entreprise. 
6. Configurer la recherche en réseau en fonction des besoins de l'entreprise. 
7. Si vous utilisez des bases de connaissances ou la recherche sémantique, configurez alors l'Embedding. 
8. Après l'enregistrement, vérifier chaque capacité séparément; ne pas juger du succès de la configuration uniquement par l'affichage "Activer" sur la page. 

## 7. Règles de configuration efficaces 
| Problèmes nécessitant la confirmation de l'ingénierie | Contenu |
| --- | --- |
| Prend-il effet immédiatement après l'enregistrement de la configuration | Cela ne prend pas effet immédiatement ; vous devez attendre 1 à 2 minutes |
| Avez-vous besoin de redémarrer le service | Pas besoin de redémarrer le service |
| La nouvelle configuration prend-elle effet sur une page ouverte | Vous devez actualiser la page actuelle |
| Sélection de priorité parmi plusieurs modèles | Non pris en charge |
| Est-ce qu'il se bascule automatiquement lorsque le modèle par défaut n'est pas disponible ? | Non pris en charge |

## 8. Dépannage des problèmes courants 

| Phénomène | Causes courantes | Méthode de dépannage |
| --- | --- | --- |
| Échec du service de modèle de connexion | Anomalies d'adresse de requête, de réseau, de certificat ou de port | Vérifiez l'adresse du service, DNS, le port, le certificat et les politiques de pare-feu | 
| Échec d'authentification encouru | API Erreur de clé, expirée ou autorisations insuffisantes | Confirmez que la API Clé est correcte et a accès au modèle ou service ciblé | 
| Modèle inexistant signalé | L'ID du modèle ne correspond pas au nom côté serveur | Confirmez l'ID complet du modèle et vérifiez la casse et le suffixe de version | 
| Texte disponible mais images non disponibles | Le modèle ne prend pas en charge l'entrée d'image ou le commutateur d'entrée d'image n'est pas activé | Vérifiez la capacité du modèle et le commutateur d'entrée | 
| Entrée de fonctionnalité image existante mais appel échoué | Les fonctionnalités ne correspondent pas aux capacités réelles du modèle | Vérifiez les capacités de génération et d'édition supportées par le modèle d'image | 
| Dépassements de délai fréquents lors de la recherche en ligne | Le service de recherche est lent, le réseau est instable ou le paramètre de délai est trop court | Vérifiez la latence du réseau, la performance du service et les paramètres de délai | 
| Échec d'écriture d'embedding | Les dimensions de sortie ne sont pas cohérentes avec la configuration de stockage vectoriel | Vérifiez les dimensions de sortie réelles du modèle et la configuration de stockage | 

## Questions et réponses

1. Comment vérifier si la configuration est effective ?

Après avoir terminé la configuration, vous pouvez aller dans la barre latérale de l'éditeur pour ouvrir une session IA afin de vérifier si la fonction fonctionne :

- Les messages devraient recevoir une réponse normalement
- Si un modèle d'image est configuré, vous pouvez envoyer une commande comme 'Générer une image de Xxx' et vérifier si la commande s'exécute correctement
- Si la recherche en ligne est configurée, vous pouvez envoyer une commande comme 'Rechercher en ligne la météo d'aujourd'hui à Pékin' et vérifier si le résultat répond aux attentes

2. Prend-il en charge l'interface /chat/completions ?

Non pris en charge pour le moment. Actuellement, seul le protocole OpenAI Responses API est pris en charge. Il est connu que des API officielles comme Deepseek / Xiaomi-Mimo offrent un support pour ce protocole. Des solutions de déploiement local comme vLLM et Ollama prennent également en charge le protocole Responses.
