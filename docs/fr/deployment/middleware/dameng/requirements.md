# Dameng Exigences V8

[← ShimoDocs Suite documentation de déploiement](../../README.md)

Ce document est destiné à guider le personnel qui met en œuvre, maintient ou intègre Dameng la base de données pour la première fois, afin de compléter Dameng DM8 l'initialisation de la base de données, MySQL la configuration du mode de compatibilité, le démarrage du service et la vérification de la connexion étape par étape.

Les exemples dans ce document utilisent la planification suivante :

| Élément | Valeur d'exemple |
| --- | --- |
| Répertoire d'installation de la base de données | `/opt/dmdbms` |
| Répertoire de stockage de la base de données | `/dmdata/data` |
| DATABASE_NAME | `DMTEST` |
| Nom de l'instance | `DBSERVER` |
| Port de la base de données | `5236` |
| Compte administrateur | `SYSDBA` |
| Administrateur PASSWORD | `<SYSDBA_PASSWORD>` |

> Remarque : `<SYSDBA_PASSWORD>` et `<SYSAUDITOR_PASSWORD>` dans le document sont des espaces réservés. Lors des opérations réelles, veuillez les remplacer par de vrais mots de passe qui respectent les PASSWORD exigences de complexité.

## 1. Confirmation préopératoire

### 1. Confirmez que Dameng est déjà installé

Exécutez sur le serveur :

```bash
ls /opt/dmdbms/bin
```

Si vous pouvez voir des fichiers comme `dminit`, `dmserver`, `disql`, cela indique que le Dameng logiciel a déjà été installé.

Vous pouvez également vérifier la version :

```bash
/opt/dmdbms/bin/dmserver
```

Un contenu comme celui-ci peut apparaître dans la sortie :

```text
dmserver V8
version: 03134284194-20240920-243574-20108
```

### 2. Confirmer l'utilisateur système

Dameng généralement exécute la base de données en utilisant l'utilisateur `dmdba` . Vérifiez si l'utilisateur existe :

```bash
id dmdba
```

S'il n'existe pas, il peut être créé par l'utilisateur `root` :

```bash
groupadd dinstall
useradd -g dinstall -m -d /home/dmdba -s /bin/bash dmdba
passwd dmdba
```

### 3. Préparer le répertoire des données

Exécutez en utilisant le `root` :

```bash
mkdir -p /dmdata/data
chown -R dmdba:dinstall /dmdata
chmod -R 775 /dmdata
```

Le but de cette étape est de créer un répertoire pour stocker les fichiers de la base de données et d'accorder les permissions à l'utilisateur `dmdba` .

## 2. Initialiser la base de données

Passez à `dmdba` :

```bash
su - dmdba
```

Exécutez la commande d'initialisation : 

```bash
/opt/dmdbms/bin/dminit \
  PATH=/dmdata/data \
  PAGE_SIZE=32 \
  EXTENT_SIZE=32 \
  CASE_SENSITIVE=0 \
  UNICODE_FLAG=1 \
  DB_NAME=DMTEST \
  INSTANCE_NAME=DBSERVER \
  PORT_NUM=5236 \
  SYSDBA_PWD=<SYSDBA_PASSWORD> \
  SYSAUDITOR_PWD=<SYSAUDITOR_PASSWORD>
```

Si l'initialisation réussit, vous verrez une sortie similaire à : 

```text
create dm database success
```
Après une initialisation réussie, le répertoire de la base de données est généré : 

```text
/dmdata/data/DMTEST
```

Le fichier de configuration clé parmi eux est :

```text
/dmdata/data/DMTEST/dm.ini
```

## 3. Modifier MySQL Configuration de compatibilité

Modifiez le fichier de configuration en utilisant le `root` ou `dmdba` :

```bash
vi /dmdata/data/DMTEST/dm.ini
```

Recherchez et modifiez les deux configurations suivantes : 

```ini
COMPATIBLE_MODE = 4
ORDER_BY_NULLS_FLAG = 0
```

Si le fichier contient déjà ces deux configurations, vous pouvez directement modifier les lignes existantes.

Ne pas ajouter une autre configuration du même nom à la fin du fichier, sinon des configurations en double peuvent apparaître, faisant en sorte que la valeur réelle effective diffère de celle prévue.

Après avoir terminé la modification, vous pouvez vérifier en utilisant la commande suivante :

```bash
grep -Ein 'COMPATIBLE_MODE|ORDER_BY_NULLS_FLAG' /dmdata/data/DMTEST/dm.ini
```

Vous devriez voir :

```text
COMPATIBLE_MODE = 4
ORDER_BY_NULLS_FLAG = 0
```

## 4. Enregistrer le service de base de données

Revenez à `root` :

```bash
exit
```

Enregistrer le service de base de données : 

```bash
/opt/dmdbms/script/root/dm_service_installer.sh \
  -t dmserver \
  -p DBSERVER \
  -dm_ini /dmdata/data/DMTEST/dm.ini
```

Après un enregistrement réussi, le nom du service est généralement :

```text
DmServiceDBSERVER.service
```

Configurer pour démarrer au démarrage et démarrer le service : 

```bash
systemctl daemon-reload
systemctl enable DmServiceDBSERVER.service
systemctl start DmServiceDBSERVER.service
```

Vérifier l'état du service : 

```bash
systemctl status DmServiceDBSERVER.service --no-pager
```

Si vous voyez : 

```text
Active: active (running)
```

Indique que le service de base de données a démarré. 

## 5. Vérifier si la base de données est disponible 

### 1. Vérifier le port 

Exécutez : 

```bash
ss -lntp | grep ':5236'
```

Si vous voyez `dmserver` écoutant sur `5236`, cela indique que le port de la base de données est normal.

### 2. Test de connexion locale

Passez à `dmdba` :

```bash
su - dmdba
```

Connectez-vous à la base de données : 

```bash
/opt/dmdbms/bin/disql SYSDBA/<SYSDBA_PASSWORD>@127.0.0.1:5236
```

Exécutez après une connexion réussie :

```sql
select 1 as OK;
```

Si retour : 

```text
OK
-----------
1
```

Indique que la connexion à la base de données est normale. 

### 3. Vérifiez si c'est MySQL mode compatible

Exécutez dans `disql`: 

```sql
select para_name, para_value
from v$dm_ini
where para_name in (
  'COMPATIBLE_MODE',
  'ORDER_BY_NULLS_FLAG',
  'INSTANCE_NAME',
  'PORT_NUM'
);
```

Résultat attendu : 

```text
INSTANCE_NAME        DBSERVER
PORT_NUM             5236
COMPATIBLE_MODE      4
ORDER_BY_NULLS_FLAG  0
```

Parmi eux :

```text
COMPATIBLE_MODE = 4
```

Indique que l'état d'exécution actuel de la base de données a activé MySQL le mode compatible. 



## Annexe 1, Description détaillée des éléments de configuration 

### 1. `PATH` 

Exemple : 

```text
PATH=/dmdata/data
```

Signification : 

`PATH` est le répertoire racine des fichiers de la base de données. Lors de l'initialisation, Dameng créera le répertoire de base de données dans ce répertoire.

Si `DB_NAME=DMTEST`, le répertoire final est généralement : 

```text
/dmdata/data/DMTEST
```

Ce répertoire stockera les fichiers de données, les fichiers journaux, les fichiers de contrôle et le `dm.ini` fichier de configuration.

Recommandations :

- Il est recommandé de le placer sur un disque de données avec une capacité suffisante et des performances stables dans un environnement de production.
- Il n'est pas recommandé de le placer dans des répertoires temporaires, tels que `/tmp`.
- Ne déplacez pas le répertoire de manière imprudente après l'initialisation.

### 2. `DB_NAME`

Exemple :

```text
DB_NAME=DMTEST
```

Signification : 

`DB_NAME` est le nom du DATABASE_NAME. Cela affectera le nom du répertoire de la base de données, le nom du fichier journal, etc. 

Par exemple, lorsque `DB_NAME=DMTEST`, il génère généralement : 

```text
/dmdata/data/DMTEST
/dmdata/data/DMTEST/DMTEST01.log
/dmdata/data/DMTEST/DMTEST02.log
```

Recommandations :

- Utilisez un seul clair DATABASE_NAME tout au long du projet.
- Il n'est pas recommandé de le changer après l'initialisation.

### 3. `INSTANCE_NAME`

Exemple :

```text
INSTANCE_NAME=DBSERVER
```

Signification : 

`INSTANCE_NAME` est le nom de l'instance de la base de données. Il est généralement utilisé pour générer le nom du service lors de l'enregistrement d'un service.

Par exemple : 

```text
INSTANCE_NAME=DBSERVER
```

Le nom du service après l'enregistrement est généralement :

```text
DmServiceDBSERVER.service
```

Recommandation : 

- Pour une machine unique avec une seule instance, vous pouvez utiliser `DBSERVER`.
- Lors du déploiement de plusieurs instances sur une machine, chaque nom d'instance doit être différent.

### 4. `PORT_NUM`

Exemple : 

```text
PORT_NUM=5236
```

Signification : 

`PORT_NUM` est le port d'écoute de la base de données. Les applications doivent accéder à ce port lors de la connexion à la base de données. 

Le port saisi sur la page du programme doit être cohérent avec celui-ci : 

```text
HOST:172.17.9.84
PORT:5236
```

Recommandations : 

- Le port par défaut pour Dameng est généralement `5236`. 
- S'il y a plusieurs Dameng instances sur la même machine, les ports ne peuvent pas être dupliqués. 
- Après avoir changé le port, le service de base de données doit être redémarré. 

### 5. `PAGE_SIZE` 

Exemple : 

```text
PAGE_SIZE=32
```

Signification : 

`PAGE_SIZE` est la taille de page de la base de données, en KB. Lorsque la base de données lit et écrit des données, elle organise les données en fonction des pages. 

`PAGE_SIZE=32` signifie que chaque page de données fait 32 Ko. 

Impact : 

- Cela affecte le stockage des données, l'indexation et le comportement des E/S. 
- Il n'est pas recommandé de modifier après l'initialisation. 
- Si un ajustement est nécessaire, cela nécessite généralement de réinitialiser la base de données et de migrer les données. 

Recommandations : 

- S'il y a un SOP pour le scénario, configurez selon le SOP. 
- Lorsqu'il n'y a pas d'exigences particulières, ne le changez pas arbitrairement. 

### 6. `EXTENT_SIZE` 

Exemple : 

```text
EXTENT_SIZE=32
```

Signification : 

`EXTENT_SIZE` est la taille du cluster, mesurée en pages. Elle peut être comprise comme l'unité de base d'allocation d'espace utilisée par la base de données à la fois.

Si : 

```text
PAGE_SIZE=32
EXTENT_SIZE=32
```

Alors un cluster correspond à environ : 

```text
32KB * 32 = 1024KB
```

Cela correspond à environ 1 Mo. 

Impact : 

- Affectera la granularité de l'allocation d'espace des fichiers de données. 
- Il n'est pas recommandé de modifier après l'initialisation. 

### 7. `CASE_SENSITIVE` 

Exemple : 

```text
CASE_SENSITIVE=0
```

Signification : 

`CASE_SENSITIVE` indique si les noms des objets de la base de données sont sensibles à la casse.

Valeurs courantes : 

```text
0:CASE_INSENSITIVE
1:CASE_SENSITIVE
```

Par exemple, lorsqu'il n'y a pas de distinction de casse, les deux noms de table suivants peuvent être considérés comme le même objet :

```text
user
USER
```

Impact : 

- Affectera la reconnaissance des noms de tables, des noms de champs et des noms d'objets. 
- Pour MySQL migration ou MySQL-compatibles, il est généralement préférable de le configurer comme `0`. 
- Il n'est pas recommandé de le modifier après l'initialisation. 

### 8. `UNICODE_FLAG` 

Exemple : 

```text
UNICODE_FLAG=1
```

Signification : 

`UNICODE_FLAG` est une configuration de jeu de caractères.

Valeurs courantes : 

```text
0:GB18030
1:UTF-8
2:EUC-KR
```

`UNICODE_FLAG=1` indique que la base de données utilise le UTFjeu de caractères -8.

Recommandation :

- Il est recommandé d'utiliser UTF-8 pour les nouveaux systèmes.
- Meilleure compatibilité avec les caractères chinois, anglais et multilingues.
- Il n'est pas recommandé de modifier après l'initialisation.

### 9. `SYSDBA_PWD`

Exemple :

```text
SYSDBA_PWD=<SYSDBA_PASSWORD>
```

Signification : 

`SYSDBA_PWD` est le PASSWORD pour le `SYSDBA` compte administrateur.

`SYSDBA` est similaire à un super administrateur de base de données et dispose de privilèges élevés.

Recommandation : 

- Utilisez un PASSWORD.
- Ne pas utiliser de mots de passe simples tels que `SYSDBA`, `123456`, `password`.
- PASSWORD la longueur recommandée est d'au moins 8 caractères et inclut des lettres et des chiffres.
- Ne pas écrire le PASSWORD réel dans des documents externes.

### 10. `SYSAUDITOR_PWD`

Exemple : 

```text
SYSAUDITOR_PWD=<SYSAUDITOR_PASSWORD>
```

Signification : 

`SYSAUDITOR_PWD` est le PASSWORD de la `SYSAUDITOR` compte administrateur d'audit.

`SYSAUDITOR` est principalement utilisé pour les capacités de gestion liées à l'audit.

Recommandation :

- Utilisez un PASSWORD différent de `SYSDBA`.
- Utilisez un PASSWORD qui répond aux exigences de complexité.

### 11. `COMPATIBLE_MODE`

Exemple :

```text
COMPATIBLE_MODE = 4
```

Signification : 

`COMPATIBLE_MODE` est la configuration du mode de compatibilité de la Dameng Base de données, utilisée pour contrôler à quel type de base de données la base de données s'aligne en termes de SQL syntaxe, fonctions et certains comportements.

Significations des valeurs courantes : 

```text
0:DEFAULT_MODE
1:SQL92
2:Oracle
3:MS SQL Server
4:MySQL
5:DM6
6:Teradata
7:PostgreSQL
8:DB2
```

Ce texte est configuré comme : 

```text
COMPATIBLE_MODE = 4
```

Indique l'activation de MySQL le mode compatible. 

Fonction : 

- Améliore la compatibilité de MySQL SQL la syntaxe dans Dameng. 
- Réduit le coût de transformation lors de la migration à partir de MySQL ou de l'adaptation à Dameng. 

Remarque : 

- Cette configuration ne signifie pas que Dameng prend en charge le MySQL protocole. 
- Les programmes doivent toujours utiliser le Dameng pilote en interne ; s'il n'y a pas d'option de configuration de pilote sur la page, les utilisateurs n'ont pas besoin de le remplir séparément. 
- Un redémarrage du service de base de données est nécessaire après modification. 
- Si cela prend effet en fin de compte doit être basé sur les `v$dm_ini` résultats de la requête. 

### 12. `ORDER_BY_NULLS_FLAG` 

Exemple : 

```text
ORDER_BY_NULLS_FLAG = 0
```

Signification : 

`ORDER_BY_NULLS_FLAG` est utilisé pour contrôler si NULL les valeurs apparaissent au début ou à la fin lors du tri avec `ORDER BY`. 

Pourquoi c'est important : 

Différentes bases de données peuvent avoir des comportements par défaut différents pour le tri des NULL. Lors de la migration d'une application de MySQL à Dameng, si les résultats du tri dépendent de la position des NULL, ce paramètre peut affecter l'ordre des résultats de la requête. 

Cet article est configuré comme : 

```text
ORDER_BY_NULLS_FLAG = 0
```

Le but est de rendre le comportement du tri plus proche des MySQL habitudes d'utilisation.

Remarque :

- Un redémarrage du service de base de données est nécessaire après modification.
- Si le business SQL a déjà explicitement spécifié `NULLS FIRST` ou `NULLS LAST`, le comportement spécifié dans le SQL doit avoir la priorité.

## Annexe 2, Questions fréquemment posées

### 1. Pourquoi ne puis-je pas me connecter en utilisant un MySQL client même après configuration MySQL ?

Parce que MySQL le mode de compatibilité affecte uniquement SQL syntaxe et certains comportements de base de données, cela ne change pas Damengle protocole réseau de '.

Lorsque des applications ou des outils se connectent à Dameng, le Dameng pilote doit toujours être utilisé :

```text
dm.jdbc.driver.DmDriver
jdbc:dm://<host>:5236
```

ne peut pas être utilisé : 

```text
com.mysql.cj.jdbc.Driver
jdbc:mysql://<host>:5236
```

### 2. Comment confirmer que la configuration a réellement pris effet ?

Ne vous contentez pas de regarder le `dm.ini` fichier ; il est recommandé de se connecter à la base de données pour vérifier l'état d'exécution :

```sql
select para_name, para_value
from v$dm_ini
where para_name in ('COMPATIBLE_MODE', 'ORDER_BY_NULLS_FLAG');
```

L'état d'exécution est effectif uniquement lorsque les résultats suivants sont observés : 

```text
COMPATIBLE_MODE      4
ORDER_BY_NULLS_FLAG  0
```

### 3. Pourquoi cela ne prend-il pas effet après modification `dm.ini`?

Raisons courantes :

- Le service de base de données n'a pas été redémarré après la modification.
- Il y a des éléments de configuration en double dans le fichier.
- Le fichier modifié n'est pas le `dm.ini` actuellement utilisé par l'instance.

Vous pouvez confirmer quel fichier de configuration l'instance actuelle utilise via la commande de démarrage du service :

```bash
systemctl status DmServiceDBSERVER.service --no-pager
```

Vous verrez généralement quelque chose comme ce qui suit dans la sortie :

```text
dmserver path=/dmdata/data/DMTEST/dm.ini -noconsole
```

### 4. Que dois-je faire si une PASSWORD erreur de complexité se produit lors de l'initialisation ?

Indique que le PASSWORD est trop simple. Veuillez le changer pour un PASSWORD, par exemple :

```text
AT_LEAST 8 POSITION
CONTAINS_LETTERS_AND_NUMBERS
AVOID_USING_THE_ACCOUNT_NAME_ITSELF
```

### plus complexe. Ces paramètres peuvent-ils être changés plus tard ? 

Non. 

Il n'est généralement pas recommandé de changer les paramètres d'initialisation plus tard, par exemple : 
- 'PAGE_SIZE'
- 'EXTENT_SIZE'
- 'CASE_SENSITIVE'
- 'UNICODE_FLAG'
- 'DB_NAME'
- 'INSTANCE_NAME'

Si ces paramètres sont mal configurés, il est généralement recommandé de réinitialiser la base de données et de migrer à nouveau les données. Le 

'dm.ini' paramètre peut être ajusté plus tard, par exemple : 

- 'COMPATIBLE_MODE'
- 'ORDER_BY_NULLS_FLAG'
- 'PORT_NUM'

Cependant, après modification, le service de base de données doit généralement être redémarré. 

## Annexe 3 : Liste de contrôle finale 


- Le répertoire de données '/dmdata/data' a été créé. 
- L'hôte du répertoire de données est 'dmdba:dinstall'. 
- La base de données a été initialisée avec succès. 
- '/dmdata/data/DMTEST/dm.ini' existe. 
- `COMPATIBLE_MODE = 4`. 
- `ORDER_BY_NULLS_FLAG = 0`. 
- Service de base de données `DmServiceDBSERVER.service` est `active`. 
- Port `5236` est en écoute. 
- `SYSDBA` peut se connecter à la base de données. 
- Dans `v$dm_ini`, la valeur d'exécution de `COMPATIBLE_MODE` est `4`.
