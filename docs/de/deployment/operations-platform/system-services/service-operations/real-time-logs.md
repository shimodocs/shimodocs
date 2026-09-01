# Echtzeit-Logs

[← ShimoDocs Suite Bereitstellungsdokumentation](../../../README.md)

## Funktionsübersicht

Echtzeitprotokolle werden verwendet, um die Betriebsprotokolle von Diensten in einem Kubernetes Cluster online anzuzeigen, sodass Sie Dienstanomalien, Anfragefehler und Ausführungsverzögerungen schnell lokalisieren können.

Hauptanwendungsfälle:
- Echtzeitprotokolle schnell filtern
- Eine leichtgewichtige Alternative, wenn ein vollständiges Protokollierungssystem nicht bereitgestellt ist

Hinweis: Echtzeitprotokolle werden über das Kubernetes APIabgerufen, und Protokolldaten können durch Kubernetes Rolling-Updates beeinflusst werden.

## Zugriff auf die Seite

Nach dem Einloggen in die Managementkonsole wählen Sie **Echtzeitprotokolle** im linken Navigationsbereich, um die Seite aufzurufen.

Echtzeitprotokolle werden nur im Kubernetes Bereitstellungsmodus unterstützt. Wenn Sie dieses Menü nicht sehen, wenden Sie sich bitte an Ihren Systemadministrator, um den Bereitstellungsmodus und die Zugriffsrechte Ihres aktuellen Kontos zu bestätigen.

## Protokolle abfragen

Es wird empfohlen, folgende Schritte zu befolgen:

1. Wählen Sie das **Cluster** und **NAMESPACE** den Sie abfragen möchten.
2. Wählen Sie das Protokollziel aus, unterstützt Deployment, StatefulSet oder Pod, und mehrere Ziele können gleichzeitig ausgewählt werden.
3. Wählen Sie den Protokollbereich aus, die Abfrage kann die letzten 100 Zeilen, 1000 Zeilen, 5000 Zeilen oder Protokolle der letzten 1 Minute bis 24 Stunden umfassen.
4. Um die Abfrageergebnisse einzugrenzen, können Sie Filterbedingungen auf Zeilenebene eingeben.
5. Klicken Sie **Start** und die Seite lädt Protokolle innerhalb des ausgewählten Bereichs und zeigt kontinuierlich neu erzeugte Protokolle an.

Klicken Sie **Stop** um das Echtzeit-Pulling zu beenden. Wenn die Abfrage neu gestartet wird, werden die Protokolle auf der aktuellen Seite gelöscht und neue Abfrageergebnisse geladen.

## Protokollfilterung

Filter auf Zeilenebene ist nicht groß-/kleinschreibungssensitiv für englische Buchstaben. Drücken Sie nach Eingabe der Bedingungen die Eingabetaste, um sie anzuwenden. Häufige Verwendungsweisen sind wie folgt:

```text
error
error AND timeout
error OR warning
error NOT health
error AND (timeout OR deadline)
"connection refused"
```

- `AND`: Schließt mehrere Bedingungen gleichzeitig ein. 
- `OR`: Schließt eine der Bedingungen ein. 
- `NOT`: Schließt angegebenen Inhalt aus. 
- `()`: Kombiniert mehrere Filterbedingungen. 
- `""`: Sucht nach dem gesamten Ausdruck mit Leerzeichen. 

Sie können auf die Hilfsschaltfläche rechts neben dem Eingabefeld klicken, um vollständige Syntaxbeispiele zu sehen. Sie können auch **Häufige Abfragen** verwenden, um voreingestellte Protokollziele und Filterbedingungen schnell auszufüllen. 

## Protokolle anzeigen 

Die Protokollliste zeigt die Protokollzeit, POD_NAMEund den Protokollinhalt an. 

- Klicken Sie auf POD_NAME um den vollständigen Namen zu kopieren. 
- Wenn der Inhalt lang ist, können Sie ihn erweitern, um das vollständige Protokoll anzuzeigen. 
- Protokolle in JSON Format können in formatierten Inhalt erweitert werden und unterstützen das Kopieren mit einem Klick. 
- Wenn es viele Protokolle gibt, paginiert die Seite automatisch, und Sie können schnell mit den Schaltflächen oben in der Liste zum Anfang oder Ende springen. 

## Protokollmengenverteilung 

Das Diagramm zur Protokollmengenverteilung auf der Seite zeigt die Anzahl der Protokolle in verschiedenen Zeiträumen und zeigt die Gesamtzahl der Protokollzeilen sowie die Anzahl der nach dem Filtern übereinstimmenden Zeilen an. 

Sie können im Verteilungsdiagramm einen Zeitraum durch Ziehen auswählen, und die Protokollliste zeigt nur den Inhalt innerhalb dieses Zeitraums an, was sich eignet, um schnell Perioden plötzlicher Protokollspitzen oder Anomalien zu fokussieren. 

## Seitenoperationen 

- **Start**: Protokolle basierend auf den aktuellen Bedingungen abrufen und kontinuierlich neue Protokolle empfangen. 
- **Stop**: Empfang neuer Protokolle stoppen; bereits geladene Protokolle bleiben auf der Seite.
- **Löschen**: Die aktuell angezeigten Protokolle löschen; wenn das Echtzeit-Abrufen fortgesetzt wird, erscheinen weiterhin neue Protokolle.

## Häufige Situationen

- **Noch keine Protokolle**: Bitte stellen Sie sicher, dass der Zielservice läuft, und versuchen Sie, den Protokollzeitraum zu erweitern.
- **Kein Ziel ausgewählt**: Bitte wählen Sie mindestens eine Deployment-, StatefulSet- oder Pod-Ressource aus.
- **Zu viele Ziele**: Eine einzelne Abfrage unterstützt maximal 20 tatsächliche Pods; bitte reduzieren Sie die Auswahl und versuchen Sie es erneut.
- **Ungültige Filterbedingungen**: Bitte überprüfen Sie, ob `AND`, `OR`, `NOT`, Klammern oder Anführungszeichen vollständig sind.
- **Protokollabruf unterbrochen**: Dies kann durch einen Neustart des Dienstes, Änderungen im Netzwerk oder unzureichende Berechtigungen verursacht werden. Sie können erneut klicken **Start** .

> Die Seite speichert bis zu 500.000 Zeilen Protokolle. Sobald das Limit überschritten wird, werden ältere Protokolle automatisch entfernt.

## Beispiel für die Bedienelemente

Die Abbildung unten zeigt die Bereiche zur Auswahl der Arbeitslast, zur Schlüsselwortfilterung und zur Anzeige von Echtzeitprotokollen.

Klicken Sie **Cluster auswählen & NAMESPACE** um das Ziel-Cluster zu wechseln und NAMESPACE, dann die Arbeitslasten auswählen, die Sie anzeigen möchten.

