# MySQL 8 Anforderungen

[← ShimoDocs Suite Bereitstellungsdokumentation](../../README.md)

Dieses Dokument soll Implementierungs-, Betriebs- oder Integrationspersonal dabei anleiten, die ShimoDocs Verbindung zu einer MySQL 8-Datenbank-Initialisierung sowie den Dienststart und die Verbindungsüberprüfung Schritt für Schritt abzuschließen.


## 1. Bestätigung vor dem Betrieb

## MySQL Bestätigung der Instanzspezifikationen

| Empfohlene Version | Benutzer unter 3000 | Benutzer über 3000 | 
| --- | --- | --- |
| MySQL 8.0 | 4C 8G 200G SSD | 8C 16G 200G SSD | 

## MySQL Konfigurations- und Hochverfügbarkeitsanforderungen
Unterstützt Master-Slave-Hochverfügbarkeitsumschaltung
Zeichensatz: utf8mb4 
Zeitzone: Asia/Shanghai oder UTC 
Verbindungen: max_Verbindungen ≥ 1000
Verbindungsbenutzer: Admin-Rechte

> [!TIP]
>
> Muss ein separates konfigurieren MySQL Instanz;
> 1. Um Fehlerisolierung, Berechtigungssicherheit sowie unabhängige Sicherung und Wiederherstellung zu erreichen und den stabilen und effizienten Betrieb des Dokumentsystems zu gewährleisten.
> 2. Das System unterstützt derzeit keine benutzerdefinierten Datenbanknamen und Tabellenpräfixe, daher muss die Planung und Vorbereitung einer separaten Instanz vor der Bereitstellung abgeschlossen sein.





## Netzwerkkonnektivität 
Die Ports zum Verbinden des k8s-Geschäftscluster-Netzwerks mit der MySQL Instanz müssen geöffnet werden. 

```js
telnet IP 3306
```
## Benutzerauthentifizierung
Der MySQL Der bereitgestellte Benutzer muss beim Verbinden mit dem MySQL Server authentifiziert werden.

# Erklärung: 
- Der MySQL Die Konfigurationen im Dokument sind alle Empfehlungseinstellungen, die zur Bewertung der Kapazität in der Anfangsphase des Projekts und zur Ressourcenplanung verwendet werden und nicht endgültige Produktionskonfigurationen darstellen. Die tatsächliche endgültige Konfiguration wird nach der Vorverkaufsbewertung bestimmt.
- Bei der Verwendung von Servern mit inländischer CPU Architektur wird empfohlen, die Gesamtressourcen auf das Doppelte der Standardspezifikation zu schätzen.
- Es wird empfohlen, in der formalen Produktionsumgebung Kapazitäten für Erweiterungen vorzusehen und eine hochverfügbare Bereitstellung zu priorisieren.
