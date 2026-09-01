# Zusammenarbeitsblockierung

[← ShimoDocs Suite Bereitstellungsdokumentation](../../../README.md)

## Funktionsbeschreibung

Wenn ein Rückstand in Kafkaauftritt und bestätigt wird, dass der abnormale Rückstand durch eine bestimmte Datei verursacht wird, können Sie diese Sperrfunktion verwenden, um die Bearbeitung dieser Datei zu untersagen, wodurch das Kafka Rückstandsproblem gelöst wird

## Verwendungsillustration

1. Wählen Sie Zusammenarbeitssperre 

2. Geben Sie die Dateiguid ein, Hervorhebung: Dies bezieht sich auf die guid innerhalb von ShimoDocs, nicht auf die Datei-ID des Kunden 

Geben Sie die ShimoDocs Datei GUID und klicken Sie auf 'Zur Sperrliste hinzufügen'; die Datei wird innerhalb von 3 Minuten nicht mehr bearbeitbar sein. 

Klicken Sie auf die Schaltfläche 'Entsperren', um die Bearbeitungsfunktion der Datei wiederherzustellen 

### So erhalten Sie die GUID 

1. Öffnen Sie die Browser-Entwicklertools 

2. Filteranfragen 

3. In der Anfrage ist die 16-stellige Zeichenfolge von rp3OMYnMrdcQJZkm die GUID 

### So bestimmen Sie die Wirkung der Sperre

Das Dokument kann nicht erfolgreich gespeichert werden; nach dem Bearbeiten der Datei erscheint nach 2 Minuten ein Offline-Popup, und die Daten gehen nach dem Aktualisieren der Seite verloren.

### Wann entsperren

Das Aufheben der Sperre wird nicht empfohlen. Dies liegt im Allgemeinen daran, dass die Datei zu groß ist, um vom Server zur Bearbeitung unterstützt zu werden. Nach der Sperrung wird sie nur noch im Lesemodus verfügbar. Es wird empfohlen, den Dateiinhalt manuell in eine neue Datei zu kopieren.
