# Déploiement sur un seul nœud Kubernetes Déploiement

[← ShimoDocs Suite documentation de déploiement](../README.md)

## 1. Scénarios applicables
- **K8s déploiement sur nœud unique**:
    - Convient pour de petites équipes légères, une utilisation à petite échelle de moins de 200 personnes, PoC, démonstrations, vérification des fonctionnalités et tests à court terme.
- Un seul serveur est nécessaire, et le serveur sert simultanément de nœud d'installation, K8s de nœud maître et de nœud de travail d'entreprise.
- **Remarque**
    - Pour le lancement officiel, le fonctionnement à long terme ou l'extension de haute disponibilité ultérieure, il est recommandé d'utiliser K8s déploiement de cluster.

## 2. Aperçu du processus de déploiement

| Étape | Que faire | Indicateur de complétion |
| --- | --- | --- |
| 1. Vérifier l'environnement du système | Confirmer les ressources du serveur, le disque, le réseau, la synchronisation de l'heure et les commandes de base | Le serveur répond aux exigences de déploiement |
| 2. Préparer les matériaux d'installation | Obtenez l'installateur et le paquet d'installation du produit ; un environnement hors ligne nécessite également de préparer un paquet image hors ligne | Le nom du fichier correspond à CPU l'architecture |
| 3. Téléchargez les matériels d'installation | Téléchargez l'installateur et le paquet d'installation sur le nœud de déploiement | Les fichiers ont été placés dans le répertoire spécifié sur le serveur |
| 4. Démarrez l'installateur | Lancez la `mdp-installer` page web | La page d'installation est accessible via le navigateur |
| 5. Installation via la page Web | Sélectionnez le paquet de distribution, configurez les nœuds, complétez la vérification de l'environnement et lancez le déploiement | Toutes les tâches d'installation ont réussi |
| 6. Acceptation après l'installation | Vérifiez le cluster, les services, la connexion, la licence et les fonctions métiers | Les fonctions principales peuvent être utilisées normalement |

## 3. Préparation avant le déploiement

### 3.1 Préparer les informations du serveur

| Informations | Exemple | Description |
| --- | --- | --- |
| INSTALL_NODE_IP | `<INSTALL_NODE_IP>` | Déploiement à nœud unique K8s utilise seulement 1 serveur |
| CPU Architecture | `amd64` / `arm64` | L'installateur et le paquet d'installation doivent correspondre à l'architecture du serveur |
| Environnement réseau | En ligne / Hors ligne | Choisissez en ligne si le réseau public est accessible ; choisissez hors ligne pour les environnements internes ou isolés |
| Utilisateur d'exécution | `root` ou un utilisateur avec `sudo` privilèges | L'installateur doit exécuter les tâches de déploiement via SSH |
| SSH Port | `22` | Si le SSH le port a été modifié, remplissez le port réel |
| Protocole d'accès | HTTP / HTTPS | HTTP peut être utilisé pour l'environnement de test ; HTTPS est recommandé pour la production ou l'accès externe |
| ACCESS_DOMAIN | `<ACCESS_DOMAIN>` ou `<INSTALL_NODE_IP>` | L'adresse d'entrée pour que les utilisateurs accèdent ShimoDocs Suite |
| Répertoire de données | `/data` | Il est recommandé d'utiliser un montage de disque de données séparé |

### 3.2 Préparer les matériaux d'installation

| Matériel | Nom de fichier exemple | Description |
| --- | --- | --- |
| Installateur | `mdp-installer-amd64` | Exemple pour `amd64` architecture ; remplacez par le nom de fichier réel pour d'autres architectures |
| Package d'installation du produit | `co1.8.20260807.3639-drive-release..tar.gz` | Pour le déploiement à nœud unique K8s , choisissez le package de distribution dont le nom de fichier ne contient pas `k3s`; le nom du fichier est soumis à la livraison réelle |
| Package d'image hors ligne de base | `smbase_image-amd64.tar.gz` | Nécessaire uniquement pour une installation hors ligne |
| Package d'image hors ligne du produit | `offline_app_image.tar.gz` | Nécessaire uniquement pour l'installation hors ligne, doit correspondre à la version du package d'installation du produit |

Remarque :

- Les noms de fichiers dans les commandes doivent être remplacés par les noms de fichiers réels, tels que `mdp-installer-amd64`, `co1.8.<VERSION>-drive-release.tar.gz`.
- Le package d'installation du produit, le package d'image hors ligne et le serveur CPU l'architecture doit être cohérente.
- Avant l'installation hors ligne, il est recommandé de préparer d'un coup le package d'image hors ligne de base et le package d'image hors ligne du produit pour éviter d'ajouter temporairement des packages pendant le déploiement.

### 3.3 Vérifier les ressources du serveur

| Élément | Exigences recommandées |
| --- | --- |
| Nombre de serveurs | 1 |
| CPU | 16 cœurs ou plus |
| Mémoire | 32 Go ou plus |
| Disque système | Racine `/` partition 100 Go ou plus |
| Disque de données | Monté indépendamment à `/data`, espace disponible supérieur à 300 Go |
| Installation hors ligne | Il est recommandé de réserver en plus plus de 100 Go sur le disque de données pour les packages d'images et les fichiers d'extraction temporaires |

Exécuter sur le serveur : 

```bash
lscpu
free -g
df -h
timedatectl status
```

Confirmer les résultats suivants : 

- CPU, mémoire et disque répondent aux spécifications de déploiement. 
- `/data` a été monté sur un disque de données séparé. 
- La synchronisation de l'heure système est normale. 
- Le serveur peut être accessible via SSH connexion. 
- L'environnement d'installation en ligne peut accéder au réseau public ; l'environnement d'installation hors ligne dispose des packages d'images hors ligne prêts. 

### 3.4 Vérifier les ports 

| Port | Objectif | 
| --- | --- | 
| `22/TCP` | SSH connexion et exécution des tâches d'installation | 
| `18080/TCP` | Page Web de l'installateur | 
| `80/TCP` ou `443/TCP` | ShimoDocs Suite point d'accès | 

Si le serveur a un pare-feu ou un groupe de sécurité activé, veuillez ouvrir les ports ci-dessus à l'avance. 

## 4. Télécharger les outils et packages d'installation 

L'exemple suivant utilise l' `amd64` architecture. Pour d'autres architectures, veuillez remplacer par les noms de fichiers réels. 

### 4.1 Télécharger l'installateur 

Exécuter sur l'ordinateur local : 

```bash
scp mdp-installer-amd64 root@<INSTALL_NODE_IP>:/root/
```

### 4.2 Télécharger le package d'image hors ligne

Cette étape peut être sautée pour l'installation en ligne.

Pour l'installation hors ligne, le package d'image hors ligne doit être téléchargé sur le nœud de déploiement :

```bash
scp smbase_image-amd64.tar.gz offline_app_image.tar.gz root@<INSTALL_NODE_IP>:/root/
```

### 4.3 Connexion au serveur

```bash
ssh root@<INSTALL_NODE_IP>
```

### 4.4 Ajouter la permission d'exécution à l'installateur

```bash
chmod +x /root/mdp-installer-amd64
```

### 4.5 Lancer la page web de l'installateur

Exécutez sur le serveur :

```bash
cd /root
./mdp-installer-amd64 server
```

Si vous souhaitez que l'installateur fonctionne en arrière-plan, vous pouvez utiliser : 

```bash
nohup /root/mdp-installer-amd64 server > /root/mdp-installer.log 2>&1 &
```

Accès via le navigateur : 

```text
http://<INSTALL_NODE_IP>:18080
```

## 5. Installation via la page web

### 5.1 Sélection du package de distribution

Après être entré dans la page web de l'installateur, sélectionnez le package de distribution du produit à déployer cette fois-ci.

Pour K8s Pour un déploiement sur un seul nœud, veuillez choisir le package de distribution dont le nom de fichier ne contient pas `k3s`, par exemple :

```text
co1.8.20260807.3639-drive-release.tar.gz
```

### 5.2 Configuration SSH Connexion

L'installateur se connectera au nœud de déploiement via SSH et exécuter les tâches d'installation. SSH les paramètres prennent en charge deux méthodes d'authentification :

- Authentification par clé privée.
- PASSWORD Authentification.

Il est recommandé d'utiliser le `root` utilisateur ou un utilisateur avec `sudo` privilèges pour effectuer le déploiement. Après avoir rempli les informations, vous pouvez d'abord tester la connexion pour vous assurer que l'installateur peut se connecter normalement au nœud de déploiement.

### 5.3 Confirmer la configuration de base

Après avoir sélectionné le paquet de distribution, passez à l'étape suivante. S'il n'y a pas d'exigences particulières, vous pouvez conserver la configuration par défaut de la page ; si l'environnement de déploiement dispose déjà d'un plan clair pour les noms de domaine, les certificats, les segments réseau ou les middlewares, remplissez en fonction du plan réel.

Points clés à confirmer lors de la configuration : 

- Assurez-vous que le protocole d'accès et ACCESS_DOMAIN sont correctement remplis. 
- Pod CIDR et Service CIDR ne sont pas en conflit avec le réseau existant, le réseau de bureau, VPN, ou IDC segments réseau. 
- Utilisez `/data` ou le répertoire de disque de données prévu pour le répertoire de données. 
- La méthode d'installation en ligne/hors ligne est cohérente avec l'environnement réseau actuel. 

### 5.4 Déploiement initial 

Après la configuration, cliquez sur Déploiement initial. La page affichera un aperçu de ce déploiement. Veuillez vous concentrer sur la vérification : 

- Version du paquet produit. 
- Déploiement NODE_IP. 
- SSH utilisateur et port. 
- ACCESS_DOMAIN et protocole. 
- Répertoire de données. 
- Mode d'installation en ligne ou hors ligne. 
- Sélection du middleware. 

Continuez après avoir confirmé que tout est correct. 

### 5.5 Vérifier l'environnement du système

L'installateur vérifiera automatiquement l'environnement du serveur.

Continuer le déploiement après avoir passé la vérification. Si certains éléments échouent, veuillez les traiter selon les invites de la page et vérifier à nouveau. Les directions de traitement courantes incluent : 

- Espace disque insuffisant : Libérez de l'espace ou étendez le disque de données. 
- Port indisponible : Libérez le port ou ajustez l'utilisation du port. 
- SSH Connexion échouée : Vérifiez le compte, PASSWORD, clé privée, port et groupe de sécurité. 
- Synchronisation temporelle anormale : Configurez NTP ou calibrez l'heure du serveur. 
- Commandes de base manquantes : Installez les commandes manquantes selon la distribution du système. 

### 5.6 Démarrer le déploiement 

Après avoir réussi la vérification de l'environnement, cliquez pour démarrer le déploiement. 

Pendant le processus de déploiement, vous pouvez visualiser les journaux d'exécution de chaque composant. Pendant l'installation, veuillez vous assurer que : 

- Le processus d'installation reste en cours d'exécution. 
- Le navigateur peut se connecter au réseau du nœud d'installation. 
- Ne pas redémarrer le serveur. 
- Ne déplacez pas et ne supprimez pas le package d'installation, le package d'image hors ligne ou le répertoire de données. 

### 5.7 Attendre la fin de l'installation

Le processus d'installation nécessite un certain temps d'attente, et la durée spécifique dépend des performances du serveur, de l'environnement réseau et de la vitesse de téléchargement des images.

Lorsque la page montre que toutes les tâches ont été exécutées avec succès et qu'il n'y a aucun composant échoué, cela indique que le déploiement est terminé.

### 5.8 Confirmation du résultat de l'installation

Après la fin de l'installation, l'installateur affichera une page de fin de déploiement et les informations d'accès. Veuillez d'abord vérifier qu'aucune tâche n'a échoué sur la page avant de continuer à accéder au système métier et MDP Plateforme d'Opérations.

Visitez l'adresse métier : 

```text
http://<ACCESS_DOMAIN>/
```

Si HTTPS a été configuré pendant l'installation, veuillez visiter : 

```text
https://<ACCESS_DOMAIN>/
```

Après vous être connecté avec le compte par défaut ou le compte administrateur, veuillez changer immédiatement PASSWORD le mot de passe initial.

Accéder à MDP Plateforme d'opérations : 

```text
http://<ACCESS_DOMAIN>/mdp/
```

Si vous devez modifier le MDP administrateur PASSWORD, vous pouvez exécuter la commande suivante sur le nœud de déploiement pour modifier ou réinitialiser le PASSWORD.
Veuillez remplacer {password} par un nouveau mot de passe complexe et sécurisé PASSWORD conformément aux exigences réelles de sécurité.

```bash
kubectl exec -it $(kubectl get pods -l app=mdp -o jsonpath='{.items[0].metadata.name}') -- reset-admin-password {password}
```

## 6. Acceptation post-installation

### 6.1 Vérification K8s Statut du nœud

Exécutez sur le nœud de déploiement :

```bash
kubectl get node
```

Le statut du nœud devrait être `Ready`. 

Continuez à vérifier le service : 

```bash
kubectl get pod -A
```

Les états normaux sont habituellement : 

- `Running`: Le service est en cours d'exécution. 
- `Completed`: La tâche a terminé son exécution. 

Si vous rencontrez des états tels que `CrashLoopBackOff`, `ImagePullBackOff`, `Error`, `Pending`, veuillez d'abord vérifier les journaux du Pod correspondant et les traiter. 

### 6.2 Vérifier l'entrée d'accès 

Accéder à ShimoDocs Suite entrée via le navigateur : 

```text
http://<ACCESS_DOMAIN>/
```

Si HTTPS est configuré, veuillez visiter : 

```text
https://<ACCESS_DOMAIN>/
```

Confirmez que la page de connexion peut s'ouvrir normalement.

### 6.3 Vérifier le back-office de gestion et la licence

Confirmez les éléments suivants :

- Le back-office admin est accessible.
- L'administrateur peut se connecter.
- La page de licence peut être ouverte.
- Les informations sur la machine peuvent être consultées.
- La licence peut être demandée ou mise à jour selon le processus d'autorisation.

### 6.4 Vérifier les fonctions commerciales

Après s'être connecté avec un compte test ou un compte créé par un administrateur, au minimum vérifier :

- Vous pouvez créer des documents, des feuilles de calcul et des présentations.
- Le document peut être modifié et enregistré, et le contenu reste présent après actualisation.
- La collaboration multi-utilisateurs est disponible.
- L'importation et l'exportation de fichiers sont normales.
- Les fonctionnalités principales telles que la recherche, les espaces d'équipe et les listes de contacts sont disponibles.

Après la première connexion avec le compte test par défaut, veuillez changer le PASSWORD le mot de passe initial.
Compte PASSWORD est le compte de livraison de déploiement PASSWORD!
```text
ACCOUNT:autotest@example.com
PASSWORD:xxxxx
```

### 6.5 Arrêter le processus d'installation

Après que le déploiement est terminé et que l'acceptation est passée, le service Web de l'installateur peut être arrêté :
Arrêter la page Web d'installation :
Commande pour arrêter l'installateur : 
```bash
ps -ef | grep mdp-installer | grep -v grep
kill <PID>
```

Si l'installateur est lancé en arrière-plan en utilisant `nohup`, vous pouvez également vérifier les journaux : 

```bash
tail -f /root/nohup.out
```

## 7. Dépannage courant

### 7.1 Le navigateur ne peut pas ouvrir la page de l'installateur

Vérifiez ce qui suit :

- Si le processus de l'installateur est toujours en cours d'exécution.
- Si le port `18080` est bloqué par un pare-feu ou un groupe de sécurité.
- Si l'IP accédée par le navigateur est INSTALL_NODE_IP.

Vous pouvez exécuter ce qui suit sur le serveur :

```bash
ps -ef | grep mdp-installer | grep -v grep
ss -lntp | grep 18080
```

### 7.2 Échec de la vérification de l'environnement

Traitez chaque élément selon les indications sur la page. Après traitement, retournez à la page de l'installateur et relancez la vérification de l'environnement.

Vérifications prioritaires :

- Si le CPU, la mémoire et le disque répondent aux exigences.
- Si `/data` est un disque de données indépendant.
- Si l'heure du serveur est synchronisée.
- Si le SSH L'utilisateur a les permissions de déploiement.

### 7.3 Échec de la récupération de l'image d'installation hors ligne

Vérifiez les indications :

- Si le paquet d'image hors ligne a été téléchargé sur le nœud de déploiement.
- Si le paquet d'image hors ligne de base et le paquet d'image hors ligne du produit sont complets.
- Si la version du paquet d'image correspond au paquet d'installation du produit.
- Si l'adresse du registre d'image privé, le compte et PASSWORD sont correctement remplis.

### 7.4 Le pod reste dans un état anormal pendant longtemps

Tout d'abord, vérifiez le Pod anormal :

```bash
kubectl get pod -A
```

Vérifiez à nouveau les journaux : 

```bash
kubectl logs -n <namespace> <pod-name>
```

Traitez les problèmes liés aux images, configurations, ressources ou dépendances en fonction des journaux.

## 8. Conserver le matériel après l'installation

Après le déploiement, il est recommandé de conserver les matériaux suivants pour faciliter la maintenance, les mises à niveau et le dépannage ultérieurs :

- INSTALL_NODE_IP, ACCESS_DOMAIN, et protocole d'accès.
- Nom et version du fichier d'installation.
- Nom et version du fichier du package d'installation du produit.
- Nom et version du fichier du package d'image hors ligne.
- Captures d'écran de la configuration des clés de la page Web.
- `kubectl get node` vérifier les résultats.
- `kubectl get pod -A` vérifier les résultats.
- Enregistrements d'autorisation de licence.
- Enregistrements d'acceptation des fonctions commerciales.
- Problèmes rencontrés lors du déploiement et leurs résultats de traitement.
