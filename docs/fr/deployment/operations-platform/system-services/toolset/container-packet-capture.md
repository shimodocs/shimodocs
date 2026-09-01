# Capture de paquets du conteneur

[← ShimoDocs Suite documentation de déploiement](../../../README.md)

## Aperçu des fonctionnalités 

La capture de paquets de conteneur est utilisée pour collecter des données réseau à partir des Pods en cours d’exécution dans un Kubernetes environnement, vous aidant à analyser des problèmes tels que les échecs de connexion, les délais d’attente des requêtes, TCP les retransmissions et la congestion du réseau. 

Une fois la capture terminée, vous pouvez télécharger le PCAP fichier et l’inspecter davantage à l’aide d’outils d’analyse de réseau comme Wireshark. 

## Accéder à la page 

Après vous être connecté à la console de gestion, sélectionnez **Capture de paquets du conteneur** dans la navigation de gauche pour entrer sur la page. 

La capture de paquets de conteneur n’est applicable que dans les Kubernetes environnements de déploiement et n’est disponible que pour les administrateurs. 

## Démarrage d’une capture de paquets 

Il est recommandé de suivre ces étapes : 

1. Recherchez et localisez le Pod cible dans la **Liste des Pods**. 
2. Assurez-vous que le statut du Pod est En cours d’exécution, puis cliquez sur **Démarrer la capture**. 
3. Sélectionnez la durée de capture : 1 minute, 5 minutes ou 30 minutes. 
4. Sélectionnez la taille du fichier de capture : 100 Mo, 500 Mo ou 1 Go. 
5. Choisissez les conditions de filtrage si nécessaire, ou saisissez manuellement une expression de filtre tcpdump. 
6. Vérifiez la commande complète de capture affichée sur la page. 
7. Cliquez **Démarrer la capture** pour créer la tâche. 

Une seule tâche de capture de paquets peut s'exécuter sur le même Pod en même temps. La tâche se terminera automatiquement lorsqu'elle atteindra la durée définie, ou elle peut être arrêtée manuellement. 

## Conditions de filtre 

Définir des conditions de filtrage peut réduire le trafic non pertinent et la taille du fichier. La page propose certains préréglages couramment utilisés, tels que : 

- Trafic sur les ports spécifiés. 
- gRPC trafic. 
- Hôte et port spécifiés. 
- HTTP POST requêtes. 
- TCP établissement de connexion, retransmission ou paquets à petite fenêtre. 

Vous pouvez également saisir manuellement en utilisant la syntaxe tcpdump, par exemple : 

```text
host 10.0.0.1 and port 80
```

Si les conditions de filtrage ne sont pas spécifiées, la tâche peut collecter une grande quantité de trafic réseau du Pod.

## Gérer les tâches de capture de paquets

Sur la page **Tâches de capture de paquets** sur cette page, vous pouvez voir l'ID de la tâche, le Pod, le statut, l'heure de création et le temps d'exécution.

- **En cours d'exécution**: La tâche peut être arrêtée manuellement.
- **Terminé**: Le PCAP le fichier peut être téléchargé.
- **Échoué**: Vous pouvez consulter les journaux de la tâche pour comprendre la raison de l'échec.

La liste des tâches se rafraîchira automatiquement, ou vous pouvez cliquer **Actualiser** pour obtenir manuellement le dernier statut.

## Téléchargement et analyse

Après la fin de la tâche, cliquez sur **Télécharger** pour obtenir le PCAP fichier. La fonction de téléchargement dépend de la configuration correcte du système avec le stockage objet.

PCAP Les fichiers peuvent contenir des adresses de requête, des données de protocole ou d'autres informations sensibles. Veuillez les fournir uniquement au personnel autorisé et les stocker ou les supprimer correctement après utilisation.

## Situations courantes

- **Pod non trouvé**: La page n'affiche que les Pods qui sont dans l'état Running dans l'environnement actuel. Veuillez vérifier le statut du Pod et l'environnement de déploiement.
- **Impossible de démarrer la capture de paquets**: Veuillez vous assurer que le Pod n'a pas de tâche de capture de paquets en cours et vérifier Kubernetes les permissions et la prise en charge des conteneurs éphémères.
- **Échec de l'exécution de la tâche**: Vérifiez les journaux de la tâche pour vérifier l'expression de filtre, le statut du Pod et si les composants de capture de paquets fonctionnent correctement.
- **Impossible de télécharger le fichier**: Veuillez vérifier la configuration du stockage d'objets et la connectivité réseau.
- **Fichier de capture de paquets trop volumineux**: Réduisez la durée de la capture et utilisez une expression de filtre plus précise.

> La capture de paquets consomme certaines ressources réseau, CPUet de stockage. Veuillez éviter d'effectuer des captures longue durée et à volume élevé sans filtres pendant les périodes de pointe.
