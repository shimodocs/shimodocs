# Haute disponibilité Kubernetes Déploiement

[← ShimoDocs Suite documentation de déploiement](../README.md)

## 1. Scénarios applicables 

> [!TIP] 
> 
> K8s Le déploiement en cluster convient aux environnements de production. Comparé au déploiement sur une seule machine, le déploiement en cluster est plus adapté pour le fonctionnement à long terme, la montée en charge et les scénarios de haute disponibilité. 

- Pour les environnements de production, il est recommandé d'utiliser `3 master   N worker`. 
- Préparer au minimum 3 serveurs, tous les 3 comme maîtres. Les travailleurs peuvent initialement réutiliser les nœuds maîtres et augmenter ensuite les travailleurs selon la montée en charge. 

## 2. Préparations avant le déploiement 

### 2.1 Préparer les informations suivantes 

| Informations | Exemple | Description | 
| --- | --- | --- | 
| Environnement réseau | En ligne / Hors ligne | Choisir En ligne si l'accès au réseau public est supporté ; choisir Hors ligne pour les environnements de réseau interne ou déconnectés | 
| INSTALL_NODE_IP | `<INSTALL_NODE_IP>` | Sélectionner 1 machine comme nœud d'installation pour démarrer la page web | 
| Affaires NODE_IP | `<Node1IP>`, `<Node2IP>`, `<Node3IP>` | Au moins 3 serveurs | 
| Utilisateur pour l'exécution | `root` | Les commandes d'installation doivent être exécutées avec `root` | 
| Protocole d'accès | HTTP / HTTPS | HTTPS est recommandé pour les environnements de production | 
| ACCESS_DOMAIN | `<ACCESS_DOMAIN>` | L'adresse pour les utilisateurs pour accéder ShimoDocs Suite |
| Répertoire de données | `/data` | Il est recommandé de garder la cohérence sur tous les nœuds |
| Outil d'installation | `mdp-installer-${Arch}` | Installateur fourni par ShimoDocs, `${Arch}` distingue différentes architectures de puces, sa valeur peut être amd64 pour l'architecture x86 ou arm64 pour l'architecture arm |
| Package d'installation du produit | ShimoDocs Suite package d'installation | Utiliser le nom de fichier réellement livré |
| Package d'image hors ligne | `*.tar.gz` | Nécessaire uniquement pour une installation hors ligne |
| Middleware externe | Oui / Non | S'il y a un middleware externe, préparer l'adresse, le port, le compte, PASSWORD à l'avance |

### 2.2 Exigences minimales du serveur

| Élément | Exigence |
| --- | --- |
| Nombre de serveurs | 3 ou plus |
| Rôles recommandés | `3 master   N worker` |
| CPU par nœud | 16 cœurs ou plus |
| Mémoire par nœud | 32 Go ou plus |
| Disque système | Racine `/` partition 100 Go ou plus |
| Disque de données | Montage séparé `/data`, espace disponible de 300 Go ou plus |
| Installation hors ligne | Il est recommandé de réserver au moins 100 Go supplémentaires sur le disque de données du nœud d'installation |

Remarque :

- Ne pas partitionner `/root`, `/var`, ou `/tmp` séparément. 
- Ne mettez pas de données sur le disque système ; mettez-les toutes dans `/data`. 
- L'heure sur tous les nœuds doit être synchronisée. 
- Les nœuds d'installation doivent pouvoir accéder aux autres nœuds via SSH. 

Peut être exécuté sur chaque serveur : 

```bash
lscpu
free -g
df -h
timedatectl status
```

Confirmer que les autres nœuds sont accessibles depuis le nœud d'installation : 

```bash
ssh root@<NODE2IP>
ssh root@<NODE3IP>
```

Si la connexion échoue, vérifier d'abord SSH, PASSWORD, les paramètres de pare-feu ou de groupe de sécurité avant de continuer l'installation.

## 3. Télécharger l'outil d'installation et le package d'installation
> [!TIP]
>
> - Assurez-vous de modifier les noms de fichiers dans les commandes en fonction de la situation réelle. Par exemple, le nom du package d'installation dans un environnement à architecture x86 est mdp-installer-amd64.
> - Choisissez la méthode de téléchargement appropriée en fonction du scénario réel. Cet article utilise la ligne de commande scp comme exemple, mais d'autres outils graphiques peuvent également être utilisés pour le téléchargement. SSH 

Exécutez la commande suivante sur votre ordinateur local pour transférer l'installateur vers le nœud d'installation :

```bash
scp mdp-installer-amd64 root@<INSTALL_NODE_IP>:/root/
```

L'installation hors ligne nécessite toujours le téléchargement du package image hors ligne : 

```bash
scp smbase_image-amd64.tar.gz offline_app_image.tar.gz root@<INSTALL_NODE_IP>:/root/
```

Connectez-vous au nœud d'installation : 

```bash
ssh root@<INSTALL_NODE_IP>
```

Donnez les permissions d'exécution à l'installateur :

```bash
chmod +x /root/mdp-installer-amd64
```

Lancez la page web de l'installateur : 

```bash
nohup /root/mdp-installer-amd64 server --port 18080 &
```

Accès via le navigateur : 

```text
http://<INSTALL_NODE_IP>:18080
```

## 4. Installation via la page Web

### 4.1 Téléchargement du package d'installation du produit

1. Ouvrez `http://<INSTALL_NODE_IP>:18080`.
2. Téléchargez le ShimoDocs Suite package d'installation.
3. Après la fin du téléchargement, cliquez sur `Continue`.

### 4.2 Configuration ACCESS_DOMAIN

Saisissez l'adresse ShimoDocs Suite d'accès :

| Élément de configuration | Comment remplir |
| --- | --- |
| ACCESS_DOMAIN / IP | `<ACCESS_DOMAIN>` |

### 4.3 Confirmer la configuration de base

| Élément de configuration | Comment remplir |
| --- | --- |
| NODE_IP | Remplissez maître / travailleur NODE_IP un par un |
| SSH Port | Habituellement `22` |
| SSH PASSWORD | `root` utilisateur PASSWORD |
| Type de nœud | `master`, `worker`, nœud d'installation |
| Répertoire de données | `/data` |

Étapes d'exploitation :

1. Ajouter INSTALL_NODE_IP.
2. Ajoutez les adresses IP de chaque nœud maître/travailleur.
3. Attribuez les rôles de nœud à chaque serveur.
4. Testez la connectivité du nœud d'installation vers chaque nœud.
5. Remplissez le catalogue de données et le segment du réseau de conteneurs.

Points clés à confirmer pendant la configuration :

- Le protocole d'accès et ACCESS_DOMAIN sont correctement remplis.
- Pod CIDR et Service CIDR ne sont pas en conflit avec le réseau existant, le réseau de bureau, VPN, ou IDC les segments de réseau.
- Le répertoire des données utilise `/data` ou le répertoire du véritable disque de données prévu.
- La méthode d'installation en ligne/hors ligne est conforme à l'environnement réseau actuel.
- L'installation hors ligne nécessite le téléchargement du package d'image de base hors ligne et du package d'image de l'application. Par défaut, il s'agit d'une installation en ligne et il est nécessaire de s'assurer que le cluster a accès au réseau public.

### 4.4 Déploiement Initial

Après la configuration, cliquez sur Initialiser le Déploiement. La page affichera un aperçu de ce déploiement ; veuillez prêter une attention particulière à :

- Version du package produit.
- Déployer NODE_IP.
- SSH utilisateur et port.
- ACCESS_DOMAIN et protocole.
- Répertoire des données.
- Mode d'installation en ligne ou hors ligne.
- Sélection du middleware.

Continuez après avoir confirmé qu'il n'y a pas d'erreurs.

### 4.5 Vérifier l'Environnement Système

L'installateur vérifiera automatiquement l'environnement du serveur.

Poursuivez le déploiement après le passage de l'inspection. En cas d'échec, veuillez suivre les instructions sur la page pour y remédier, puis réinspecter. Les directions courantes de traitement comprennent :

- Espace disque insuffisant : libérez de l'espace ou étendez le disque de données.
- Port indisponible : libérez le port ou ajustez l'utilisation des ports.
- SSH Connexion échouée : Vérifiez le compte, PASSWORD, clé privée, port et groupe de sécurité.
- Exception de synchronisation de l'heure : configurez NTP ou calibrez l'heure du serveur.
- Commandes de base manquantes : Installez les commandes manquantes selon la distribution du système.

### 4.6 Commencer le déploiement

Après que la vérification de l'environnement est passée, cliquez sur Démarrer le déploiement.

Vous pouvez consulter les journaux d'exécution de chaque composant pendant le processus de déploiement. Pendant l'installation, veuillez vous assurer que : 

- Le processus d'installation reste en cours d'exécution. 
- Le navigateur peut communiquer avec le nœud d'installation via le réseau. 
- Le serveur n'est pas redémarré. 
- Ne déplacez pas et ne supprimez pas le package d'installation, le package d'image hors ligne ou le répertoire de données. 

### 4.7 Attendre la fin de l'installation

Le processus d'installation nécessite un certain temps d'attente, et le temps exact dépend des performances du serveur, de l'environnement réseau et de la vitesse de téléchargement de l'image.

Lorsque la page montre que toutes les tâches ont été exécutées avec succès et qu'il n'y a aucun composant échoué, cela indique que le déploiement est terminé.

### 4.8 Confirmer le résultat de l'installation

Une fois l'installation terminée, l'installateur affichera la page de fin de déploiement et les informations d'accès. Veuillez d'abord confirmer qu'il n'y a pas de tâches échouées sur la page avant de continuer à accéder au système métier et à la MDP Plateforme d'Opérations.

Visitez l'adresse métier : 

```text
http://<ACCESS_DOMAIN>/
```

Si HTTPS a été configuré pendant l'installation, veuillez visiter : 

```text
https://<ACCESS_DOMAIN>/
```

Après vous être connecté avec le compte par défaut ou le compte administrateur, veuillez changer immédiatement PASSWORD le mot de passe initial.

Accéder à MDP Plateforme d'Opérations :

```text
http://<ACCESS_DOMAIN>/mdp/
```

Si vous devez modifier le MDP administrateur PASSWORD, vous pouvez exécuter la commande suivante sur le nœud de déploiement pour modifier ou réinitialiser le PASSWORD.
Veuillez remplacer {password} par un nouveau mot de passe complexe et sécurisé PASSWORD conformément aux exigences réelles de sécurité.

```bash
kubectl exec -it $(kubectl get pods -l app=mdp -o jsonpath='{.items[0].metadata.name}') -- reset-admin-password {password}
```

## 5. Acceptation post-installation

### 5.1 Vérification K8s Statut du nœud

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

### 5.2 Vérification de l'accès 

Accéder à ShimoDocs Suite entrée via le navigateur : 

```text
http://<ACCESS_DOMAIN>/
```

Si HTTPS est configuré, veuillez visiter : 

```text
https://<ACCESS_DOMAIN>/
```

Confirmez que la page de connexion peut s'ouvrir normalement. 

### 5.3 Vérification du Backend de Gestion et de la Licence 

Confirmez les éléments suivants : 

- Le backend de gestion est accessible. 
- Les administrateurs peuvent se connecter. 
- La page de Licence peut être ouverte. 
- Les informations sur la machine peuvent être consultées. 
- La licence peut être demandée ou mise à jour selon le processus d'autorisation. 

### 5.4 Vérification des Fonctions Métiers 

Après connexion avec un compte test ou un compte créé par un administrateur, vérifiez au moins : 

- Peut créer des documents, des feuilles de calcul, des diaporamas. 
- Les documents peuvent être édités, enregistrés ou actualisés, et le contenu reste présent. 
- La collaboration multi-utilisateur est disponible. 
- L'importation et l'exportation de fichiers sont normales. 
- Les fonctions principales telles que la recherche, l'espace d'équipe, les contacts, etc. sont disponibles. 

Après la première connexion avec le compte test par défaut, veuillez mettre à jour votre PASSWORD immédiatement. 
Compte PASSWORD est le compte de déploiement et de livraison PASSWORD! 

```text
ACCOUNT:autotest@example.com
PASSWORD:xxxxxxx
```

### 5.5 Arrêter le processus de l'installateur

Après que le déploiement est terminé et accepté, le service Web de l'installateur peut être arrêté
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

## 6. Gestion des problèmes courants

### 6.1 Le navigateur ne peut pas ouvrir la page de l'installateur

Vérifiez ce qui suit :

- Si le processus de l'installateur est toujours en cours d'exécution.
- Si le port `18080` est bloqué par un pare-feu ou un groupe de sécurité.
- Si l'IP accédée par le navigateur est INSTALL_NODE_IP.

Vous pouvez exécuter sur le serveur :

```bash
ps -ef | grep mdp-installer | grep -v grep
ss -lntp | grep 18080
```

### 6.2 Échec de la vérification de l'environnement

Traitez chaque élément selon les indications sur la page. Après traitement, retournez à la page de l'installateur et relancez la vérification de l'environnement.

Vérifications prioritaires :

- Si le CPU, la mémoire et le disque répondent aux exigences.
- Si `/data` est un disque de données indépendant.
- Si l'heure du serveur est synchronisée.
- Si le SSH L'utilisateur a les permissions de déploiement.

### 6.3 Échec du téléchargement de l'image pour l'installation hors ligne

Vérifiez les indications :

- Si le paquet d'image hors ligne a été téléchargé sur le nœud de déploiement.
- Si le paquet d'image hors ligne de base et le paquet d'image hors ligne du produit sont complets.
- Si la version du paquet d'image correspond au paquet d'installation du produit.
- Si l'adresse du registre d'image privé, le compte et PASSWORD sont correctement remplis.

### 6.4 Le Pod reste dans un état anormal pendant longtemps

Tout d'abord, vérifiez le Pod anormal :

```bash
kubectl get pod -A
```

Vérifiez à nouveau les journaux : 

```bash
kubectl logs -n <namespace> <pod-name>
```

Traitez les problèmes liés aux images, configurations, ressources ou dépendances en fonction des journaux.

## 7. Matériaux de fixation après installation

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
