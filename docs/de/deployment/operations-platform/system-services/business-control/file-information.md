# Dateiinformationssuche

[← ShimoDocs Suite Bereitstellungsdokumentation](../../../README.md)

## Funktionsübersicht

Dateiinformationsabfrage wird verwendet, um Basisdatensätze von Dateien im System basierend auf der internen Datei GUID oder der kundenbezogenen Datei-ID abzufragen, was die Überprüfung von Datei-IDs, zugehörigen Anwendungen, Dateitypen, Status und Inhaltsgröße erleichtert.

Diese Seite ist nur lesbar und wird den Inhalt oder Status von Dateien nicht ändern.

## Zugriff auf die Seite

Nach dem Einloggen in das Verwaltungs-Backend wählen Sie **Dateiinformationsabfrage** im linken Navigationsbereich, um die Seite aufzurufen.

## Dateien abfragen

Die Seite unterstützt die folgenden Abfragebedingungen:

- **Interne Datei GUID**: die Datei `history_guid`.
- **Kunden-Datei-Anbieter GUID**: die Kundenseite `provider_file_id`.
- **App-ID**: optional, empfohlen, zusammen mit dem Anbieter auszufüllen GUID , um die zugehörige Anwendung anzugeben.

Mindestens eine der internen Dateien GUID oder der Kunden-Dateianbieter GUID soll ausgefüllt werden, dann klicken Sie auf **Abfrage**.

Wenn nur der Anbieter GUID ausgefüllt ist und die App-ID nicht ausgefüllt ist, gibt das System alle Datensätze zurück, die dem Anbieter entsprechen GUID, sodass mehrere Ergebnisse erscheinen können. 

### Die Datei abrufen GUID 
1. Im Falle von ShimoDocs Suite, müssen Sie nur die Adresse des Pakets im Browser als die **Kunden-Datei-Anbieter GUID**. 

## Abfrageergebnisse

Die Abfrageergebnisse umfassen hauptsächlich:

- **id**: Die Primärschlüssel-ID des Dateieintrags.
- **app_id**: Die ID der zugehörigen Anwendung.
- **provider_Datei_id**: Clientseitige Datei-ID.
- **history_guid**: Interne systemhistorische Datei GUID.
- **created_at**: Erstellungszeit des Eintrags.
- **type**: Dateityp, z. B. Dokument, Tabelle, Präsentation, PDF, Bild oder Video.
- **created_by**: ID des erstellenden Benutzers.
- **status**: Der aktuelle Statuswert der Datei.
- **Datei_content_Größe**: Die Größe des Dateiinhalt, in Bytes.

Die Dateitypen in den Ergebnissen werden sowohl die Typnummer als auch den entsprechenden Namen zur leichteren Identifizierung anzeigen.

## Häufige Situationen

- **Eingabeaufforderung: Abfragebedingungen müssen eingegeben werden**: Bitte füllen Sie mindestens die interne Datei GUID oder den Provider aus GUID.
- **Datei nicht gefunden**: Bitte überprüfen Sie, ob der Bezeichner vollständig ist, oder bestätigen Sie, ob die Datei zur aktuellen Umgebung gehört.
- **Mehrere Datensätze zurückgegeben**: Der Provider GUID kann in mehreren Anwendungen dupliziert sein; bitte fügen Sie die App-ID hinzu und suchen Sie erneut.
- **Dateityp wird als unbekannt angezeigt**: Die Typnummer des aktuellen Datensatzes hat möglicherweise noch keinen entsprechenden Namen konfiguriert. Sie können die Nummer dem technischen Support zur Bestätigung bereitstellen.
- **Statuswert kann nicht bestimmt werden**: Das Statusfeld ist ein zugrunde liegender Datensatzwert und muss in Verbindung mit spezifischen geschäftlichen Phänomenen und Protokollen weiter analysiert werden.

> Dateibezeichner gehören zu Geschäftsdaten; bitte vermeiden Sie, diese direkt in öffentlichen Chats oder externen Tickets zu teilen.
