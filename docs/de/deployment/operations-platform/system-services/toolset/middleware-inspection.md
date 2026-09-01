# Middleware-Inspektion

[← ShimoDocs Suite Bereitstellungsdokumentation](../../../README.md)

## Funktionsübersicht 

Die Middleware-Inspektion wird verwendet, um zu prüfen, ob systemabhängige MySQL, Redis, Elasticsearch, S3, MongoDBund Kafka verbindungen normal hergestellt und Lese-/Schreiboperationen ausgeführt werden können, damit Sie zugrunde liegende Dienstanomalien rechtzeitig erkennen können. 

Die Seite unterstützt sofortige Inspektionen, geplante Inspektionen, kürzliche Verfügbarkeits-Trends, historische Aufzeichnungen sowie Benachrichtigungen über Ausfälle und Wiederherstellungen. 

## Seitenaufruf 

Nach dem Einloggen in das Verwaltungs-Backend wählen Sie **Middleware-Inspektion** im linken Navigationsbereich, um die Seite zu betreten. 

Middleware-Inspektionen sind nur für Administratoren zugänglich. Wenn Sie dieses Menü nicht sehen, wenden Sie sich bitte an den Systemadministrator, um Ihre Berechtigungen zu bestätigen. 

## Sofortige Inspektion 

Auf der **Übersicht** Seite, klicken Sie **Sofortige Inspektion**, und das System führt eine Inspektion gemäß den gespeicherten Prüfobjekten durch. 

Inspektionsergebnisse können die folgenden Status haben: 

- **Normal**: Komponentenverbindung und Inspektionsoperationen erfolgreich. 
- **Fehlgeschlagen**: Die Komponente kann nicht verbunden werden, Lesen/Schreiben fehlgeschlagen oder Antwort ist abnormal. 
- **Übersprungen**: Die Komponente ist in der aktuellen Umgebung nicht konfiguriert oder die Inspektionsbedingungen sind nicht erfüllt. 

Durch Klicken auf die Ergebnisse der Komponente können Sie die Zieladresse, die Reaktionszeit und Fehlermeldungen einsehen. 

## Verfügbarkeitstrends anzeigen 

Die Übersichtsseite zeigt die kürzliche Verfügbarkeit jeder Komponente basierend auf historischen Inspektionsergebnissen an. Es wird unterstützt, Statusänderungen der letzten 1 Stunde, 6 Stunden, 24 Stunden, 3 Tage, 7 Tage, 14 Tage oder 30 Tage anzuzeigen. 

Bewegen Sie die Maus über den Zeitraum, um Informationen wie die Anzahl der Inspektionen in diesem Zeitraum, die durchschnittliche Reaktionszeit und kürzliche Fehler anzuzeigen. 

## Geplante Inspektionen konfigurieren 

Auf der **Planung und Warnungen** Auf dieser Seite können Sie Folgendes einstellen: 

- **Geplante Inspektionen aktivieren**: Nach Aktivierung führt das System die Inspektionen automatisch in den festgelegten Intervallen aus. 
- **Inspektionsintervall**: Unterstützt 1 bis 1440 Minuten. 
- **Aufbewahrungstage der Historie**: Unterstützt 7 bis 365 Tage; die Einstellung auf `0` bedeutet keine automatische Bereinigung. 
- **Prüfziele**: Wählen Sie die Middleware aus, die geprüft werden soll. 
- **Benachrichtigungskanäle**: Wählen Sie die Kanäle aus, um Prüfbenachrichtigungen zu erhalten. 
- **Bei Fehler benachrichtigen**: Senden Sie eine Benachrichtigung, wenn sich der Gesamtstatus von normal zu abnormal ändert. 
- **Benachrichtigung bei Wiederherstellung**: Senden Sie eine Benachrichtigung, wenn ein abnormaler Zustand wieder normal wird. 

Änderungen müssen durch Klicken auf **Speichern**angewendet werden. Wenn noch kein Benachrichtigungskanal vorhanden ist, gehen Sie bitte zuerst auf die **Benachrichtigungskanal** Seite, um einen Kanal zu erstellen und zu aktivieren.

## Prüfverlauf anzeigen

Auf der **Historie** Seite, können Sie die Prüfzeit, die Auslöseart, die Ausführungsdauer und den Endstatus einsehen.

Auslösearten umfassen manuelle Prüfung und geplante Prüfung. Klicken Sie auf einen Eintrag, um die detaillierten Ergebnisse jeder Komponente dieser Prüfung einzusehen. 

## Häufige Situationen

- **Keine Prüfaufzeichnungen**: Sie können zuerst auf **Jetzt prüfen**klicken oder geplante Prüfungen aktivieren.
- **Komponente zeigt übersprungen**: Bitte bestätigen Sie, dass die entsprechende Middleware im System konfiguriert und aktiviert wurde.
- **Prüfung fehlgeschlagen**: Überprüfen Sie das Netzwerk, das Konto, die Verbindungsadresse und den Status des Middleware-Dienstes gemäß den Fehlerinformationen.
- **Benachrichtigung nicht empfangen**: Bitte bestätigen Sie, dass der Benachrichtigungskanal ausgewählt und aktiviert wurde, und überprüfen Sie die Schalter für Fehlerschaltungen oder Wiederherstellungsbenachrichtigungen.
- **Das Popup zeigt, dass die Inspektion läuft**: Es kann nur eine Inspektionsaufgabe gleichzeitig ausgeführt werden. Bitte warten Sie, bis die aktuelle Aufgabe abgeschlossen ist, und versuchen Sie es erneut.

> Inspektionen führen leichte Verbindungs- oder Lese-/Schreibprüfungen auf Middleware durch; es wird empfohlen, ein vernünftiges Inspektionsintervall basierend auf dem Umfang der Umgebung festzulegen.
