# Datensicherung

[← ShimoDocs Suite Bereitstellungsdokumentation](../README.md)

Dieses Dokument erklärt den Umfang der Datensicherung, die Anforderungen an die Wiederherstellung, die Ausführungsmethoden und die Punkte zur Überprüfung nach der Wiederherstellung für die ShimoDocs privatisierte Umgebung.

Dieses Dokument deckt die folgenden Inhalte ab:

* Umfang der Sicherung und Verantwortungslinien

* Datenbanksicherungs- und Wiederherstellungsanforderungen

* Anforderungen an die Sicherung und Wiederherstellung von Objektspeichern

* Bestätigungspunkte vor der Wiederherstellung

* Überprüfungspunkte nach der Wiederherstellung

Dieses Dokument deckt die folgenden Inhalte nicht ab:

* Schritte zur Erstinstallation und Bereitstellung

* Upgrade- und Migrationspläne

* Anleitungen für Wiederherstellungstools von Drittanbietermiddleware

* Prozess zur Handhabung von Produktionsvorfällen

# 2. Umfang der Sicherung und Verantwortungslinien

## 2.1 Umfang der Sicherung

Die Daten, die in den Sicherungsbereich in der ShimoDocs privatisierten Umgebung einbezogen werden müssen, umfassen:

* MySQL Daten

* MongoDB Daten

* Redis Daten

* Objektspeicherdaten 

* Installationskonfiguration und Umgebungsparameterdateien 

Datenverzeichnisse, Sicherungsverzeichnisse und Aufbewahrungsfristen der Sicherungen werden einheitlich von der Kundenseite verwaltet. 

## 2.2 Verantwortungslinien 

Die Grenzen der Sicherungs- und Wiederherstellungsverantwortlichkeiten sind wie folgt: 

* Die Kundenseite ist verantwortlich für die Erstellung und Durchführung formaler Sicherungsrichtlinien 

* Die Kundenseite ist verantwortlich für die Aufbewahrung der Sicherungsdateien, die Sicherheit der Medien und die Verwaltung der Aufbewahrungsfristen 

* Die Kundenseite ist verantwortlich für Wiederherstellungsübungen, Wiederherstellungsfreigaben und die Abnahme der Wiederherstellungsergebnisse 

* ShimoDocs kann technische Unterstützung und Anleitungen für Wiederherstellungsoperationen bereitstellen 

Wenn externe Middleware, selbst erstellter Objektspeicher oder vom Kunden verwaltete Infrastruktur beteiligt sind, wird die Backup- und Wiederherstellungsstrategie vollständig von der Kundenseite übernommen. 

# 3. Bestätigung vor der Ausführung der Wiederherstellung 

Die Datenwiederherstellung ist ein risikoreicher Vorgang. Die folgenden Bestätigungen müssen vor der Ausführung abgeschlossen werden. 

## 3.1 Zielbestätigung 

Vor der Wiederherstellung sind die folgenden Informationen zu klären: 

* Zielumgebung 

* Zielcluster, Knoten, NAMESPACE 

* Umfang der wiederherzustellenden Daten 

* Wiederherstellungspunkt in der Zeit 

* Ausführungsfenster 

## 3.2 Risikobestätigung

Bestätigen Sie vor der Wiederherstellung die folgenden Punkte:

* Ob diese Wiederherstellung die aktuellen Online-Daten überschreibt

* Ob diese Wiederherstellung Ausfallzeiten erfordert

* Ob das neueste Backup zu den aktuellen Online-Daten hinzugefügt wurde

* Ob der Rollback-Punkt nach einer fehlgeschlagenen Wiederherstellung geklärt wurde

## 3.3 Backup-Gültigkeitsbestätigung

Überprüfen Sie vor der Wiederherstellung Folgendes:

* Backup-Dateien sind vollständig und lesbar

* Der Sicherungszeitpunkt erfüllt die Wiederherstellungsziele

* Das Sicherungsverzeichnis ist korrekt eingebunden

* Alle für die Wiederherstellung erforderlichen Konfigurationsdateien sind vollständig

* Sicherungsdateien haben die Überprüfung der Wiederherstellbarkeit bestanden

# 4. Sicherungsstrategie

## 4.1 Datenbanksicherung

Die Kriterien für die Datenbanksicherung sind wie folgt:

|Szenario|Ausführungsmethode|Häufigkeit|Aufbewahrungszeitraum|Beschreibung|
|:----|:----|:----|:----|:----|
|Verwendet ShimoDocs eingebettete Middleware|Systemgeplante Sicherung|Einmal täglich|7 Tage|Ausgeführt durch geplante Aufgaben innerhalb des Clusters|
|Verwendung von vom Kunden selbst verwalteter Middleware|Sicherung auf Kundenseite|Einmal täglich oder öfter|7 Tage oder länger|Ausführen gemäß Richtlinie des Kunden|



Datenbanksicherung muss mindestens abdecken:

* MySQL

* MongoDB

* Redis

## 4.2 Objektspeicher-Sicherung

Kriterien für die Objektspeicher-Sicherung sind wie folgt:

|Datentyp|Ausführungsmethode|Häufigkeit|Aufbewahrungszeitraum|Beschreibung|
|:----|:----|:----|:----|:----|
|Objektspeicher-Geschäftsdaten|Kaltbackup oder Disaster-Recovery-Replikation|Ausführen gemäß Geschäftsebene|Ausführen gemäß Kundenrichtlinie|Deckt Dokumentanhänge und Dateiobjekte ab|
|Objektspeicher-Konfigurationsdaten|Konfigurationssicherung|Synchronisierte Sicherung nach Änderungen|Ausführen gemäß Kundenrichtlinie|Deckt Zugriffsparameter und Mount-Informationen ab|



Mehrere Kopien im Objektspeicher sind Teil des Redundanzmechanismus des Clusters und entsprechen nicht einer Datensicherung.

## 4.3 Konfigurationsdatei-Backup

Die folgenden Konfigurationen sind im Backup-Umfang enthalten:

* Installationsparameter

* Domänen- und Protokollkonfiguration

* Adressen externer Abhängigkeiten und Portinformationen

* Zugangsinformationen zum Objektspeicher 

* Geschäftsbezogene Konfigurationsdateien 

# 5. Datenbankwiederherstellung 

Dieser Abschnitt gilt für alle Datenwiederherstellungen für MySQL, MongoDBund Redis. 

## 5.1 Vorbereitung vor der Wiederherstellung 

Führen Sie die folgenden Vorbereitungen durch, bevor Sie die Datenbankwiederherstellung durchführen: 

* Bereiten Sie ein Wiederherstellungsverzeichnis auf dem Zielknoten vor, zum Beispiel, `/data/restore` 

* Legen Sie die wiederherzustellenden Daten in das Wiederherstellungsverzeichnis 

* Überprüfen Sie, ob die Middleware-Konfiguration in der `global_config.json` Datei mit der aktuellen Umgebung übereinstimmt 

* Überprüfen Sie den Wiederherstellungsknoten, den Wiederherstellungspunkt, das Ausführungsfenster und die Genehmigungsinformationen 

## 5.2 Überprüfung der Sicherungsaufgabe 

Überprüfen Sie die geplanten Datenbanksicherungsaufgaben: 

```plain
kubectl get cronjob
```


Zeichnen Sie auch die folgenden Informationen auf:

* CronJob-Name

* Letzte Ausführungszeit

* Neuester Ausführungsergebnis

* Verzeichnis zur Speicherung der Sicherungsdatei

## 5.3 Ausführung fortsetzen

Die Datenbankwiederherstellung wird über einen einmaligen Job durchgeführt, und das Wiederherstellungsskript befindet sich im Backup-Image.

Die Ausführungsschritte sind wie folgt:

1. Stellen Sie die Wiederherstellungsaufgabenliste zusammen `db-restore.yaml`

2. Ändern Sie `spec.template.spec.nodeName` zu dem Knoten, auf dem sich das Wiederherstellungsverzeichnis befindet

3. Ändern Sie `hostPath.path` zu dem Verzeichnis, in das die Daten wiederhergestellt werden

4. Führen Sie den `kubectl apply -f db-restore.yaml` Befehl zur Datenwiederherstellung aus

Die Beispielaufgabenliste ist wie folgt:

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  labels:
    job-name: db-restore
  name: db-restore
spec:
  template:
    metadata:
      labels:
        job-name: db-restore
      name: db-restore
    spec:
      containers:
      - command:
        - /bin/sh
        - -c
        - cd /data/pri-init/scripts/backup && sh restore_all.sh
        image: registryo.shimo.im/smbase/backup:co
        imagePullPolicy: Always
        name: db-restore
        volumeMounts:
        - name: db-config
          mountPath: /data/pri-init/scripts/global_config.json
          subPath: global_config.json
        - name: data
          mountPath: /backup
      dnsPolicy: ClusterFirst
      nodeName: master-1
      volumes:
      - name: db-config
        configMap:
          name: init-invoker
          items:
          - key: global_config.json
            path: global_config.json
      - name: data
        hostPath:
          path: /data/restore
      imagePullSecrets:
      - name: ee
      restartPolicy: Never
      schedulerName: default-scheduler
```


## 5.4 Ausführungsanweisungen

Nach der Ausführung der Datenbank-Wiederherstellungsaufgabe werden die folgenden Daten zurückgesetzt:

* MySQL

* MongoDB

* Redis

Während der Wiederherstellungsperiode können Geschäftsdaten überschrieben werden. Vollständige Stilllegungsmaßnahmen und Datenüberprüfung vor der Ausführung.

# 6. Objektstorage-Wiederherstellung

Dieser Abschnitt gilt für MinIO und S3-kompatible Objektstorage-Wiederherstellung.

## 6.1 Sicherungsmethoden

Die gängigen Sicherungsmethoden für Objektstorage sind wie folgt:

|Methode|Anwendbares Szenario|Beschreibung|
|:----|:----|:----|
|Rsync-Synchronkopie|Standalone-Umgebung|Geeignet für Verzeichnisebene Kaltbackup|
|Disk-Snapshot|Standalone-Umgebung|Geeignet für schnelle Wiederherstellung auf derselben Speicherplattform|
|`mc mirror`|Standalone- oder Clusterumgebung|Geeignet für Objekt-Daten Kaltbackup und Wiederherstellung|
|Site-Replikation / Bucket-Replikation|Clusterumgebung|Geeignet für Disaster-Recovery-Replikation|



## 6.2 Ausführung fortsetzen

Die in einer Standalone-Umgebung häufig verwendeten Wiederherstellungsmethoden sind wie folgt:

* Beim Verwenden von Rsync für die Sicherung führen Sie eine Rücksynchronisation durch, um das Datenverzeichnis wiederherzustellen

```plain
rsync -av backup:/data/minio/ /data/minio/
```


* Beim Verwenden von `mc mirror` für die Sicherung führen Sie eine umgekehrte Spiegelwiederherstellung durch

```plain
mc mirror backup-minio/ new-minio/
```


Die Wiederherstellungsrichtlinien für die Clusterumgebung sind wie folgt:

* Wenn eine Disaster-Recovery-Kopie vorhanden ist, führen Sie die Wiederherstellung gemäß dem Primär-Standby-Switch-Plan durch

* Beim Verwenden von Cold Backup führen Sie die Wiederherstellung gemäß dem Objekt-Speicher-Datenverzeichnis oder dem Inhalt des Image-Repositorys durch

## 6.3 Ausführungsanweisungen

Vor der Wiederherstellung des Objektspeichers müssen folgende Punkte bestätigt werden:

* Wiederherstellungsziel-Bucket-Bereich

* Wiederherstellungspunkt

* Ob das Online-Objekt überschrieben werden soll

* Zielspeicherpfad und Berechtigungskonfiguration

* ACCESS_DOMAIN und Gateway-Konfiguration nach der Wiederherstellung

# 7. Überprüfung nach der Wiederherstellung

Nach Abschluss der Wiederherstellung mindestens Folgendes überprüfen:

* Datenbankdienststatus ist normal

* Objektspeicherdienststatus ist normal

* Über das Admin-Panel verwaltbar

* Benutzeranmeldung ist normal

* Kern-Dokumente können normal erstellt, bearbeitet, gespeichert, importiert und exportiert werden

* Der Daten-Wiederherstellungspunkt entspricht den Erwartungen

Nach Abschluss der Wiederherstellung die folgenden Informationen aufzeichnen:

* Wiederherstellungs-Ausführungszeit

* Daten-Wiederherstellungszeitpunkt

* Ausführende Person, Genehmiger, Prüfer

* Nach der Wiederherstellung gefundene Probleme
