# Service-Logs

[← ShimoDocs Suite Bereitstellungsdokumentation](../../../README.md)

## Funktionsübersicht 
Die Funktion Service-Logs ist eine Kibana-ähnliche Protokollabrufplattform, die Pod-Protokolle von verschiedenen ShimoDocs Diensten sammeln kann und Funktionen für Protokollsuchen, Abfragen und Anteilsanalysen bietet. 

## Einstieg und Navigation
Menü auf der linken Seite: Systemdienste --> Service-Operationen --> Service-Logs

## SQL Modus
Das Eingabefeld unterstützt ClickHouse SQL Syntaxabfragen. Nach der Eingabe SQLkönnen Sie die Abfrage im ClickHouse-Rohmodus ausführen.

Wie in der Abbildung unten gezeigt, geben Sie

``` sql
`_raw_log_` like '%access%'
```

ein, um alle Protokolle abzufragen, die Zugriff enthalten. 

## Bedingte Filterung
Wie in der Abbildung unten gezeigt, klicken Sie auf die Schaltfläche „Bedingung hinzufügen“, um eine neue Filterbedingung hinzuzufügen.

## Anteilsanalyse
Wie in der Abbildung unten gezeigt, klicken Sie auf das Symbol neben einem Feld im Zeilenprotokoll, um das Dropdown-Menü zu öffnen. Nach der Auswahl von „Top-Werte“ können Sie den Anteil dieses Feldes innerhalb des aktuellen Zeitraums oben rechts anzeigen.

## Feldbeschreibung

| Eingebautes Feld | Beschreibung |
| --- | --- |
| lv | Der Fehlerlevel des Protokolls, einschließlich info, error, warn |
| container.name | CONTAINER_NAME |
| Methode | Die Methode im Zugriffsprotokoll; gRPC druckt die gRPC Methode, HTTP druckt die API Pfad |
| peerIP | Die Peer-IP |
| peerName | Der Name des Peers, wie zum Beispiel der Dienstname usw. |
| comp | Die Komponente im Zugriffsprotokoll, wie server.begin |
| cost | Die im Zugriffsprotokoll verbrachte Zeit, in Millisekunden |

## Fallanalyse
### Alle Fehlerprotokolle des Tages abfragen

Im Bereich Bedingung hinzufügen das Feld lv auswählen und lv = error wie im folgenden Bild gezeigt hinzufügen

### Anforderungsprotokolle anzeigen

    1. Verwenden Sie `msg`='access', um alle Anforderungsprotokolle anzuzeigen, einschließlich HTTP und gRPC
    
2. Anzeigen HTTP Anfragen

3. Anzeigen gRPC Anfragen

