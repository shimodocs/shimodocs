# Überwachung statischer Ressourcen

[← ShimoDocs Suite Bereitstellungsdokumentation](../../../README.md)

Die Überwachung statischer Ressourcen wird verwendet, um die von Webseiten referenzierten JS- und CSS Ressourcen zu überprüfen, damit Sie den Zugriffsstatus der Ressourcen, die Übertragungsprotokolle, die Cache-Konfiguration und CDN die Nutzung verstehen können.

> Der Funktionsname auf der Systemseite lautet 'Statische Ressourcen-Erkennung'.

## 1. Verwendung

Melden Sie sich auf der Verwaltungsplattform an, wählen Sie **Systemdienste** oben aus und dann **Werkzeugsammlung > Statische Ressourcen-Erkennung** in der linken Navigationsleiste.

Diese Funktion ist nur für Administratoren verfügbar. Wenn Sie den Eintrag nicht sehen, prüfen Sie bitte Ihre Kontoberechtigungen und die aktuelle Produktversion.

1. Geben Sie die vollständige Seite ein URL, zum Beispiel `https://example.com/recent`.
2. Wenn die Seite eine Anmeldung erfordert, erweitern Sie 'Benutzerdefinierte Anforderungsheader' und füllen Sie die erforderlichen Informationen wie `Cookie` und `Authorization`.
3. Klicken Sie auf 'Erkennung starten' und warten Sie auf die Rückgabe der Ergebnisse.

> Anforderungsheader werden auch verwendet, um auf statische Ressourcen zuzugreifen, die von der Seite referenziert werden. Bitte verwenden Sie nur temporäre Anmeldedaten und stellen Sie sicher, dass die Domains von Cross-Origin-Ressourcen vertrauenswürdig sind. Die Seitenadresse, Anforderungsheader und die neuesten Untersuchungsergebnisse werden im aktuellen Browser gespeichert.

## 2. Umfang der Untersuchung

Das System wird JS erkennen und CSS direkt in der Seite referenziert, HTMLaber es werden keine Bilder, Schriftarten, Inline-Code oder Ressourcen erkannt, die nach der Skriptausführung dynamisch geladen werden.

- Bis zu 3 Same-Domain JS- und CSS Dateien werden für jede Domain erkannt;
- Bis zu 50 Cross-Domain-Ressourcen können gleichzeitig erkannt werden;
- Doppelte URLs werden nur einmal gezählt.

Nicht erkannte Same-Domain-Ressourcen werden als „Same-Domain-Sampling übersprungen“ markiert, und dies weist nicht auf einen Ressourcenfehler hin.

## 3. Anzeige der Ergebnisse

Nach Abschluss der Untersuchung zeigt die Seite:

- **Zusammenfassende Informationen**: Anzahl der HTML Ressourcen, erkannte Anzahl, Anzahl der Probleme, Verwendung des Caches, CDNund HTTP/2;
- **Seitenantwort**: Statuscode, Protokoll und Cache-Informationen der Zielseite;
- **Ressourcenliste**: URL, Statuscode, Protokoll, Cache, CDNProbleme und Antwortheader jeder Ressource.

Die Ressourcenliste unterstützt Filter nach 'Erkannt', 'Alle' und 'Problematisch'. 

Das System weist hauptsächlich auf folgende Probleme hin: 

- HTTP 4xx/5xx; 
- Kein gültiger Cache erkannt; 
- HTTP/2 nicht verwendet; 
- Cross-Origin-Ressourcen zeigen keine CDN Eigenschaften; 
- Anforderung abgelaufen oder Domain-Name konnte nicht aufgelöst werden. 

## 4. Häufige Probleme 

### Seitenerkennung fehlgeschlagen 

Bitte überprüfen Sie die Seite URL, die Netzwerkkonnektivität, HTTPS das Zertifikat und den Anmeldestatus. Der Erkennungsdienst verwendet die Anmeldeinformationen des Browsers nicht automatisch, daher geben Sie bei Bedarf bitte benutzerdefinierte Anfrage-Header an. 

### Ressource nicht erkannt 

Bitte stellen Sie sicher, dass die Seite normal zurückgegeben wird HTML. Dynamisch durch Skripte geladene Ressourcen werden nicht erkannt. 

### CDN Zeigt 'Nicht erkannt' an 

Dieses Ergebnis zeigt nur an, dass keine CDN Merkmale in der Antwort erkannt wurden, und bedeutet nicht, dass die Ressource definitiv keine CDNverwendet. Bitte überprüfen Sie dies mit der CDN Konsole und Netzwerkarchitektur. 

## 5. Hinweise 

- Die Erkennungsergebnisse spiegeln wider, was vom Netzwerk der Management-Plattform während dieser Anfrage beobachtet wurde, und können von der tatsächlichen Benutzererfahrung abweichen. 
- CDN, Cache und Problemstatus sind automatisch bestimmte Ergebnisse und dienen nur der unterstützenden Diagnose. 
- 'Keine Probleme gefunden' bedeutet nicht, dass die Seite eine vollständige Leistungs-, Sicherheits- oder Benutzerfreundlichkeitsbewertung bestanden hat. 
- Nach der Veröffentlichung der Seite, CDN wird es empfohlen, nach der Aktualisierung oder Änderung der Netzwerkumgebung erneut zu testen.
