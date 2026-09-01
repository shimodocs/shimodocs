# Dokumentenreparatur

[← ShimoDocs Suite Bereitstellungsdokumentation](../../../README.md)

Die Symptome einer abnormal beschädigten Datei umfassen, dass das Dokument nicht richtig geöffnet wird, Fehlermeldungen beim Laden erscheinen und der Inhalt nicht angezeigt wird.

Wenn das Dokument nicht geöffnet werden kann, verwenden Sie diese Funktion, um die Datei zu reparieren.

# Verwendungsillustration

Es gibt 2 Reparaturmethoden

## Vorbereitungen

### Schnellreferenz für Dateitypen

|**Dateityp**|**Datei URL Adressfunktionen**|**Bemerkungen**|
|:----|:----|:----|
|rdoc(richdoc)|/**docs**/{fileguid}|Leichtes Dokument|
|mosheet(modoc)|/**Blätter**/{fileguid}|Tabelle|
|modoc(modoc)|/**docx**/{fileguid}|Professionelles Dokument|

### Warnung vor operationellen Risiken

Reparaturfehler ist risikofrei

## Wiederherstellung von verschlüsselten Daten

Unterstützt nur die Reparatur von tabellenartigen Dateien. Für andere Dateitypen wählen Sie [Aus historischen Daten reparieren]

Dies ist die bevorzugte Methode. Sie können die Datei direkt eingeben GUID , um die Reparatur durchzuführen. Dies GUID ist das ShimoDocs Datei GUID.

Das Prinzip der Reparatur besteht darin, die verschlüsselten Dateidaten im Objektspeicher in unverschlüsselte Dateiinhaltsdaten umzuwandeln, was in den meisten Szenarien anwendbar ist.

Wenn diese Methode die Reparatur nicht erfolgreich durchführt, wählen Sie eine andere Methode.

### Datei GUID

1. Öffnen Sie die Entwicklerwerkzeuge des Browsers

2. Pull-Requests filtern

3. Im Request ist der Teil rp3OMYnMrdcQJZkm, diese 16-stellige Zeichenfolge, das guid

## Aus historischen Daten wiederherstellen

Aus historischen Aufzeichnungen wiederherstellen

1. Kunden-Datei-ID

2. Dateityp

   1. Für traditionelle Dokumente/Tabellenpräsentationen wählen Sie modoc

   2. Für leichte Dokumente wählen Sie richdoc

1. Datenquelle auswählen

### Kunden-Datei-ID

Wenn der Kunde verwendet ShimoDocs für die gesamte Seite, ist es die Dateiadresse des Dokuments im Browser, zum Beispiel das folgende m8AZMoYMrRsYbOkb

### Wie man die Datenquelle bestimmt 

Überprüfen Sie die Konfiguration des svc-edit Dienstes 

Konfigurationselement: history.driver 

Wenn es mysql ist, ist der Schalter 'Mongo-Datenquelle verwenden' ausgeschaltet 

Wenn es mongo ist, ist der Schalter 'Mongo-Datenquelle verwenden' eingeschaltet 

