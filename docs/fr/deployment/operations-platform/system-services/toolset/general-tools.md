# Outils généraux

[← ShimoDocs Suite documentation de déploiement](../../../README.md)

## 1. Aperçu de la page

La page Outils Généraux comprend 7 fonctions couramment utilisées : JSON formatage, conversion de format, JWT décodage, conversion de timestamp, vérification de l'heure machine, analyse de code QR, et encodage/décodage Base64.

1. Cliquez sur n'importe quelle carte de fonction pour entrer dans la page d'opération.
2. Après être entré, vous pouvez passer directement à d'autres outils à partir de la liste des fonctions à gauche.
3. Cliquez sur 'Retour au menu des fonctions' pour revenir à la page d'accueil de la carte.

## 2. JSON Formatage

Cette fonction est utilisée pour formater, compresser et valider JSON le contenu.

1. Cliquez sur "JSON Formatage."
2. Entrez le contenu à traiter dans le "Input" JSON"zone", par exemple :

   ```json
   {"name":"MDP","enabled":true,"items":[1,2]}
   ```

3. Cliquez sur 'Format', puis en retrait JSON sera généré à droite.
4. Cliquez sur "Compresser", et un compact JSON sans espaces ni sauts de ligne sera généré à droite.
5. Cliquez sur 'Copier' pour copier le résultat traité.
6. Cliquez sur 'Effacer' pour supprimer le contenu d'entrée et de sortie.

Résultats du test : Les chaînes, les valeurs booléennes et les tableaux sont tous correctement préservés, et les fonctions de formatage et de compression fonctionnent correctement.

## 3. Conversion de format

Cette fonction prend en charge la conversion et le formatage entre JSON, YAML, et TOML formats.

1. Cliquez sur "Conversion de format."
2. Sélectionnez le format du contenu d'entrée dans « Format source ».
3. Sélectionnez le format de sortie souhaité dans « Format cible ».
4. Saisissez le contenu à convertir à gauche.
5. Cliquez sur « Convertir », et le résultat s'affichera à droite.
6. Cliquez sur « Inverser le format » pour échanger les formats source et cible.
7. Cliquez sur "Format" pour ajuster l'indentation et la mise en page du contenu actuel.
8. Cliquez sur "Copier" pour copier le résultat.

Cette fois, nous utilisons JSON pour convertir en YAML, entrez :

```json
{"name":"MDP","ports":[80,443],"enabled":true}
```

Résultat de la conversion : 

```yaml
name: MDP
ports:
  - 80
  - 443
enabled: true
```

Résultats mesurés : Champs, tableaux et valeurs booléennes convertis correctement.

## 4. JWT Décoder

Cette fonctionnalité est utilisée pour analyser l'en-tête, la charge utile et la signature d'un JWT Jeton.

1. Cliquez sur "JWT Décoder.
2. Collez le JWT Jeton dans la zone de saisie.
3. Cliquez sur "Décoder."
4. Voir l'algorithme de signature et le type de jeton dans l'en-tête.
5. Voir les informations telles que l'utilisateur, le rôle et la date d'expiration dans la charge utile.
6. Voir le contenu brut de la signature.
7. Cliquez sur le bouton copier dans chaque section pour copier les résultats de l'analyse.
8. Cliquez sur "Effacer" pour supprimer le Token actuel et les résultats de l'analyse.

Résultats du test : Le Token testé a analysé avec succès des champs tels que `HS256`, `JWT`, utilisateur, rôle et heure d'expiration.

> JWT Le décodage est uniquement destiné à visualiser la structure du Token et ne peut pas remplacer la vérification de la validité de la signature côté serveur.

## 5. Conversion de l'horodatage 

Cette fonctionnalité prend en charge la conversion bidirectionnelle entre l'horodatage Unix et la date-heure. 

### 5.1 Conversion de l'horodatage en date/heure 

1. Cliquez sur "Conversion de l'horodatage". 
2. Entrez un horodatage de 10 chiffres en secondes ou de 13 chiffres en millisecondes dans le champ "Horodatage (secondes ou millisecondes)". 
3. Cliquez sur "Convertir" en haut. 
4. Consultez la date et l'heure dans "Résultats de la conversion". 
5. Cliquez sur le bouton copier à côté du résultat pour copier le contenu. 

### 5.2 Date-heure en horodatage 

1. Entrez 'YYYY-MM-JJ HH:mm:ss' ou le ISO format de l'heure dans le champ "Date et heure". 
2. Cliquez sur "Convertir" ci-dessous. 
3. Visualisez l'horodatage Unix dans "Résultat de conversion (secondes)". 
4. Cliquez sur "Heure actuelle" pour remplir rapidement l'horodatage et la date actuels. 
5. Cliquez sur "Effacer" pour supprimer tout le contenu. 

Résultat du test : '1704067200' converti avec succès en date-heure, et la date-heure peut également être correctement convertie en horodatages au niveau des secondes. 

> Lors de la vérification des données entre fuseaux horaires, clarifiez d'abord si le temps d'activité utilise UTC les fuseaux horaires de l'entreprise ou locaux. 

## 6. Vérification de l'heure de la machine

Cette fonction est utilisée pour vérifier l'heure de tous les Pods avec `app=ws-gateway` dans le courant NAMESPACE et mettre en évidence les instances avec un écart de temps de plus de 30 secondes.

1. Cliquez sur "Vérification de l'heure de la machine".
2. Cliquez sur "Actualiser" dans le coin supérieur droit.
3. Vérifiez les libellés actuels NAMESPACE et de la requête.
4. Consultez l'heure de référence calculée par le système, qui est l'heure médiane de tous les Pods.
5. Consultez le nœud où chaque Pod est situé, le timestamp Unix et l'heure lisible.
6. Vérifiez "Différence par rapport à la référence" et "Statut".
7. Si l'écart dépasse 30 secondes, vérifiez le NTP/Chrony du nœud, l'heure de la machine virtuelle et les paramètres de fuseau horaire.

Résultat du test : 1 `ws-gateway` Pod retourné, avec un écart par rapport à l'heure de référence de `0s` et un statut "Normal".

## 7. Analyse de code QR

Cette fonction est utilisée pour télécharger des images de code QR et extraire le texte, les liens ou autres contenus qu'elles contiennent.

1. Cliquez sur "Analyse de code QR".
2. Cliquez sur "Sélectionner un fichier".
3. Choisissez une image de code QR claire depuis votre appareil local.
4. Après que la page affiche l'aperçu de l'image, consultez le "Résultat de l'analyse" ci-dessous.
5. Comparez le résultat avec le contenu attendu du code QR pour confirmer la cohérence.
6. Cliquez sur "Copier" pour copier le contenu analysé.
7. Cliquez sur "Effacer" pour supprimer l'image et le résultat de l'analyse.

Résultat du test : Le code QR de test peut être téléchargé avec succès, prévisualisé et correctement analysé.

## 8. Encodage et décodage Base64

Cette fonction permet la conversion bidirectionnelle entre texte brut et contenu Base64.

### 8.1 Encodage Base64

1. Cliquez sur "Encodage et décodage Base64".
2. Saisissez le texte brut à gauche.
3. Cliquez sur "Encoder".
4. Consultez le résultat encodé en Base64 à droite.

### 8.2 Décodage Base64

1. Saisissez le contenu Base64 à gauche.
2. Cliquez sur "Décoder."
3. Consultez le texte restauré à droite.
4. Cliquez sur "Copier" pour copier le résultat.
5. Cliquez sur "Effacer" pour effacer l'entrée et la sortie.

Résultat du test :

```text
MDPTEST → TURQ5rWL6K+V
TURQ5rWL6K+V → MDPTEST
```

Chinois UTF-8 le contenu peut être converti normalement dans les deux sens.

