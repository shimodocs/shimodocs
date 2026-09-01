# Redis Konfiguration

[← ShimoDocs Suite Bereitstellungsdokumentation](../../README.md)

Dieses Dokument soll Implementierer, Betriebsmitarbeiter oder Integratoren anleiten, die Integration von ShimoDocs mit externen Redis Schritt für Schritt abzuschließen. Es wird typischerweise für Kernszenarien wie Sitzungsverwaltung, verteilte Sperren, Rate-Limiting-Zähler und Nachrichtenwarteschlangen verwendet.

## 1. Bestätigung vor dem Betrieb

## Redis Instanzanforderungen


| Middleware | Empfohlene Version | Für unter 3000 Benutzer | Für über 3000 Benutzer |
| --- | --- | --- | --- |
| Redis | Redis 6.2.21 | 2C 4G 100G SSD | 2C 8G 100G SSD |


## Cluster-Konfigurationsanforderungen
- Unterstützt Master-Slave/Sentinel Hochverfügbarkeit
- Datenpersistenz
- Cluster-Modus wird nicht unterstützt
- Anzahl der DBs muss >= 64 sein





## Netzwerkkonnektivität

Die Ports zum Verbinden des K8s Business-Cluster-Netzwerks mit der Redis Instanz müssen geöffnet sein

```js
telnet IP 6379
```

## Authentifizierung und Autorisierung
- In Produktionsumgebungen wird empfohlen, die PASSWORD Authentifizierung zu aktivieren (requirepass / ACL).


## Weitere Anforderungen
- Internes Netzwerk P99 Latenz sollte < 10 ms sein
- Uhrensynchronisation: Redis Cluster-Knoten und ShimoDocs Anwendungsserver müssen NTP synchronisiert sein
- Regelmäßige vollständige Backups
