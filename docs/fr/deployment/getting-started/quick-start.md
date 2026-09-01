# Démarrage rapide

[← ShimoDocs Suite documentation de déploiement](../README.md)

> [!TIP]
>
> Cet article explique comment utiliser le `mdp-installer` pour déployer rapidement un environnement neuf de ShimoDocs Suite.
>
> Cet article est rédigé pour le **installation en ligne tout-en-un sur un seul nœud** scénario, adapté pour l'installation initiale, l'expérience produit, la vérification des fonctionnalités et la pratique du processus de déploiement. Après avoir terminé cet article, vous pourrez obtenir le ShimoDocs Suite adresse d'accès aux affaires et le MDP adresse de la Plateforme d'Opérations.

> Les adresses IP, noms des packages d'installation, VERSIONet répertoires sur la page sont donnés à titre d'exemple. Lors du déploiement réel, veuillez vous référer à l'environnement actuel et aux documents fournis.

## 1. Aperçu du processus de déploiement

L'ensemble du processus peut être divisé en 7 étapes :

| Étape | Action à accomplir | Indicateur de complétion |
| --- | --- | --- |
| 1. Préparer les serveurs et les matériaux d'installation | Assurez-vous que les serveurs, l'installateur et ShimoDocs Suite le package de distribution sont disponibles | Pouvoir se connecter au serveur et localiser les fichiers d'installation |
| 2. Démarrer l'installateur | Exécuter `mdp-installer` sur le nœud d'installation | Le terminal affiche l'adresse d'accès de l'installateur |
| 3. Télécharger le paquet de distribution | Sélectionnez le ShimoDocs Suite paquet de distribution dans le navigateur | La page indique que le paquet de distribution a passé la vérification |
| 4. Configurer l'environnement de déploiement | Remplissez le domaine ou l'adresse IP, le mode de déploiement, les informations de connexion des nœuds et les répertoires de données | Les nœuds sont vérifiés avec succès et la vue d'ensemble du déploiement peut être consultée |
| 5. Vérification de l'environnement | Attendez que l'installateur vérifie le serveur et l'environnement de déploiement | Aucune défaillance ne bloque l'installation |
| 6. Démarrer l'installation | Confirmez les résultats de la vérification et exécutez le déploiement | La page montre que l'installation est terminée |
| 7. Enregistrer les informations de livraison | Enregistrez l'adresse d'accès et complétez la vérification de la connexion, du service et des fonctionnalités | Pages commerciales et MDP La plateforme des opérations peut être accessible |

## 2. Préparation avant le déploiement

### 1. Préparer un nœud d'installation

Le nœud d'installation est utilisé pour exécuter l'installateur et sert de serveur cible pour ce déploiement tout-en-un sur un seul nœud.

Avant de commencer, veuillez confirmer :

- Un serveur utilisable a été préparé et le SERVER_Une adresse IP a été obtenue.
- Le serveur peut être accessible via SSH.
- Le SSH utilisateur est `root`, ou dispose des autorisations nécessaires pour effectuer des tâches de déploiement.
- Le serveur CPU l'architecture correspond à celle de l'installateur et du paquet de distribution, par exemple, les deux sont `x86_64`.
- Le serveur répond aux spécifications de déploiement actuelles ; il est recommandé d'utiliser une installation minimale d'Ubuntu 24.04 LTS.
- La partition racine et l'espace de données répondent aux exigences de déploiement actuelles, et le répertoire de données a été déterminé.
- L'heure du serveur et le fuseau horaire sont corrects, et la synchronisation de l'heure est normale.
- L'ordinateur avec le navigateur peut accéder au port `18080/TCP` du nœud d'installation.
- Le serveur peut accéder à Internet pour télécharger les packages de déploiement et les ressources d'image en ligne.
- Si l'accès métier utilise des noms de domaine, la résolution des noms de domaine a été effectuée à l'avance (facultatif).

Les exigences minimales du serveur sont les suivantes :

| Système d'exploitation | Architecture | CPU | Mémoire | Disque |
| --- | --- | --- | --- | --- |
| Ubuntu 24.04 LTS | `x86_64` | 16 cœurs | 32 Go | 100 Go SSD |

De plus, veuillez confirmer :

- Ne pas partitionner `/root`, `/var`, `/tmp` séparément.
- Avant l'installation, ne pas déployer de composants supplémentaires tels que Docker ou Kubernetes sur le serveur qui pourraient affecter les vérifications de l'installateur.
- Port `22/TCP` est utilisé pour SSH, `18080/TCP` est utilisé pour la page web de l'installateur, `80/TCP` et `443/TCP` sont utilisés pour l'accès métier.

> Avant le déploiement officiel, il est recommandé de confirmer les spécifications du serveur selon la concurrence réelle, la taille des fichiers et les exigences de disponibilité ; le processus à nœud unique dans ce document convient pour un déploiement rapide et une vérification, pour une exploitation à long terme ou une haute disponibilité, veuillez utiliser le schéma de déploiement en cluster correspondant.

### 2. Préparer les matériels d'installation

#### Obtenir l'installateur

Téléchargez l'installateur fourni par ShimoDocs dans le répertoire `/root/` du nœud d'installation. Vous pouvez choisir l'une des méthodes suivantes :

- **Méthode 1 : Télécharger via SSH outil**. Télécharger `mdp-installer-amd64` dans le répertoire `/root/` répertoire du nœud d'installation.

#### Obtenir le paquet de distribution

Préparer le ShimoDocs Suite paquet de distribution pour ce déploiement. Le paquet de distribution est téléchargé sur la page d'installation web, et le nom et la version du fichier doivent correspondre à la livraison réelle.

Exemple de nom de fichier : `co1.8.20260711.3286-drive-release.tar.gz`

La nomenclature est la suivante :

| Fichier | Description |
| --- | --- |
| `mdp-installer` Installateur | Choisissez le fichier correspondant selon l'architecture du serveur, par exemple, `mdp-installer-amd64`. |
| ShimoDocs Suite Paquet de distribution | Le nom et la version du fichier doivent correspondre à la livraison réelle, par exemple, `co1.8.20260711.3286-drive-release.tar.gz`. |

Il est recommandé de placer l'installateur et les matériaux d'installation connexes dans le même répertoire de travail pour faciliter la récupération et le stockage ultérieurs. Avant d'utiliser le paquet de distribution, veuillez vous assurer que les fichiers sont complets et non endommagés par l'outil de transfert.

## 3. Lancer l'installateur

### 1. Se connecter au nœud d'installation

Connectez-vous au nœud d'installation via SSH et naviguez vers le répertoire où se trouve l'installateur. Par exemple :

```bash
ssh root@<INSTALL_NODE_IP>
cd /root
```

### 2. Ajouter les permissions d'exécution

Si l'installateur n'a pas encore la permission d'exécution, exécutez :

```bash
chmod +x ./mdp-installer-amd64
```
Le nom de fichier dans la commande doit être remplacé par le nom réel de l'installateur. 

### 3. Lancer la page d'installation web 

Exécutez : 

```bash
./mdp-installer-amd64 server
```

Si vous souhaitez que l'installateur continue à s'exécuter après la fermeture du terminal actuel, vous pouvez utiliser : 

```bash
nohup ./mdp-installer-amd64 server > nohup.out 2>&1 &
```

Après un démarrage réussi, le terminal affichera deux adresses : 

- `Local`: À utiliser uniquement par le nœud d'installation lui-même. 
- `Network`: Accessible par d'autres ordinateurs sur le même réseau. 

Si démarré en mode arrière-plan, vous pouvez exécuter la commande suivante pour voir la sortie de l'installateur : 

```bash
cat nohup.out
```

Ouvrir le `Network` adresse affichée dans le terminal dans un navigateur, par exemple :

```text
http://<INSTALL_NODE_IP>:18080/
```

> Pendant l'installation, veuillez garder le processus d'installation en cours. Ne fermez pas le processus d'installation et n'arrêtez pas le service en cours.

## 4. Télécharger ShimoDocs Suite Paquet de mise à jour

### 1. Sélectionner le paquet de mise à jour

Après être entré sur la page d'installation :

1. Cliquez **Démarrer le déploiement** ou l'entrée de sélection du paquet de mise à jour sur la page.
2. Sélectionnez le ShimoDocs Suite `.tar.gz` paquet de mise à jour à utiliser pour ce déploiement.
3. Attendez que le téléchargement du fichier et la vérification soient terminés.

### 2. Confirmer les résultats de la vérification

Après la réussite de la vérification, la page affichera le nom du package de version et l'entrée de configuration du déploiement.

Veuillez confirmer que les informations suivantes sont correctes :

- Le nom du package est cohérent avec la version livrée cette fois-ci.
- Le package de publication appartient au ShimoDocs Suite produit.
- La page n'a pas indiqué de corruption de fichier, d'erreurs de format ou de non-concordance du schéma.

Après confirmation, cliquez **Continuer** pour accéder à la configuration du déploiement.

Si la vérification échoue, veuillez reconfirmer si le package de distribution est complet, si le type de fichier est correct et si le package de distribution correspond à l'architecture du serveur. CPU 

## 5. Configuration de l'environnement de déploiement

### 1. Confirmer l'adresse réseau

Vérifiez le nom d'hôte ou l'adresse IP indiquée sur la page. Cette adresse doit être celle du nœud d'installation que l'utilisateur peut normalement accéder.

Ne pas entrer `127.0.0.1`, et n'utilisez pas d'adresses temporaires qui ne peuvent être accessibles que par l'ordinateur actuel. Lors de l'accès via un nom de domaine, veuillez vous assurer que le nom de domaine a été résolu vers l'entrée de service correcte.

### 2. Sélectionnez le mode Monopuce Tout-en-Un

Sélectionner **Monopuce Tout-en-Un** dans le mode de déploiement ou l'environnement cible (le nom réel affiché sur la page dépend de la version actuelle).

Dans ce mode, le nœud d'installation assume simultanément les rôles de contrôle et de gestion pour ce déploiement, ce qui le rend adapté aux environnements légers pour l'expérience produit, la validation des fonctionnalités et la planification monopuce.

### 3. Configurer le nœud SSH

L'installateur se connecte au nœud cible via SSH et exécute les tâches de déploiement. Veuillez remplir :

- NODE_Adresse IP.
- SSH Utilisateur, généralement `root`.
- SSH Port, généralement `22` par défaut.
- PASSWORD ou informations d'authentification de clé privée.

Après avoir rempli, cliquez sur **Vérifier** pour confirmer que la SSH connexion est réussie.

> SSH Les identifiants ne doivent être utilisés et stockés que dans un environnement contrôlé. Ne pas écrire PASSWORD ou les clés privées dans des documents publics, captures d'écran ou enregistrements de discussion.

### 4. Définir le répertoire des données et autres configurations

Remplissez ou confirmez les configurations suivantes selon les instructions de la page :

| Élément de configuration | Description |
| --- | --- |
| ACCESS_DOMAIN / IP | Adresse pour l'accès des utilisateurs ShimoDocs Suite; lors de l'utilisation d'une IP, remplissez l'adresse réellement accessible. |
| Mode de déploiement | Sélectionnez le mode monopuce Tout-en-Un. |
| Répertoire des données du nœud | Utilisé pour stocker les données du déploiement. Veuillez vous assurer que le disque dispose de suffisamment d'espace et de permissions de lecture/écriture. |
| Dépôt hors ligne | Ce guide est pour l'installation en ligne ; conservez la valeur par défaut sur la page. |
| Middleware tiers | Ce guide utilise le déploiement par défaut ; confirmez selon les exigences de livraison actuelles si un middleware externe est nécessaire. |

S'il n'y a pas d'exigences de configuration particulières, vous pouvez conserver les valeurs par défaut pour le dépôt hors ligne et le middleware tiers. Après vérification, cliquez **Initialiser le déploiement** en bas de la page.

## 6. Confirmer l'aperçu du déploiement

L'aperçu du déploiement est utilisé pour vérifier la configuration de l'installation avant la vérification formelle.

Veuillez prêter une attention particulière aux points suivants :

- Assurez-vous que la version du package de version et le nom du produit sont corrects.
- ACCESS_DOMAIN ou que l'IP est correcte et n'est pas `127.0.0.1`.
- Le mode de déploiement est All-in-One à nœud unique.
- NODE_IP, SSH L'utilisateur et le port sont corrects.
- Le répertoire de données est correct et l'espace disque est suffisant.
- La configuration du dépôt hors ligne et du middleware tiers est conforme à l'environnement actuel.

Après avoir confirmé qu'il n'y a pas d'erreurs, cliquez **Continuer** pour passer au contrôle de l'environnement.

## 7. Effectuer le contrôle de l'environnement

L'installateur vérifiera les nœuds et l'environnement de déploiement. Le processus de vérification peut prendre quelques minutes, veuillez garder la page ouverte.

### 1. Voir l'aperçu du nœud

L'aperçu du nœud affiche la progression des vérifications telles que SSH la connectivité, le système et la performance, le stockage et le disque, le réseau et l'environnement de déploiement.

Pour voir les résultats détaillés d'une vérification spécifique, cliquez sur l'élément de vérification correspondant ou l'entrée de détail.

### 2. Voir les résultats détaillés de la vérification

Les résultats détaillés incluent généralement :

- SSH connectivité et autorisations d'exécution utilisateur.
- Système d'exploitation, CPU Architecture et nombre de cœurs.
- Capacité de mémoire, espace disque et autorisations de répertoire.
- Fuseau horaire et état de la synchronisation de l'heure.
- Réseau, ressources d'image et connectivité aux services externes.
- Résidus environnementaux sur le serveur pouvant affecter le déploiement.

### 3. Comprendre le statut de la vérification

| Statut | Signification | Prochaine action |
| --- | --- | --- |
| Succès | L'élément de vérification actuel répond aux exigences de déploiement | Continuer à attendre la fin des autres éléments |
| Avertissement | Ne bloquera pas directement le déploiement, mais nécessite une confirmation pour savoir si cela correspond au plan actuel | Ouvrir les détails et continuer après avoir confirmé l'impact |
| Échec | Le problème actuel peut affecter l'installation ou le fonctionnement du produit | Corriger le problème d'abord, puis relancer l'analyse |
| En cours | L'installateur effectue la vérification | Attendez la fin de la vérification, ne pas effectuer d'actions répétées |

Si un élément reste en "En cours" pendant longtemps, vous pouvez d'abord attendre que la vérification sur disque ou à distance en cours soit terminée avant de décider de relancer l'analyse.

### 4. Gestion des avertissements et des échecs

Si la page affiche un avertissement :

1. Ouvrir la description détaillée de l'élément de vérification correspondant.
2. Confirmer si l'avertissement correspond au plan de déploiement actuel.
3. En cas de doute, sauvegarder la page et les journaux de l'installateur, puis contacter le personnel de mise en œuvre ou des opérations pour confirmation.

Si la page échoue à s'afficher : 

1. Suivez les instructions pour corriger SSHles problèmes de permissions, ressources, disque, réseau ou middleware. 
2. Cliquez **Rescanner**. 
3. Confirmez que les éléments ayant échoué ont disparu. 

Après avoir vérifié qu'il n'y a pas d'échecs bloquant le déploiement et que tous les avertissements ont été confirmés, cliquez sur **Continuer**. 

## 8. Démarrer l'installation 

### 1. Confirmer le plan d'installation 

La page affichera le plan d'installation et les tâches à exécuter. Après avoir confirmé qu'ils sont corrects, cliquez sur **Démarrer le déploiement**. 

Un message "Confirmer pour démarrer l'installation" peut apparaître sur la page. Une fois commencé, la tâche d'installation se déroulera selon le calendrier ; si vous devez ajuster la configuration, veuillez cliquer sur **Annuler** pour revenir à l'étape précédente.

### 2. Vérifier la progression du déploiement

Après avoir démarré le déploiement, la page affichera le statut actuel de la tâche, les journaux en temps réel et le temps d'exécution. Le déploiement sur un seul nœud prend généralement environ 10 minutes, et le temps réel dépendra des performances du serveur et de la bande passante réseau.

Pendant le processus d'installation, veuillez noter : 

- Ne fermez pas le processus d'installation. 
- Ne redémarrez pas les nœuds d'installation. 
- Ne rafraîchissez pas, ne revenez pas en arrière et ne soumettez pas à nouveau la tâche d'installation. 
- Si la tâche échoue, vérifiez d'abord la première erreur dans le journal de la tâche correspondante, puis traitez-la selon les instructions. 

Lorsque la page affiche **Installation terminée** ou entre dans la **page de Livraison du déploiement** , cela signifie que la tâche d'installation est terminée. 

## 9. Enregistrer les informations de livraison 

La page de fin d'installation affichera les informations d'accès et la vérification d'entrée pour ce déploiement. Veuillez immédiatement effectuer les actions suivantes : 

1. Exécutez la vérification du service post-installation et confirmez les résultats de la vérification. 
2. En utilisant les informations d'accès sur la page de livraison du déploiement, ouvrez la ShimoDocs Suite page commerciale et complétez la vérification de connexion. 
3. Enregistrez l'adresse ShimoDocs Suite adresse d'accès aux affaires et le MDP de la Plateforme des Opérations. 
4. Sauvegardez le compte initial et temporaire PASSWORD, et changez immédiatement le mot de passe initial PASSWORD après la première connexion. 
5. Vérifiez les nœuds du cluster et l'état de l'application dans la MDP Plateforme des Opérations. 

> Les informations de livraison incluent les adresses d'accès et les identifiants initiaux. Ne faites pas de captures d'écran et ne les distribuez pas, ne les téléchargez pas sur des bases de connaissances publiques, et ne les envoyez pas par des canaux non contrôlés. 

## 10. Vérifier les résultats du déploiement 

Après avoir complété l'installation, il est recommandé d'effectuer l'acceptation dans l'ordre suivant : 

### 1. Vérifier les services post-installation 

Effectuez les vérifications post-installation sur la page de fin d'installation pour confirmer que les cas de test du service passent ou que les résultats correspondent aux attentes de l'environnement actuel. 

Si la vérification échoue ou réussit partiellement, vous pouvez soumettre à nouveau une tâche d'inspection dans le MDP Plateforme des Opérations. 

### 2. Vérifier MDP Plateforme d'Opérations

Connectez-vous à la MDP Plateforme des Opérations, allez à **Services Système → Gestion du Cluster**, et confirmez que les nœuds du cluster et l'état d'exécution de l'application sont normaux.

### 3. Vérifier ShimoDocs Suite les fonctions

Connectez-vous à la ShimoDocs Suite page frontend et vérifier au moins les fonctions suivantes :

- Créer un fichier ou une suite de tests.
- Modifier le contenu et enregistrer.
- Exporter les fichiers.
- Importer les fichiers.

Après que toutes les vérifications ci-dessus ont été effectuées avec succès, cela indique que ce déploiement rapide est terminé. Si une exploitation à long terme, une mise à l'échelle ou une haute disponibilité est requise à l'avenir, veuillez passer au plan de déploiement correspondant selon l'échelle réelle et compléter la licence ainsi que la configuration commerciale.

## 11. Questions courantes

### 1. Le navigateur ne peut pas ouvrir la page d'installation

Vérifiez dans l'ordre :

- Si le processus de l'installateur est toujours en cours d'exécution.
- Si l'adresse d'accès utilise l'IP réelle du nœud d'installation ou un nom de domaine résolvable.
- Si le port `18080/TCP` a été ouvert.
- Si le réseau entre l'ordinateur avec le navigateur et le nœud d'installation est connecté.

### 2. Échec de la vérification du package de distribution

Vérifiez :

- Si le fichier téléchargé est un package `.tar.gz` de version complet.
- Si le nom du fichier et le type de produit sont cohérents avec cette livraison.
- Si le package de version correspond au serveur CPU 
- Si le fichier est endommagé lors du téléchargement ou du transfert.

### 3. SSH Échec de l'authentification

Vérifiez :

- Si le NODE_IP et SSH port sont corrects.
- Si le SSH utilisateur, PASSWORD, ou clé privée est correct.
- Est-ce que le SSH utilisateur dispose des permissions requises pour le déploiement ?
- Si le pare-feu ou le groupe de sécurité autorise SSH les connexions.

### 4. Avertissements dans la vérification de l'environnement

Les avertissements n'empêcheront pas directement le déploiement, mais vous devez ouvrir les détails pour confirmer l'impact. S'il concerne les performances du disque, la synchronisation du temps, les résidus de configuration ou les services externes, confirmez d'abord s'il est conforme au plan de déploiement actuel avant de décider de continuer.

### 5. Échecs dans la vérification de l'environnement

Les éléments en échec doivent être corrigés en premier. Ne contournez pas la vérification et ne commencez pas l'installation directement. Après correction, cliquez **Rescanner** pour confirmer que les éléments en échec ont été validés.

### 6. Échec de la tâche d'installation

1. Ouvrez le journal d'exécution de la tâche ayant échoué.
2. Trouvez le premier message d'erreur apparu.
3. Enregistrez le journal de l'installateur, le nom de la tâche ayant échoué et l'heure d'apparition.
4. Après avoir traité les problèmes correspondants de réseau, disque, image, middleware ou autres, Kubernetes continuez selon la méthode de récupération réelle.
