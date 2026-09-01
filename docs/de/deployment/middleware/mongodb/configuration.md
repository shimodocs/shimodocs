# MongoDB Konfiguration

[← ShimoDocs Suite Bereitstellungsdokumentation](../../README.md)

Dieser Artikel soll Implementierungs-, Betriebs- oder Integrationspersonal anleiten, die Integration von ShimoDocs mit externen MongoDB Schritt für Schritt abzuschließen.

## 1. Bestätigung vor dem Betrieb

## MongoDB Instanzanforderungen


| Middleware | Empfohlene Version | Unter 3000 Benutzern | Über 3000 Benutzer |
| --- | --- | --- | --- |
| MongoDB | MongoDB 4.4 | 2C 8G 100G SSD | 4C 16G 100G SSD |


## Cluster-Konfigurationsanforderungen
- Unterstützt Replikatensatz-Hochverfügbarkeitscluster, mindestens 3 Knoten sind in Produktionsumgebungen erforderlich
- Es wird empfohlen einzuschalten SCRAM-SHA-256 Authentifizierung





## Netzwerkkonnektivität

Die Ports für K8s Geschäftscluster zum Zugriff MongoDB muss offen sein

```js
telnet IP 27017
```

## Authentifizierung und Autorisierung
- In Produktionsumgebungen wird empfohlen, durchzusetzen SCRAM-SHA-256 Authentifizierung.


## Weitere Anforderungen
- Internes Netzwerk P99 Leseverzögerung < 5 ms, Schreibverzögerung < 10 ms
- Festplatte IOPS muss den Spitzen-Schreibanforderungen entsprechen; SSD ist obligatorisch
- Uhrensynchronisation: MongoDB Cluster-Knoten und ShimoDocs Anwendungsserver müssen NTP synchronisiert sein
- Regelmäßige vollständige Backups und kontinuierliche Oplog-Backups
