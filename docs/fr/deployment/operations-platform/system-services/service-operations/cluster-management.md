# Gestion du cluster

[← ShimoDocs Suite documentation de déploiement](../../../README.md)

## 1. Aperçu de la fonction

Le module de gestion de cluster est une console dans le MDP Plateforme d'opérations qui interfère avec le client Kubernetes clusters, ciblant trois scénarios : inspections quotidiennes, dépannage d'urgence et changements de ressources. L'objectif de ce module est de permettre au personnel opérationnel en service de réaliser des tâches courantes de dépannage et d'exploitation sans avoir à passer fréquemment à l'interface native `kubectl`.

Principales capacités :

- Aperçu du cluster : état des nœuds, statut des applications en cours d'exécution
- Gestion de la charge de travail : visualisation, redémarrage, modification du nombre de réplicas, modification des ressources du conteneur et visualisation YAML pour Deployment, StatefulSet, DaemonSet, Pod, Job et CronJob
- Gestion de la configuration : consulter ConfigMap, HorizontalPodAutoscaler (HPA)
- Ressources réseau : consulter Service, Ingress
- Diagnostic au niveau du Pod : journaux en temps réel, journaux de crash, K8s événements, terminal web, YAML vue

### 1.1 Utilisateurs concernés

| Rôle        | Opérations courantes                                      |
| ----------- | ---------------------------------------------------- |
| Opérations en service | Visualisation des anomalies de nœuds et de Pods, interrogation des journaux, visualisation des événements |
| Support sur site | Voir l'état des répliques de déploiement, la version de l'image, les demandes/limites de ressources         |
| Urgence de panne | Redémarrer le déploiement ou le DaemonSet, ajuster le nombre de répliques, ajuster CPU/Mémoire |
| Planification de capacité | Voir HPA nombre actuel de répliques et limites supérieures/inférieures                             |

### 1.2 Opérations non recommandées dans ce module

Suppression NAMESPACE, expulsion forcée de Pods, modification de Secret ou RBAC ressources, et autres opérations sensibles ne sont pas disponibles dans ce module et doivent être effectuées via les outils natifs `kubectl` ou outils de changement pertinents. Les opérations par lots inter-clusters ne sont pas disponibles ; chaque opération n'affecte que le cluster actuellement sélectionné et NAMESPACE. Pour télécharger des journaux de fichiers volumineux en une seule fois, il est recommandé d'utiliser le terminal Web au lieu d'une fenêtre contextuelle de flux de journaux.

---

## 2. Entrée et navigation

Menu de gauche : **Gestion des opérations → Gestion de cluster**.

Après être entré, le **menu Déploiements** à gauche est sélectionné par défaut. Le NAMESPACE par défaut sur le premier du cluster actuel, et la sélection personnalisée du cluster et NAMESPACE est prise en charge.

---

## 3. Charges de travail

### 3.1 Déploiements
**Étapes**: Trouver le Déploiement cible → Cliquer sur l'icône du crayon en haut à droite → La fenêtre contextuelle d'édition apparaît → Saisir les nouvelles valeurs → Confirmer les modifications.

Champs pris en charge pour la modification dans la fenêtre contextuelle : 

- Nombre de répliques, valeur minimale 0, doit être un entier 
- CPU Requête / Limite par conteneur, unité "core", peut être remplie `1` ou `1000m` 
- Requête / Limite de mémoire par conteneur, unité Mi, peut être remplie `512` 

Après soumission, une reconstruction progressive est déclenchée. Les champs non listés (image, env, sondes, etc.) ne seront pas modifiés. 

#### 3.1.1 Redémarrage du Déploiement 
Étapes : Trouver le Déploiement cible → Cliquer sur l'icône de flèche circulaire en haut à droite → Confirmer que la fenêtre contextuelle apparaît → Vérifier le cluster / NAMESPACE / nom de charge → Confirmer le redémarrage. 

La fenêtre de confirmation indique clairement que "le redémarrage entraînera la reconstruction des Pods et les services peuvent être brièvement interrompus." Le redémarrage reconstruira les Pods sur tous les nœuds simultanément. 

### 3.2 Pods
**Étapes opérationnelles**: Aller aux Pods depuis le menu de gauche → La section inférieure liste tous les Pods sous le NAMESPACEactuel, prise en charge de la recherche par Namespace, POD_NAME, IP du Pod, et NODE_IP. 

Cela YAML est uniquement pour consultation.

### 3.3 Jobs et CronJobs

#### Jobs
**Étapes**: Allez dans Jobs depuis le menu de gauche → Le tableau répertorie tous les Jobs dans le courant NAMESPACE.

Peut être recherché par Espaces de noms et Nom.

#### CronJobs
**Étapes**: Allez dans CronJobs depuis le menu de gauche → Le tableau répertorie tous les CronJobs sous le courant NAMESPACE.

Peut être recherché par Espaces de noms et Nom. 
Cliquez **** **** pour développer et afficher le sous-tableau des Pods correspondants à tous les Jobs déclenchés par ce CronJob. 

### 3.4 DaemonSets 
**Étapes opérationnelles**: Entrez dans DaemonSets depuis le menu de gauche. 

Peut être recherché par Espaces de noms et nom de charge de travail.
Opérations prises en charge :

- Modifier : CPU / La mémoire peut être modifiée, le nombre de répliques ne peut pas être changé.
- Redémarrer : Reconstruire les Pods sur tous les nœuds simultanément.
- YAML: Lecture seule.

### 3.5 StatefulSets
**Étapes opérationnelles**: Allez dans StatefulSets depuis le menu de gauche → Vue tableau.

La modification du nombre de répliques, CPU/Mémoire, des redémarrages ou des listes de Pods des StatefulSets n’est pas prise en charge. Les modifications requises doivent être effectuées en utilisant le natif `kubectl` (voir Annexe B).

---

## 4. Configuration

### 4.1 ConfigMaps
**Étapes**: Entrez dans ConfigMaps depuis le menu de gauche → le tableau répertorie tous les ConfigMaps dans le courant NAMESPACE.
[Gestion du cluster] ne prend pas en charge l’édition de paires clé-valeur. Pour les modifications, veuillez aller au Centre de configuration.

### 4.2 HPA
**Étapes opérationnelles**: Entrez HPA depuis le menu de gauche → Le tableau répertorie tous les HPAs sous le courant NAMESPACE.

Lecture seule. Pour modifier le HPA min et max, veuillez utiliser le natif `kubectl`.

---

## 5. Réseau

### 5.1 Services
**Étapes**: Utilisez le menu de gauche pour aller dans Services → un tableau répertorie tous les Services sous le courant NAMESPACE.

Lecture seule. Pour effectuer des modifications, veuillez les modifier via la configuration globale mdp. 

### 5.2 Ingresses
**Étapes**: Accédez à Ingresses depuis le menu de gauche → Le tableau répertorie tous les Ingresses sous la vue actuelle NAMESPACE. 

Vue seule, pour apporter des modifications, veuillez les effectuer via la configuration globale mdp.

---

## 6. Opérations courantes

### 6.1 Dépannage des problèmes de Pod

1. Utilisez le menu déroulant en haut pour passer au cluster correspondant et NAMESPACE
2. Accédez à Pods dans le menu de gauche
3. Filtrer par POD_NAME ou IP
4. Faites attention au champ Phase en haut de la carte, priorisez `Failed` et `Pending`
5. La Condition correspondant à l'indicateur de santé grisé est le point problématique
6. Cliquez sur l'icône "Événements" à la fin de la ligne pour trouver la cause principale
7. Utilisez "Logs" pour voir la sortie en temps réel / "Logs de crash" pour voir la dernière sortie du conteneur

### 6.2 Redémarrer le déploiement

1. Accédez à Déploiements dans le menu de gauche
2. Trouvez le déploiement cible
3. Cliquez sur l'icône de flèche circulaire en haut à droite
4. Confirmez la fenêtre contextuelle en vérifiant le cluster / NAMESPACE / nom de la charge de travail → confirmer le redémarrage
5. Observez la barre de progression de l'état des réplicas de Pod en bas de la carte pour juger de la progression de la reconstruction

### 6.3 Réduire les réplicas de déploiement pour vérification

1. Entrez dans le déploiement correspondant
2. Cliquez sur l'icône crayon "Modifier"
3. Entrez la nouvelle valeur pour le nombre de réplicas (peut être réglé sur 0 pour le débogage) 
4. Ajuster CPU / Mémoire selon les besoins (optionnel) 
5. Confirmez les modifications et attendez la mise à jour progressive 

Avant de réduire le nombre de réplicas, il est recommandé de confirmer avec SRE vos collègues si la valeur cible affectera le trafic en ligne. 

### 6.4 Modifier ConfigMap 

La plateforme ne prend pas en charge la modification des paires clé-valeur ConfigMap dans Gestion de cluster - Configuration - ConfigMap. Veuillez vous rendre dans le Centre de configuration. 

--- 

## 9. Questions Fréquemment Posées 

**Q1 : L'aperçu général montre que le taux de fonctionnement de l'application n'est pas de 100 %.** 

Cela signifie qu'il y a des Pods sous l'actuel NAMESPACE qui ne sont pas en état Prêt (y compris En attente, CrashLoopBackOff, Erreur, etc.). Veuillez aller dans le menu Pods à gauche, filtrer par POD_NAME ou IP, et vérifier les événements et les journaux de chaque Pod non prêt. 

**Q2 : Le popup est vide après avoir cliqué sur 'Modifier le déploiement'.** 

Il y a trois raisons courantes : la gigue du réseau, trop de `managedFields` dans l'objet ressource, ou serveur API exceptions. Veuillez désactiver d'abord la réessai ; si c'est toujours vide, contactez SRE et fournir le nom du cluster / namespace / workload pour le dépannage. 

**Q3 : Le YAML le contenu de la fenêtre contextuelle est très volumineux.** 

Phénomène normal. K8s Les objets de ressources contiennent par défaut beaucoup de métadonnées et de conditions, avec le contenu clé concentré dans la `spec` section. 

**Q4 : Pas de sortie dans la fenêtre contextuelle du journal.** 

Le conteneur peut ne pas produire de journaux vers stdout/stderr, veuillez vérifier la politique de sortie des journaux de l'application. Si le conteneur a crashé, utilisez l'icône "Crash Log" pour récupérer la sortie de l'instance précédente. 

**Q5 : La modification du nombre de répliques ou des ressources n'a pas pris effet.** 

La plateforme délivre un Strategic Merge Patch, et K8s entrera dans le processus de réconciliation en quelques secondes. S'il n'y a aucun changement dans les 30 secondes, veuillez revenir à la `kubectl describe deployment` pour vérifier les événements. 

**Q6 : Impossible de modifier les StatefulSets, ConfigMaps, HPA, Services, Ingress.** 

La plateforme ne permet que la visualisation de ces ressources. Les modifications doivent être effectuées via la configuration globale mdp, et seuls les Services et Ingress sont pris en charge. 

--- 

--- 

## Annexe A : Clé kubectl commandes utilisées sur cette plateforme 

Les commandes suivantes sont utilisées pour s'exécuter directement sur l'hôte ou le terminal de maintenance comme chemin alternatif lorsque les fonctions de ce module ne sont pas couvertes. 

```bash
# View
kubectl get  statefulset <name> -n <ns>
kubectl get deployment <name> -n <ns>

# Restart STS / deployment
kubectl rollout restart statefulset/<name> -n <ns>
kubectl rollout restart deployment/<name> -n <ns>

# View the complete Ingress rule chain
kubectl describe ingress <name> -n <ns>
```

`kubectl describe deployment <name> -n <ns>` peuvent être utilisées pour dépanner l'avancement de la réconciliation émise par la plateforme après modification.

Précautions :
La modification des ressources gérées par MDP telles que deployment, configmap, ingress, sts, etc., via kubectl doit être évitée. La manière correcte d'opérer est d'utiliser la MDP configuration du backend.

## Annexe B : Glossaire des termes

| Terme               | Explication                                           |
| --------------- | ---------------------------------------------------- |
| Cluster         | Cible K8s cluster, configuré et émis lorsque MDP démarre                              |
| Namespace       | K8s NAMESPACE, utilisé pour l'isolation des activités ou de l'environnement                                   |
| Workload        | Workload, se réfère généralement à Deployment, StatefulSet, DaemonSet, Job, CronJob |
| Pod             | La plus petite unité de planification dans K8s, portant de 1 à N conteneurs                              |
| HPA             | HorizontalPodAutoscaler, mise à l'échelle horizontale basée sur les métriques                  |
| Request / Limit | Réservation / limite de ressources du conteneur, la plateforme supporte la modification des deux |
| Patch           | Mise à jour partielle, cette plateforme utilise Strategic Merge Patch                     |
| STS             | Abréviation de StatefulSet                                       |
| DS              | Abréviation de DaemonSet                                         |
