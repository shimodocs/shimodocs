# Journaux en temps réel

[← ShimoDocs Suite documentation de déploiement](../../../README.md)

## Aperçu des fonctionnalités

Les journaux en temps réel sont utilisés pour consulter les journaux d'exploitation des services dans un Kubernetes cluster en ligne, vous permettant de localiser rapidement les anomalies de service, les échecs de requêtes et les retards d'exécution.

Principaux cas d'utilisation :
- Filtrer rapidement les journaux déclenchés en temps réel
- Une alternative légère lorsqu'un système complet de journalisation n'est pas déployé

Remarque : Les journaux en temps réel sont obtenus via le Kubernetes API, et les données de journal peuvent être affectées par Kubernetes les mises à jour progressives.

## Accéder à la page

Après vous être connecté à la console de gestion, sélectionnez **Journaux en temps réel** dans la navigation de gauche pour entrer sur la page.

Les journaux en temps réel ne sont pris en charge que dans le Kubernetes mode de déploiement. Si vous ne voyez pas ce menu, veuillez contacter votre administrateur système pour confirmer le mode de déploiement et les permissions d'accès de votre compte actuel.

## Interrogation des journaux

Il est recommandé de suivre ces étapes :

1. Sélectionnez le **Cluster** et **NAMESPACE** vous voulez interroger.
2. Choisissez la cible du journal, en prenant en charge Deployment, StatefulSet ou Pod, et plusieurs cibles peuvent être sélectionnées en même temps.
3. Sélectionnez la plage de journaux, qui peut interroger les 100 lignes les plus récentes, 1000 lignes, 5000 lignes ou les journaux de la dernière minute à 24 heures.
4. Pour affiner les résultats de la requête, vous pouvez remplir des conditions de filtre au niveau des lignes.
5. Cliquez **Démarrer** et la page chargera les journaux dans la plage sélectionnée et affichera en continu les journaux nouvellement générés.

Cliquez **Arrêter** pour mettre fin à la récupération en temps réel. Lorsque la requête est relancée, les journaux sur la page actuelle seront effacés et les nouveaux résultats de la requête seront chargés.

## Filtrage des journaux

Le filtrage au niveau des lignes n'est pas sensible à la casse pour les lettres anglaises. Appuyez sur Entrée après avoir saisi les conditions pour les appliquer. Les usages courants sont les suivants :

```text
error
error AND timeout
error OR warning
error NOT health
error AND (timeout OR deadline)
"connection refused"
```

- `AND`: Inclut plusieurs conditions simultanément. 
- `OR`: Inclut n'importe laquelle des conditions. 
- `NOT`: Exclut le contenu spécifié. 
- `()`: Combine plusieurs conditions de filtre. 
- `""`: Recherche la phrase complète contenant des espaces. 

Vous pouvez cliquer sur le bouton d'aide à droite de la boîte de saisie pour voir des exemples de syntaxe complets. Vous pouvez également utiliser **Questions fréquentes** pour remplir rapidement les cibles de journal prédéfinies et les conditions de filtrage. 

## Consultation des journaux 

La liste des journaux affiche l'heure du journal, POD_NAME, et le contenu du journal. 

- Cliquez sur POD_NAME pour copier le nom complet. 
- Lorsque le contenu est long, vous pouvez développer pour voir le journal complet. 
- Les journaux au JSON format peuvent être développés en contenu formaté et prennent en charge la copie en un clic. 
- Lorsqu'il y a de nombreux journaux, la page paginera automatiquement, et vous pouvez rapidement sauter en haut ou en bas en utilisant les boutons en haut de la liste. 

## Répartition du volume des journaux 

Le graphique de répartition du volume des journaux sur la page montre le nombre de journaux pendant différentes périodes et affiche le nombre total de lignes de journaux ainsi que le nombre de lignes correspondantes après filtrage. 

Vous pouvez faire glisser pour sélectionner une plage horaire sur le graphique de répartition, et la liste des journaux ne affichera que le contenu dans cette plage horaire, ce qui est idéal pour se concentrer rapidement sur les périodes de pics soudains ou d'anomalies des journaux. 

## Opérations de page 

- **Démarrer**: Récupérer les journaux en fonction des conditions actuelles et recevoir continuellement de nouveaux journaux. 
- **Arrêter**: Arrêter la réception de nouveaux journaux ; les journaux déjà chargés resteront sur la page.
- **Effacer**: Effacer les journaux actuellement affichés ; si la récupération en temps réel continue, de nouveaux journaux apparaîtront toujours.

## Situations courantes

- **Pas encore de journaux**: Veuillez vous assurer que le service cible est en cours d'exécution et essayez d'élargir la plage horaire des journaux.
- **Aucune cible sélectionnée**: Veuillez sélectionner au moins un Deployment, StatefulSet ou Pod.
- **Trop de cibles**: Une seule requête prend en charge jusqu'à 20 Pods réels ; veuillez réduire la sélection et réessayer.
- **Conditions de filtre invalides**: Veuillez vérifier si `AND`, `OR`, `NOT`, parenthèses ou guillemets sont complets.
- **Récupération des journaux interrompue**: Cela peut être causé par un redémarrage du service, des changements de réseau ou des permissions insuffisantes. Vous pouvez cliquer **Démarrer** à nouveau.

> La page conserve jusqu'à 500 000 lignes de journaux. Une fois la limite dépassée, les anciens journaux seront automatiquement supprimés.

## Exemple d'interface d'exploitation

La figure ci-dessous montre les zones de sélection de charge de travail, de filtrage par mot-clé et de visualisation des journaux en temps réel.

Cliquez **Sélectionner le Cluster & NAMESPACE** pour changer le cluster cible et NAMESPACE, puis continuez à sélectionner les charges de travail que vous souhaitez consulter.

