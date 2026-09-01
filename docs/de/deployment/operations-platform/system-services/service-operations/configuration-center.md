# Konfigurationszentrum

[← ShimoDocs Suite Bereitstellungsdokumentation](../../../README.md)

## Funktionsübersicht 

Das Konfigurationszentrum wird verwendet, um Anwendungsconfigurations verschiedener Dienste anzusehen und zu ändern. Die Seite zeigt sowohl die Werksvorlagen-Konfiguration als auch die aktuell aktive Konfiguration an, was es einfach macht, Konfigurationsunterschiede zu verstehen und Änderungen kontrolliert freizugeben. 

Nachdem die Konfiguration veröffentlicht wurde, speichert das System diese Änderung und kann die relevanten Dienste automatisch basierend auf Ihrer Auswahl neu starten, um die neue Konfiguration anzuwenden. 

## Zugriff auf die Seite 

Nach dem Einloggen in das Verwaltungs-Backend wählen Sie **Konfigurationszentrum** in der linken Navigation, um auf die Seite zuzugreifen. 

Das Konfigurationszentrum steht nur Administratoren zur Verfügung. Wenn Sie dieses Menü nicht sehen, wenden Sie sich bitte an den Systemadministrator, um Ihre Kontoberechtigungen zu bestätigen. 

## Seitenbeschreibung 

Die Seite ist hauptsächlich in drei Bereiche unterteilt: 

- **Anwendungs- und Dateiliste**: Zeigt konfigurierbare Dateien nach Anwendung an und unterstützt die Suche nach Anwendungsnamen. 
- **Werksvorlagen-Konfiguration**: Zeigt die ursprüngliche Konfiguration an, die im Installationspaket bereitgestellt wird, nur zur Ansicht und als Referenz. 
- **Derzeit aktive Konfiguration**: Zeigt die aktuell von der Umgebung verwendete Konfiguration an, die direkt bearbeitet werden kann. 

Konfigurationsdateien liegen normalerweise im JSON, YAML, oder TOML Format vor. Bitte halten Sie die korrekte Dateisyntax und Datenstruktur ein. 

## Modifizieren und Veröffentlichen der Konfiguration 

Es wird empfohlen, die folgenden Schritte zu befolgen: 

1. Wählen Sie auf der linken Seite die Anwendung und die Konfigurationsdatei, die Sie ändern möchten.
2. Orientieren Sie sich an der Werksvorlage und ändern Sie den Konfigurationsinhalt im **Wirksame Konfiguration** Bereich.
3. Nach der Änderung zeigt die Seite den Status **Geändert, aber nicht veröffentlicht**.
4. Klicken Sie **Geändert, aber nicht veröffentlicht**an oder verwenden Sie `Ctrl S` (Windows) / `Command S` (macOS), um das Bestätigungsfenster zu öffnen.
5. Überprüfen Sie die Feldpfade, Änderungstypen und die neuen zu veröffentlichenden Werte.
6. Wählen Sie, ob Sie aktivieren möchten **Nach dem Veröffentlichen der Konfiguration benötigte Dienste neu starten.** falls erforderlich.
7. Klicken Sie **Konfiguration veröffentlichen** um die Änderung abzuschließen.

Wenn es Formatierungsfehler im Konfigurationsinhalt gibt, wird das System einen Fehler anzeigen und die Veröffentlichung verhindern. Bitte korrigieren Sie es und versuchen Sie es erneut.

## Änderungsbestätigung

Das Bestätigungsfenster vor der Veröffentlichung zeigt die Unterschiede für diese Änderung an:

- **Pfad**: Der Konfigurationspfad, der geändert wurde.
- **Op**: Art der Änderung, wie hinzufügen, ändern oder löschen.
- **Wert**: Der Konfigurationswert nach der Änderung.

Es wird empfohlen, jede Abweichung zu überprüfen, um ein versehentliches Löschen einer Konfiguration oder die Änderung falscher Serviceparameter zu vermeiden.

## Service-Neustart

Einige Konfigurationen werden erst nach einem Neustart des Dienstes wirksam. Standardmäßig aktiviert die Seite **Nach dem Veröffentlichen der Konfiguration benötigte Dienste neu starten.**, und nach einer erfolgreichen Veröffentlichung werden die mit der Anwendung verbundenen Dienste automatisch neu gestartet.

Wenn diese Option deaktiviert ist, wird die Konfiguration weiterhin veröffentlicht, aber die zugehörigen Dienste müssen möglicherweise später manuell neu gestartet werden, um die neuen Einstellungen anzuwenden.

Während des Service-Neustarts können relevante Funktionen kurzzeitig schwanken; es wird empfohlen, wichtige Konfigurationsänderungen außerhalb der Geschäftszeiten vorzunehmen.

## Häufige Situationen

- **Anwendung nicht gefunden**: Bitte löschen Sie die Suchkriterien oder bestätigen Sie, dass die Zielanwendung korrekt bereitgestellt wurde.
- **Konfigurationsdatei kann nicht geladen werden**: Bitte überprüfen Sie den Dienststatus und die aktuellen Kontoberechtigungen, bevor Sie es erneut versuchen.
- **Konfigurationsformatfehler**: Bitte überprüfen Sie die Einrückung, Klammern, Anführungszeichen und das Feldformat in JSON, YAML, oder TOML.
- **Keine Änderungen zu veröffentlichen**: Der tatsächliche Konfigurationsinhalt hat sich effektiv nicht geändert, eine Veröffentlichung ist nicht erforderlich.
- **Änderungen nach der Veröffentlichung nicht wirksam**: Bitte überprüfen Sie, ob die zugehörigen Dienste neu gestartet wurden, und falls erforderlich, manuell neu starten und erneut überprüfen.
- **Freigabe fehlgeschlagen**: Bitte überprüfen Sie den Konfigurationsinhalt oder den Dienstestatus gemäß den Hinweisen auf der Seite und veröffentlichen Sie erneut nach Bearbeitung. 

> Konfigurationsänderungen können den Dienststart und Geschäftsfunktionsablauf beeinflussen. Bitte erst nach vollständiger Bestätigung der Änderungen veröffentlichen. 

## Beispiel der Bedienoberfläche 

Das folgende Diagramm zeigt die Bereiche zum Auswählen von Konfigurationsdateien, Anzeigen von Konfigurationsinhalten und Bearbeiten. 

