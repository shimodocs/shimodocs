# Installation Fehlerbehebung

[← ShimoDocs Suite Bereitstellungsdokumentation](../README.md)

> [!TIP]
>
> Häufige Probleme während der Installationsphase fallen normalerweise in die folgenden Kategorien.

## 1 Zeit-Desynchronisation

Problemsymptome:

* Anmeldefehler

* Authentifizierungsfehler

* Serviceaufruf-Ausnahmen

Bearbeitungsanforderungen:

* Zuerst die Zeitabweichung aller Knoten überprüfen

* Nach Behebung des NTP/Zeitsynchronisierungsdienstes mit der Installation oder Abnahme fortfahren

Untersuchungsbefehle:

```plain
timedatectl status
date
```


## 2 Fehler in der Konfiguration des Datenlaufwerkpfads

Phänomen:

* Nach der Installation füllt sich die Festplatte schnell

* Daten schreiben fehlgeschlagen

* Das persistente Verzeichnis befindet sich auf dem Systemlaufwerk

Bearbeitungsanforderungen:

* Das persistente Verzeichnis muss ausdrücklich auf das Datenlaufwerk zeigen

* Geschäftsdaten dürfen nicht im Verzeichnis des Systemlaufwerks gespeichert werden

Fehlerbehebungsbefehl:

```plain
findmnt -n -o TARGET -T /data
df -Th|egrep -v "overlay|tmpfs"
```


## 3 Verbindung zum abhängigen Dienst fehlgeschlagen

Phänomen:

* Service-Überprüfung schlägt während der Installation fehl

* Verbindung zu Datenbank, Cache, Nachrichtenwarteschlange oder Objektspeicher schlägt fehl

Bearbeitungsanforderungen:

* Zuerst überprüfen, ob Adresse, Port, Konto und PASSWORD korrekt eingegeben sind

* Anschließend Netzwerkverbindung und Sicherheitsrichtlinien prüfen

* Schließlich überprüfen, ob der Zielservice selbst verfügbar ist

Fehlerbehebungsbefehle:

```plain
nc -zv <MYSQL_HOST> 3306
nc -zv <REDIS_HOST> 6379
nc -zv <MONGO_HOST> 27017
nc -zv <KAFKA_HOST> 9092
```


## 4 Offline-Paket-Abgleich falsch

Phänomen:

* Mirror-Laden fehlgeschlagen

* Der Installationsprozess meldet, dass der Dienst nicht gestartet werden kann und die Version nicht übereinstimmt

* Das Installationspaket entspricht nicht dem Offline-Mirror-Paket

Bearbeitungsanforderungen:

* Bestätigen Sie, dass das Installationspaket, das Offline-Image-Paket und die Produktversion übereinstimmen

* Bestätigen Sie, ob das Installationspaket mit der CPU Architektur übereinstimmt

* Bestätigen Sie, dass Materialien aus verschiedenen Projekten oder verschiedenen Daten nicht vermischt werden

## 5. Installationsseite kann nicht geöffnet werden

Phänomen:

* Web-UI-Seite kann nicht aufgerufen werden

* Port 18080 hört nicht

* Der Installationsprozess wurde beendet

Fehlerbehebungsbefehl:

```plain
ps -ef | grep mdp | grep -v grep
ss -lntp | grep 18080
tail -n 100 /root/nohup.out
```


## 6. Empfohlene Reihenfolge zur Fehlerbehebung

Beheben Sie Installationsprobleme in folgender Reihenfolge:

1. Überprüfen Sie zunächst, ob es sich um ein Umweltproblem handelt: System, Zeit, Festplatte, Port, Netzwerk

2. Überprüfen Sie erneut, ob es sich um ein Konfigurationsproblem handelt: Domainname, Verzeichnis, Abhängigkeitsadresse, Konto PASSWORD

3. Überprüfen Sie erneut, ob es sich um ein Materialproblem handelt: Installationspaket, Offline-Paket, Architekturkompatibilität

4. Überprüfen Sie schließlich das Installationsprotokoll und den Dienststatus

Erklärung:

* Wiederholen Sie die Installation nicht, wenn die Voraussetzungen nicht erfüllt sind

* Führen Sie Befehle mit demselben expliziten Fehlgrund nicht wiederholt aus

## 7. Wann die Installation gestoppt werden sollte

Wenn die folgenden Situationen auftreten, stoppen Sie die Installation zunächst und setzen Sie sie erst fort, nachdem die zugrunde liegenden Probleme behoben wurden:

* Alle Knotenzeiten sind nicht synchron

* Datenfestplatte ist nicht eigenständig eingebunden

* Externer abhängiger Dienst ist unerreichbar

* Offline-Materialversion ist inkonsistent

* Der Installationsdienst wurde nicht ordnungsgemäß gestartet
