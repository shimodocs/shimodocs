# Dameng V8 Anforderungen

[← ShimoDocs Suite Bereitstellungsdokumentation](../../README.md)

Dieses Dokument soll dem Personal als Leitfaden dienen, das implementiert, wartet oder integriert. Dameng Datenbank zum ersten Mal, um die Dameng DM8 Datenbankinitialisierung, MySQL Konfigurationsmodus-Kompatibilität, Dienststart und Verbindungsüberprüfung Schritt für Schritt.

Die Beispiele in diesem Dokument verwenden die folgende Planung:

| Artikel | Beispielwert |
| --- | --- |
| Datenbank-Installationsverzeichnis | `/opt/dmdbms` |
| Datenbank-Speicherverzeichnis | `/dmdata/data` |
| DATABASE_NAME | `DMTEST` |
| Instanzname | `DBSERVER` |
| Datenbankport | `5236` |
| Administrator-Konto | `SYSDBA` |
| Administrator PASSWORD | `<SYSDBA_PASSWORD>` |

> Hinweis: `<SYSDBA_PASSWORD>` und `<SYSAUDITOR_PASSWORD>` in dem Dokument sind Platzhalter. Während der tatsächlichen Operationen ersetzen Sie sie bitte durch echte Passwörter, die den PASSWORD Komplexitätsanforderungen entsprechen.

## 1. Bestätigung vor dem Betrieb

### 1. Bestätigen Sie, dass Dameng bereits installiert ist

Auf dem Server ausführen:

```bash
ls /opt/dmdbms/bin
```

Wenn Sie Dateien wie `dminit`, `dmserver`, `disql`sehen, zeigt dies an, dass die Dameng Software wurde bereits installiert.

Sie können auch die Version überprüfen:

```bash
/opt/dmdbms/bin/dmserver
```

Inhalt wie dieser kann im Ausgabe erscheinen:

```text
dmserver V8
version: 03134284194-20240920-243574-20108
```

### 2. Systembenutzer bestätigen

Dameng führt normalerweise die Datenbank mit dem `dmdba` Benutzer aus. Überprüfen Sie, ob der Benutzer existiert:

```bash
id dmdba
```

Wenn er nicht existiert, kann er von dem `root` Benutzer erstellt werden:

```bash
groupadd dinstall
useradd -g dinstall -m -d /home/dmdba -s /bin/bash dmdba
passwd dmdba
```

### 3. Bereiten Sie das Datenverzeichnis vor

Führen Sie mit dem `root` Benutzer erstellt werden:

```bash
mkdir -p /dmdata/data
chown -R dmdba:dinstall /dmdata
chmod -R 775 /dmdata
```

Zweck dieses Schrittes ist es, ein Verzeichnis zum Speichern von Datenbankdateien zu erstellen und dem `dmdba` Benutzer Berechtigungen zu erteilen.

## 2. Datenbank initialisieren

Wechseln Sie zu dem `dmdba` Benutzer erstellt werden:

```bash
su - dmdba
```

Führen Sie den Initialisierungsbefehl aus: 

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

Wenn die Initialisierung erfolgreich ist, sehen Sie eine ähnliche Ausgabe wie: 

```text
create dm database success
```
Nach erfolgreicher Initialisierung wird das Datenbankverzeichnis erstellt: 

```text
/dmdata/data/DMTEST
```

Die wichtigste Konfigurationsdatei darunter ist:

```text
/dmdata/data/DMTEST/dm.ini
```

## 3. Ändern MySQL Kompatibilitätskonfiguration

Bearbeiten Sie die Konfigurationsdatei mit dem `root` oder `dmdba` Benutzer erstellt werden:

```bash
vi /dmdata/data/DMTEST/dm.ini
```

Suchen und ändern Sie die folgenden zwei Konfigurationen: 

```ini
COMPATIBLE_MODE = 4
ORDER_BY_NULLS_FLAG = 0
```

Wenn die Datei bereits diese beiden Konfigurationen enthält, können Sie die vorhandenen Zeilen direkt ändern.

Fügen Sie nicht am Ende der Datei eine weitere Konfiguration mit demselben Namen hinzu, da sonst doppelte Konfigurationen auftreten können, wodurch der tatsächlich wirksame Wert vom erwarteten Wert abweichen kann.

Nach Abschluss der Änderung können Sie mit dem folgenden Befehl überprüfen:

```bash
grep -Ein 'COMPATIBLE_MODE|ORDER_BY_NULLS_FLAG' /dmdata/data/DMTEST/dm.ini
```

Erwartet wird:

```text
COMPATIBLE_MODE = 4
ORDER_BY_NULLS_FLAG = 0
```

## 4. Datenbankdienst registrieren

Zurückwechseln zu dem `root` Benutzer erstellt werden:

```bash
exit
```

Datenbankdienst registrieren: 

```bash
/opt/dmdbms/script/root/dm_service_installer.sh \
  -t dmserver \
  -p DBSERVER \
  -dm_ini /dmdata/data/DMTEST/dm.ini
```

Nach erfolgreicher Registrierung ist der Dienstname normalerweise:

```text
DmServiceDBSERVER.service
```

Auf Autostart setzen und Dienst starten: 

```bash
systemctl daemon-reload
systemctl enable DmServiceDBSERVER.service
systemctl start DmServiceDBSERVER.service
```

Dienststatus überprüfen: 

```bash
systemctl status DmServiceDBSERVER.service --no-pager
```

Wenn Sie Folgendes sehen: 

```text
Active: active (running)
```

Zeigt an, dass der Datenbankdienst gestartet wurde. 

## 5. Überprüfen, ob die Datenbank verfügbar ist 

### 1. Den Port überprüfen 

Ausführen: 

```bash
ss -lntp | grep ':5236'
```

Wenn Sie sehen `dmserver` hört auf `5236`, zeigt dies, dass der Datenbankport normal ist.

### 2. Lokaler Login-Test

Wechseln Sie zu dem `dmdba` Benutzer erstellt werden:

```bash
su - dmdba
```

In die Datenbank einloggen: 

```bash
/opt/dmdbms/bin/disql SYSDBA/<SYSDBA_PASSWORD>@127.0.0.1:5236
```

Nach erfolgreichem Login ausführen:

```sql
select 1 as OK;
```

Wenn zurückgegeben wird: 

```text
OK
-----------
1
```

Zeigt an, dass die Datenbankverbindung normal ist. 

### 3. Überprüfen, ob es sich um MySQL Kompatibilitätsmodus handelt

Ausführen in `disql`: 

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

Erwartetes Ergebnis: 

```text
INSTANCE_NAME        DBSERVER
PORT_NUM             5236
COMPATIBLE_MODE      4
ORDER_BY_NULLS_FLAG  0
```

Daraus:

```text
COMPATIBLE_MODE = 4
```

Zeigt an, dass der aktuelle Datenbanklaufzeitstatus den MySQL Kompatibilitätsmodus aktiviert hat. 



## Anhang 1, Detaillierte Beschreibung der Konfigurationselemente 

### 1. `PATH` 

Beispiel: 

```text
PATH=/dmdata/data
```

Bedeutung: 

`PATH` ist das Stammverzeichnis der Datenbankdateien. Während der Initialisierung Dameng wird das Datenbankverzeichnis unter diesem Verzeichnis erstellt.

Wenn `DB_NAME=DMTEST`, das endgültige Verzeichnis ist normalerweise: 

```text
/dmdata/data/DMTEST
```

Dieses Verzeichnis speichert Datendateien, Protokolldateien, Steuerdateien und die `dm.ini` Konfigurationsdatei.

Empfehlungen:

- Es wird empfohlen, es in einer Produktionsumgebung auf einer Datenfestplatte mit ausreichender Kapazität und stabiler Leistung zu platzieren.
- Es wird nicht empfohlen, es in temporären Verzeichnissen zu platzieren, wie z.B. `/tmp`.
- Verschieben Sie das Verzeichnis nach der Initialisierung nicht leichtfertig.

### 2. `DB_NAME`

Beispiel:

```text
DB_NAME=DMTEST
```

Bedeutung: 

`DB_NAME` ist der Name des DATABASE_NAME. Es wird den Datenbankverzeichnisnamen, den Protokolldateinamen usw. beeinflussen. 

Zum Beispiel, wenn `DB_NAME=DMTEST`, erzeugt es normalerweise: 

```text
/dmdata/data/DMTEST
/dmdata/data/DMTEST/DMTEST01.log
/dmdata/data/DMTEST/DMTEST02.log
```

Empfehlungen:

- Verwenden Sie einen einzigen klaren DATABASE_NAME im gesamten Projekt.
- Es wird nicht empfohlen, es nach der Initialisierung zu ändern.

### 3. `INSTANCE_NAME`

Beispiel:

```text
INSTANCE_NAME=DBSERVER
```

Bedeutung: 

`INSTANCE_NAME` ist der Name der Datenbankinstanz. Er wird normalerweise verwendet, um den Servicenamen beim Registrieren eines Dienstes zu generieren.

Zum Beispiel: 

```text
INSTANCE_NAME=DBSERVER
```

Der Dienstname nach der Registrierung ist normalerweise:

```text
DmServiceDBSERVER.service
```

Empfehlung: 

- Für einen einzelnen Computer mit einer einzelnen Instanz können Sie verwenden `DBSERVER`.
- Beim Bereitstellen mehrerer Instanzen auf einem Computer muss jeder Instanzname unterschiedlich sein.

### 4. `PORT_NUM`

Beispiel: 

```text
PORT_NUM=5236
```

Bedeutung: 

`PORT_NUM` ist der Datenbank-Listening-Port. Anwendungen müssen auf diesen Port zugreifen, wenn sie eine Verbindung zur Datenbank herstellen. 

Der auf der Programmseite eingegebene Port muss mit diesem übereinstimmen: 

```text
HOST:172.17.9.84
PORT:5236
```

Empfehlungen: 

- Der Standardport für Dameng ist normalerweise `5236`. 
- Wenn es mehrere gibt Dameng Instanzen auf derselben Maschine können die Ports nicht dupliziert werden. 
- Nach dem Ändern des Ports muss der Datenbankdienst neu gestartet werden. 

### 5. `PAGE_SIZE` 

Beispiel: 

```text
PAGE_SIZE=32
```

Bedeutung: 

`PAGE_SIZE` ist die Seitengröße der Datenbank in KB. Wenn die Datenbank Daten liest und schreibt, organisiert sie Daten basierend auf Seiten. 

`PAGE_SIZE=32` bedeutet, dass jede Datenseite 32KB groß ist. 

Auswirkung: 

- Es beeinflusst die Datenspeicherung, Indexierung und das IO-Verhalten. 
- Es wird nicht empfohlen, nach der Initialisierung Änderungen vorzunehmen. 
- Wenn eine Anpassung erforderlich ist, erfordert dies normalerweise das Neuinitialisieren der Datenbank und die Migration der Daten. 

Empfehlungen: 

- Wenn es ein SOP für das Szenario gibt, konfigurieren Sie entsprechend dem SOP. 
- Wenn keine besonderen Anforderungen bestehen, ändern Sie es nicht willkürlich. 

### 6. `EXTENT_SIZE` 

Beispiel: 

```text
EXTENT_SIZE=32
```

Bedeutung: 

`EXTENT_SIZE` ist die Clustergröße, gemessen in Seiten. Sie kann als die Grundeinheit der Speicherzuweisung verstanden werden, die von der Datenbank auf einmal verwendet wird.

Wenn: 

```text
PAGE_SIZE=32
EXTENT_SIZE=32
```

Dann beträgt ein Cluster ungefähr: 

```text
32KB * 32 = 1024KB
```

Das sind ungefähr 1 MB. 

Auswirkung: 

- Wird die Granularität der Speicherzuweisung der Datendatei beeinflussen. 
- Es wird nicht empfohlen, nach der Initialisierung Änderungen vorzunehmen. 

### 7. `CASE_SENSITIVE` 

Beispiel: 

```text
CASE_SENSITIVE=0
```

Bedeutung: 

`CASE_SENSITIVE` zeigt an, ob die Namen der Datenbankobjekte groß-/klein-geschrieben werden.

Gängige Werte: 

```text
0:CASE_INSENSITIVE
1:CASE_SENSITIVE
```

Beispielsweise können bei Groß-/Kleinschreibungsunempfindlichkeit die folgenden beiden Tabellennamen als dasselbe Objekt betrachtet werden:

```text
user
USER
```

Auswirkung: 

- Wird die Erkennung von Tabellennamen, Feldnamen und Objektnamen beeinflussen. 
- Für MySQL Migration oder MySQL-kompatible Szenarien, es wird normalerweise bevorzugt, es als `0`. 
- Es wird nicht empfohlen, es nach der Initialisierung zu ändern. 

### 8. `UNICODE_FLAG` 

Beispiel: 

```text
UNICODE_FLAG=1
```

Bedeutung: 

`UNICODE_FLAG` ist eine Zeichensatzkonfiguration.

Gängige Werte: 

```text
0:GB18030
1:UTF-8
2:EUC-KR
```

`UNICODE_FLAG=1` zeigt an, dass die Datenbank den UTF-8 Zeichensatz verwendet.

Empfehlung:

- Es wird empfohlen, UTF-8 für neue Systeme.
- Bessere Kompatibilität mit chinesischen, englischen und mehrsprachigen Zeichen.
- Es wird nicht empfohlen, nach der Initialisierung Änderungen vorzunehmen.

### 9. `SYSDBA_PWD`

Beispiel:

```text
SYSDBA_PWD=<SYSDBA_PASSWORD>
```

Bedeutung: 

`SYSDBA_PWD` ist das PASSWORD für das `SYSDBA` Administrator-Konto.

`SYSDBA` ist ähnlich wie ein Datenbank-Superadministrator und hat hochrangige Berechtigungen.

Empfehlung: 

- Verwenden Sie ein starkes PASSWORD.
- Verwenden Sie keine einfachen PASSWORTe wie `SYSDBA`, `123456`, `password`.
- PASSWORD Die Länge sollte mindestens 8 Zeichen betragen und Buchstaben und Zahlen enthalten.
- Schreiben Sie das tatsächliche PASSWORD nicht in externe Dokumente.

### 10. `SYSAUDITOR_PWD`

Beispiel: 

```text
SYSAUDITOR_PWD=<SYSAUDITOR_PASSWORD>
```

Bedeutung: 

`SYSAUDITOR_PWD` ist das PASSWORD von dem `SYSAUDITOR` Audit-Administratorkonto.

`SYSAUDITOR` Wird hauptsächlich für auditbezogene Verwaltungsmöglichkeiten verwendet.

Empfehlung:

- Verwenden Sie ein PASSWORD anders als `SYSDBA`.
- Verwenden Sie ein starkes PASSWORD das die Komplexitätsanforderungen erfüllt.

### 11. `COMPATIBLE_MODE`

Beispiel:

```text
COMPATIBLE_MODE = 4
```

Bedeutung: 

`COMPATIBLE_MODE` ist die Kompatibilitätsmodus-Konfiguration der Dameng Datenbank, die verwendet wird, um zu steuern, mit welchem Typ von Datenbank die Datenbank in Bezug auf SQL Syntax, Funktionen und bestimmte Verhaltensweisen übereinstimmt.

Gängige Bedeutungen der Werte: 

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

Dieser Text ist konfiguriert als: 

```text
COMPATIBLE_MODE = 4
```

Zeigt die Aktivierung von MySQL Kompatibilitätsmodus aktiviert hat. 

Funktion: 

- Verbessert die Kompatibilität der MySQL SQL Syntax in Dameng. 
- Reduziert die Transformationskosten beim Migrieren von MySQL oder Anpassen an Dameng. 

Hinweis: 

- Diese Konfiguration bedeutet nicht, dass Dameng unterstützt das MySQL Protokoll. 
- Programme müssen den Dameng Treiber intern weiterhin verwenden; wenn es auf der Seite keine Treiberkonfigurationsoption gibt, müssen Benutzer diese nicht separat ausfüllen. 
- Nach der Änderung ist ein Neustart des Datenbankdienstes erforderlich. 
- Ob es letztendlich wirksam wird, sollte auf den `v$dm_ini` Abfrageergebnissen basieren. 

### 12. `ORDER_BY_NULLS_FLAG` 

Beispiel: 

```text
ORDER_BY_NULLS_FLAG = 0
```

Bedeutung: 

`ORDER_BY_NULLS_FLAG` wird verwendet, um zu steuern, ob NULL Werte am Anfang oder Ende erscheinen, wenn nach `ORDER BY`. 

sortiert wird. Warum es wichtig ist: 

Verschiedene Datenbanken können unterschiedliche Standardverhaltensweisen für das Sortieren von NULL-Werten haben. Beim Migrieren einer Anwendung von MySQL zu Dameng, wenn die Sortierergebnisse von der Position der NULL-Werte abhängen, kann dieser Parameter die Reihenfolge der Abfrageergebnisse beeinflussen. 

Dieser Artikel ist wie folgt konfiguriert: 

```text
ORDER_BY_NULLS_FLAG = 0
```

Zweck ist es, das Sortierverhalten näher an die MySQL Nutzungsgewohnheiten anzupassen.

Hinweis:

- Nach der Änderung ist ein Neustart des Datenbankdienstes erforderlich.
- Wenn das Unternehmen SQL bereits ausdrücklich angibt `NULLS FIRST` oder `NULLS LAST`, sollte das im SQL angegebene Verhalten Vorrang haben.

## Anhang 2, Häufig gestellte Fragen

### 1. Warum kann ich mich nicht verbinden mit einem MySQL Client auch nach dem Einstellen des MySQL Kompatibilitätsmodus?

Weil MySQL der Kompatibilitätsmodus nur SQL Syntax und einige Datenbankverhalten beeinflusst, ändert er Damengdas Netzwerkprotokoll nicht.

Wenn Anwendungen oder Werkzeuge sich verbinden mit Dameng, muss der Dameng Treiber weiterhin verwendet werden:

```text
dm.jdbc.driver.DmDriver
jdbc:dm://<host>:5236
```

kann nicht verwendet werden: 

```text
com.mysql.cj.jdbc.Driver
jdbc:mysql://<host>:5236
```

### 2. Wie kann man bestätigen, dass die Konfiguration wirklich wirksam geworden ist?

Schauen Sie nicht nur auf die `dm.ini` Datei; es wird empfohlen, sich in die Datenbank einzuloggen, um den Laufzeitstatus zu überprüfen:

```sql
select para_name, para_value
from v$dm_ini
where para_name in ('COMPATIBLE_MODE', 'ORDER_BY_NULLS_FLAG');
```

Der laufende Zustand ist nur dann wirksam, wenn die folgenden Ergebnisse gesehen werden: 

```text
COMPATIBLE_MODE      4
ORDER_BY_NULLS_FLAG  0
```

### 3. Warum wird die Änderung nach der Modifikation nicht wirksam `dm.ini`?

Häufige Gründe:

- Der Datenbankdienst wurde nach der Änderung nicht neu gestartet.
- Es gibt doppelte Konfigurationseinträge in der Datei.
- Die geänderte Datei ist nicht die `dm.ini` aktuell von der Instanz verwendet.

Sie können bestätigen, welche Konfigurationsdatei die aktuelle Instanz verwendet, über den Befehl zum Starten des Dienstes:

```bash
systemctl status DmServiceDBSERVER.service --no-pager
```

In der Ausgabe sehen Sie im Allgemeinen etwas wie das Folgende:

```text
dmserver path=/dmdata/data/DMTEST/dm.ini -noconsole
```

### 4. Was soll ich tun, wenn ein PASSWORD Komplexitätsfehler während der Initialisierung auftritt?

Zeigt an, dass der PASSWORD zu einfach ist. Bitte ändern Sie zu einem komplexeren PASSWORD, zum Beispiel:

```text
AT_LEAST 8 POSITION
CONTAINS_LETTERS_AND_NUMBERS
AVOID_USING_THE_ACCOUNT_NAME_ITSELF
```

### 5. Können diese Parameter später geändert werden? 

Nein. 

Initialisierungsparameter sollten im Allgemeinen später nicht geändert werden, zum Beispiel: 
- 'PAGE_SIZE'
- 'EXTENT_SIZE'
- 'CASE_SENSITIVE'
- 'UNICODE_FLAG'
- 'DB_NAME'
- 'INSTANCE_NAME'

Wenn diese Parameter falsch konfiguriert sind, wird normalerweise empfohlen, die Datenbank neu zu initialisieren und die Daten erneut zu migrieren. Der 

'dm.ini' Parameter kann später angepasst werden, zum Beispiel: 

- 'COMPATIBLE_MODE'
- 'ORDER_BY_NULLS_FLAG'
- 'PORT_NUM'

Nach einer Änderung muss der Datenbankdienst in der Regel neu gestartet werden. 

## Anhang 3: Endgültige Checkliste 


- Das Datenverzeichnis '/dmdata/data' wurde erstellt. 
- Der Host des Datenverzeichnisses ist 'dmdba:dinstall'. 
- Die Datenbank wurde erfolgreich initialisiert. 
- '/dmdata/data/DMTEST/dm.ini' existiert. 
- `COMPATIBLE_MODE = 4`. 
- `ORDER_BY_NULLS_FLAG = 0`. 
- Datenbankdienst `DmServiceDBSERVER.service` ist `active`. 
- Port `5236` wird überwacht. 
- `SYSDBA` kann sich bei der Datenbank anmelden. 
- In `v$dm_ini`, der Laufzeitwert von `COMPATIBLE_MODE` ist `4`.
