# Middleware-Konfiguration

[← ShimoDocs Suite Bereitstellungsdokumentation](../../../README.md)

## 1. Überblick 

Die Middleware-Konfiguration ist die Seite in der MDP Operations-Plattform, die sich mit verschiedenen Speicher- und Middleware-Lösungen in der Kundenumgebung integriert und zentrale Verwaltung der Verbindungsinformationen für Komponenten wie S3 Objektspeicher, RedisNachrichtenwarteschlange, Kafkarelationale Datenbank, MySQLDokumentendatenbank, MongoDBund Volltextsuche Elasticsearchermöglicht. Konfigurationsänderungen werden über asynchrone Aufgaben an die Kundenumgebung ausgegeben, mit Echtzeitanzeige des Test- und Freigabefortschritts während des Änderungsprozesses. 

Hauptfunktionen: 

- Verbindungseinstellungen für jede Middleware konfigurieren (Endpunkt, Zugriffsschlüssel, USERNAMEPASSWORDusw.) 
- Zwischen verschiedenen Anbietern wechseln (S3 und OSS, AWS / MinIO / Tencent Cloud COS / Huawei Cloud OBS, MySQL und DM) Dameng) 
- Verfolgung von geänderten Werten in Formularen; nur geänderte Felder werden übermittelt 
- Jeder Konfigurationsabschnitt kann unabhängig getestet werden; Veröffentlichung ist nur nach erfolgreicher Überprüfung erlaubt 
- Ein-Klick-Veröffentlichung: Alle unbearbeiteten Feldkonfigurationen stapelweise einreichen und asynchron ausführen 

### 1.1 Anwendbare Benutzer 

| Rolle           | Häufige Operationen                                     | 
| -------------- | ---------------------------------------------------- | 
| Implementierungsingenieur | Füllen Sie die Middleware-Verbindungsinformationen während der Erstbereitstellung aus | 
| Betriebsdienst | Anmeldeinformationen ersetzen, Endpunktänderungen, Verbindungen testen | 
| Notfallreaktion | Backup-Middleware wechseln und Timeout-Konfigurationen ändern |

### 1.2 Operationen, die in diesem Modul nicht empfohlen werden 

Anbieterwechsel (wie S3 → OSS) sind Änderungen, die eine groß angelegte Migration von nachgelagerten Daten betreffen und nach dem Änderungsprozess behandelt werden sollten. Die stapelweise Anpassung von Verbindungsinformationen über mehrere Umgebungen wird in diesem Modul nicht abgedeckt; Sie müssen jede Umgebung einzeln betreten, um die Seite zu konfigurieren. Middleware-Kapazitätsplanung und Überwachungsalarme befinden sich nicht auf dieser Seite; bitte verwenden Sie das Cluster-Management- und Alarmkonfigurationsmodul. 

---

## 2. Detaillierte Erklärung jeder Middleware-Konfiguration 

### 2.1 S3 Objektspeicher 

**Operationsschritte**: Gehen Sie im linken Menü auf 'Middleware-Konfiguration', das standardmäßig in diesem Abschnitt ist → Scrollen Sie nach unten, um drei Konfigurationsabschnitte in folgender Reihenfolge zu sehen: Öffentliche S3 Instanzeinstellungen, Einstellungen für kollaboratives Bearbeiten S3 Instanzeinstellungen und Bucket-Einstellungen. 

#### 2.1.1 Öffentlich S3 und kollaboratives Bearbeiten S3

**Operationsschritte**: Zuerst füllen Sie das Öffentlichkeitsformular aus S3 Instanzeinstellungen, dann die Zusammenarbeit beim Bearbeiten ausfüllen S3 Instanzeinstellungen und füllen Sie schließlich die Bucket-Einstellungen aus. Nachdem Sie ein Feld geändert haben, klicken Sie unten auf "Verbindung testen".
Die Formularfelder in beiden Abschnitten sind konsistent:
| Feld               | Beschreibung                                                              | Erforderlich |
| ----------------- | ---------------------------------------------------------------------- | ---- |
| Speichertyp           | Dropdown auswählen 'S3 (Object Storage)' oder 'OSS (Alibaba Cloud)'                             | Ja   |
| Untertyp               | Dynamisch basierend auf dem Speichertyp geladen: Für S3, Optionen beinhalten AWS / Tencent Cloud COS / Huawei Cloud OBS / MinIO / Andere; für OSS, nur Alibaba Cloud OSS ist verfügbar | Ja    |
| Zugriffsschlüssel-ID     | Anmeldeinformationen Bezeichner                                                        | Ja   |
| Zugriffsschlüssel-Geheimnis | Anmeldeinformationsschlüssel, Eingabefeld PASSWORD maskiert | Ja |
| Region | Zum Beispiel `cn-north-1` | Ja |
| ForcePathStyle | Checkbox, ob Pfadstilzugriff aktiviert werden soll | Nein |
| SSL | Checkbox, ob HTTPS | Nein |
| Endpunkt | Interner Dienstzugriffsadresse aktiviert werden soll | Ja |
| Öffentliche Zugriffsadresse | Benutzerseitige Zugriffsadresse | Ja |
| Adressersetzungsregel | Regex oder Zeichenfolge zur Abbildung der internen Adresse auf die öffentliche Adresse | Ja |

#### 2.1.2 Bucket-Einstellungen 

**Operationsschritte**: Alle vom Server zurückgegebenen Buckets werden einzeln dargestellt, und Sie können den CDN Domainnamen nach Bedarf ausfüllen. 

| Feld       | Beschreibung                  |
| --------- | ------------------------- |
| Bucket-Name | Name des Buckets         |
| Präfix      | Objektspeicher-Pfadpräfix  |
| CDN Domain  | CDN Beschleunigungs-Domain     |
| Aktivieren CDN Authentifizierung | Checkbox, einmal aktiviert, werden zwei Elemente "Authentifizierungstyp" und "Authentifizierungsschlüssel" hinzugefügt |

> Nach dem Aktivieren CDN der Authentifizierung sind der entsprechende Authentifizierungstyp und Authentifizierungsschlüssel erforderlich. 

### 2.2 Redis
**Schritte**: Verwenden Sie die Schnellnavigation rechts, um auf das Redis Symbol zu klicken, um zu diesem Abschnitt zu scrollen → wählen Sie einen Modus → füllen Sie die Adresse aus und PASSWORD → klicken Sie auf "Verbindung testen". 

Feldbeschreibung:

| Feld     | Beschreibung                        |
| -----     | -----------------------------    |
| Modus      | Standalone oder Sentinel          |
| Adresse   | Verbindungsadresse im Standalone-Modus, z.B., `redis-sentinel-master-ss:6379` |
| Master-Name | Erforderlich im Sentinel-Modus, z.B., `mymaster` |
| Adressliste | Mehrere Adressen im Sentinel-Modus, können dynamisch hinzugefügt/entfernt werden |
| PASSWORD  | Erforderlich                          |

Ein Wechsel der Modi setzt automatisch die Felder Adresse, Master-Name und Adressliste zurück.

### 2.3 Kafka
**Schritte zur Bedienung**: Klicken Sie auf das Kafka Symbol in der Schnellnavigation rechts, um zu diesem Abschnitt zu scrollen → Füllen Sie die Broker-Adresse aus → Wenn SASL aktiviert ist, erweitern Sie die SASL Unterfelder → Klicken Sie auf „Verbindung testen“.

Feldbeschreibung:

| Feld         | Beschreibung                                         |
| ---------- | ------------------------------------------------ |
| Broker-Adresse | Array, kann dynamisch hinzugefügt/gelöscht werden        |
| Themen-Präfix    | Präfix wird automatisch allen Themen hinzugefügt         |
| Aktivieren SASL Authentifizierung | Schalter, bei Aktivierung werden drei SASL Konfigurationen hinzugefügt |
| Authentifizierungsmechanismus | PLAIN / SCRAM-SHA-256 / SCRAM-SHA-512 (nach Aktivierung SASL) |
| USERNAME / PASSWORD   | SASL Anmeldeinformationen (nach Aktivierung SASL)           |

### 2.4 MySQL (Relationale Datenbank)
**Operationsschritte**: Klicken Sie in der rechten Schnellnavigation auf das RDB Symbol und scrollen Sie zu diesem Abschnitt → wählen Sie MySQL oder DM Dameng → Host, Port ausfüllen, USERNAMEPASSWORD → Klicken Sie auf „Verbindung testen“.

Feldbeschreibung:

| Feld      | Beschreibung        |
| -------- | ---------------- |
| Datenbanktyp | MySQL oder DM (Dameng) |
| Host-Adresse  | Zum Beispiel `mysql-master` |
| Port          | 3306           |
| USERNAME / PASSWORD | Anmeldeinformationen      |

> Das „RDB Relationale Datenbank“ im rechten Menü und Seitentitel entspricht dem MySQL Konfigurationsabschnitt.

### 2.5 MongoDB
**Operationsschritte**: Klicken Sie auf das MongoDB Symbol in der rechten Schnellnavigation, um zu diesem Abschnitt zu scrollen → Verbindungszeichenfolge ausfüllen → Jede Datenbank-Anmeldeinformation einzeln gemäß Serverkonfiguration umsetzen → Klicken Sie auf „Verbindung testen“.

Feldbeschreibung: 

| Feld           | Beschreibung                       |
| ------------- | -------------------------------- |
| Verbindungszeichenfolge | Zum Beispiel `mongo-master:27017` |
| Jede Datenbank USERNAME / PASSWORD | Einzeln für die serverkonfigurierten Datenbanken umgesetzt |

### 2.6 Elasticsearch
**Schritte:** Verwenden Sie die schnelle Navigation auf der rechten Seite, um auf das Elasticsearch Symbol zu klicken und zu diesem Abschnitt zu scrollen → Füllen Sie die Host-Adresse und den Port aus → Wenn die Authentifizierung aktiviert ist, ausfüllen USERNAME und PASSWORD → Klicken Sie auf 'Verbindung testen'.

Feldbeschreibung: 

| Feld     | Beschreibung       | Erforderlich |
| ----      | --------------  | ----    |
| Host-Adresse | z.B., `elasticsearch` | Ja      |
| Port        | 9200             | Ja      |
| USERNAME    | ES-Zugangsdaten   | Nein       |
| PASSWORD    | ES-Zugangsdaten   | Nein       |

---

## 3. Häufige Operationen 

### 3.1 Aktualisierung der Zugangsdaten (z.B. Zugriffsschlüsselrotation) 

1. Gehen Sie zur Middleware-Konfiguration 
2. Ersetzen Sie die Access Key ID und das Access Key Secret in der S3 öffentlichen Karte 
3. Klicken Sie auf 'Verbindung testen' und warten Sie auf die grüne Nachricht 'Verbindungstest erfolgreich' 
4. Verbindungstest für andere geänderte Abschnitte wiederholen 
5. Klicken Sie unten auf "Konfiguration veröffentlichen" 
6. Das System meldet, dass eine asynchrone Aufgabe erstellt wurde, und leitet zum Aufgabenprotokoll-Tab weiter 

### 3.2 Wechseln von Middleware-Anbietern 

1. Gehen Sie zur Middleware-Konfiguration 
2. Ändern Sie in der entsprechenden Karte "Speichertyp / Subtyp" sowie den Endpunkt, den Zugriffsschlüssel, die Adressersetzungsregeln usw. des neuen Anbieters 
3. Nach der Änderung auf "Verbindung testen" klicken, um zu überprüfen 
4. Auf "Konfiguration veröffentlichen" klicken 

> Der Wechsel des Anbieters erfordert das Neuladen des Verbindungspools für nachgelagerte Dienste, daher bitte Stoßzeiten vermeiden; nach dem Wechsel wird empfohlen, die Anwendungsprotokolle 5 bis 10 Minuten zu überwachen 

### 3.3 Aktivieren Kafka SASL 

1. Gehe zur Middleware-Konfiguration und finde die Kafka Abschnitt 
2. Schalten Sie die "Aktivieren" ein SASL Authentifizierungs“-Schalter und der SASL Felder werden erweitert 
3. Füllen Sie den Authentifizierungsmechanismus aus, USERNAMEund PASSWORD 
4. Klicken Sie auf "Verbindung testen" 
5. Nach dem Bestehen klicken Sie auf "Konfiguration veröffentlichen" 

### 3.4 Wiederherstellung nach Fehlbedienung 

Bevor Sie auf "Konfiguration veröffentlichen" klicken, wird der Formularstatus im localStorage des Browsers gespeichert. Er kann auf folgende Weise wiederhergestellt werden: 

- Klicken Sie auf die Schaltfläche "Alle zurücksetzen" am unteren Ende, und alle Felder werden auf die ursprünglichen Serverwerte zurückgesetzt. 

### 3.5 Asynchrones Aufgaben-Tracking 

Nach erfolgreicher Veröffentlichung der Konfiguration springt das System zum Aufgabenprotokoll-Tab, um den Aufgabenfortschritt anzuzeigen. Aufgaben können kurz oder lang sein, abhängig von der Anzahl der Middleware-Instanzen und der Anzahl der geänderten Felder. 

--- 

## 4. Häufige Probleme 

**F1: Klicken Redis in der oberen rechten Schnellnavigation hat keine Reaktion.**

Die Schnellnavigation auf der rechten Seite scrollt nur zu dem entsprechenden Abschnitt. Wenn dieser Abschnitt nicht auf der aktuellen Seite angezeigt wird (z. B. durch ein Popup blockiert), können Sie die Seite scrollen oder erneut auf das Redis Symbol in der rechten Navigation klicken, um die Position neu zu setzen.

**F2: Nach der Veröffentlichung der Konfiguration scheint sich der Status nicht zu aktualisieren.**

Die Seite wird nach der Veröffentlichung automatisch aktualisiert. Wenn der Browser nicht automatisch aktualisiert, können Sie F5 manuell drücken, um die neueste Konfiguration abzurufen.

**F3: Die Zahl in 'N Konfigurationen geändert' stimmt nicht mit der tatsächlichen Anzahl überein.**

Die Seite zählt basierend auf den Feldern mit veränderten Werten im Formular. In bestimmten Situationen, z. B. nach einem Zurücksetzen und anschließender Änderung oder beim dynamischen Hinzufügen/Entfernen von Array-Elementen, kann dies zu Zählabweichungen führen. Sie können auf 'Alle zurücksetzen' klicken und die Einträge erneut ausfüllen.

**F4: Die Bucket-Einstellungen-Karte kann den Bucket, den ich hinzufügen möchte, nicht finden.**

Die Seite rendert bestehende Buckets basierend auf der serverseitigen Konfiguration. Das Hinzufügen eines neuen Buckets erfordert die Änderung der zugrunde liegenden Serverkonfigurationsdatei, nicht dieser Seite. Wenn dies erforderlich ist, wenden Sie sich bitte an den Implementierungsingenieur.

---

## Anhang A: Terminologie-Referenz

| Begriff       | Erklärung                                                                |
| -------- | ----------------------------------------------------------------- |
| Serverkonfigurationsdatei | Die endgültige Konfigurationsquelle für die Plattformwartung, gebildet durch das Zusammenführen der Standardwerte der Plattform mit den auf dieser Seite angegebenen Werten |
| Bucket   | Speicher-Buckets in S3 / OSS |
| Endpunkt | Middleware-Servicenadresse, verwendet für den internen Clusterzugriff |
| Öffentliche Zugriffsadresse | Für Benutzer sichtbare Middleware-Adresse |
| Adressersetzungsregeln | Eine interne Adresse auf ein Regex oder eine Zeichenkette einer öffentlichen Adresse abbilden |
| SASL     | Simple Authentication and Security Layer, Authentifizierungsmechanismen für Komponenten wie Kafka |
| Sentinel | Einer von Rediss Hochverfügbarkeitsplänen |
| DM       | Dameng Datenbank (heimische relationale Datenbank)                                             |
| Dirty Field       | Felder im Formular, die geändert wurden und sich vom Anfangswert unterscheiden          |
