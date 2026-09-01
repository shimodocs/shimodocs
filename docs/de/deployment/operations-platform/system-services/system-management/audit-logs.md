# Prüfprotokolle

[← ShimoDocs Suite Bereitstellungsdokumentation](../../../README.md)

## Funktionsübersicht

Das Operationsprotokoll wird verwendet, um Benutzerverwaltungsoperationen im System zu betrachten und zu verfolgen, was bei Fehlerbehebung, Sicherheitsprüfung und Änderungsverfolgung hilft.

Diese Seite ist schreibgeschützt und unterstützt nicht das Ändern oder Löschen von Protokolleinträgen.

## Zugriff auf die Seite

Nach dem Einloggen in das Verwaltungs-Backend wählen Sie **Operationsprotokoll** im linken Navigationsbereich, um die Seite aufzurufen.

## Protokolle filtern

Sie können mithilfe der folgenden Kombination von Bedingungen abfragen:

- **Ereignisquelle**: Zum Beispiel Systemsteuerung, Anwendungs-Konfigurationszentrum, Updater, Kubernetes Ressourcenverwaltung oder Benutzerverwaltungszentrum.
- **Operationstyp**: Zeigt entsprechende Operationen basierend auf der Ereignisquelle an, wie Konfigurationsupdates, Versions-Upgrades, Dienstneustarts oder Benutzerverwaltungsaktionen.
- **Betriebsbenutzer**: Filtert Einträge, die von einem bestimmten Benutzer erstellt wurden.

Nachdem Sie die Ereignisquelle ausgewählt haben, passt sich die Liste der Operationstypen automatisch an. Klicken Sie **Suchen** um die Filterbedingungen anzuwenden, oder klicken Sie auf die Schaltfläche Zurücksetzen, um die Bedingungen zu löschen.

## Protokollliste anzeigen

Die Liste zeigt hauptsächlich:

- Protokoll-ID.
- Ereignisquelle und Operationstyp.
- Betriebsbenutzer.
- Typ, Name und ID des bearbeiteten Objekts.
- Operationszeit. 

Die Gesamtanzahl der Protokolle wird oben auf der Seite angezeigt, und die Liste unterstützt die Paginierung sowie das Anpassen der Anzahl der pro Seite angezeigten Elemente. 

## Protokolldetails anzeigen 

Klicken Sie **Details** Auf der rechten Seite des Datensatzes können Sie die vollständigen Informationen anzeigen, einschließlich: 

- Den Namen und die interne Kennung der Quelle und des Operationstyps. 
- Den Benutzer, der die Operation ausführt, und die Benutzer-ID. 
- Operationszeit. 
- Objekttyp, Objekt-ID und Objektname. 
- Ereignismetadaten. 

Bei Änderungen am Application Configuration Center können die Details auch die Konfigurationsänderungen anzeigen, ob ein automatischer Neustart nach der Freigabe erfolgt und welche Workloads neu gestartet wurden.

## Häufige Anwendungsfälle

- Überprüfen, wer eine bestimmte Konfigurationsänderung durchgeführt hat.
- Bestätigen der Zeitpunkte von System-Upgrades, Service-Neustarts oder Skalierungsoperationen.
- Verfolgen verwandter Änderungen anhand von Objektnamen.
- Überprüfen von Konfigurationsunterschieden und Ausführungsergebnissen mit Hilfe von Ereignismetadaten.
- Untersuchen von Fehlbedienungen oder unerwarteten administrativen Änderungen.

## Häufige Situationen

- **Keine Datensätze gefunden**: Versuchen Sie, die Filterbedingungen zu löschen oder zu bestätigen, ob die ausgewählte Quelle, der Operationstyp und der Benutzer übereinstimmen.
- **Liste der Operationstypen ist leer**: Wählen Sie zuerst die Ereignisquelle aus oder laden Sie die Seite neu, um die aktuelle Enumeration zu erhalten.
- **Objektinformationen sind leer**: Einige Systemereignisse sind möglicherweise nicht mit bestimmten Objekten verknüpft, was normal ist.
- **Metadaten sind nicht formatiert**: Einige historische Ereignisse werden möglicherweise als Klartext gespeichert, und die Seite zeigt den Originalinhalt an.
- **Protokollmenge entspricht nicht den Erwartungen**: Protokolle zeichnen nur Operationen auf, die vom System überwacht wurden, und können von der Aufbewahrungsrichtlinie der Umgebung beeinflusst werden.

> Operationsprotokolle können Benutzer, Objektkennungen und Informationen zu Konfigurationsänderungen enthalten und sollten nur autorisiertem Personal zugänglich sein.
