# Kafka Konfiguration

[← ShimoDocs Suite Bereitstellungsdokumentation](../../README.md)

Dieses Dokument soll Implementierungs-, Betriebs- oder Integrationspersonal anleiten, um die ShimoDocs Integration mit externer Nachrichten-Middleware Kafka Schritt für Schritt abzuschließen, für Szenarien wie asynchrone Aufgabenverarbeitung, Nachrichtenbenachrichtigungen, Datensynchronisation und Übermittlung von Audit-Logs. 


## 1. Vorbereitende Bestätigung 

## Kafka Instanzanforderungen 


| Middleware | Empfohlene Version | Für weniger als 3000 Benutzer | Für mehr als 3000 Benutzer | 
| --- | --- | --- | --- | 
| Kafka | Kafka 3.5 | 2C 4G 300G SSD | 4C 8G 300G SSD | 


## Konfigurationsanforderungen 
- Broker >= 3 
- Replikationsfaktor: Standardanzahl der Replikate ist 3, in Produktionsumgebungen ist ≥ 3 erforderlich, um hohe Verfügbarkeit sicherzustellen 
- Nachrichtenaufbewahrung: 72 Stunden (kann an Geschäftsanforderungen angepasst werden) 
- Maximale Einzelnachrichtengröße pro Thema: 10 MB 
- Authentifizierung: unterstützt SASL verschlüsselter Zugriff (PLAIN, SCRAM-SHA-256, SCRAM-SHA-512) 





## Netzwerkkonnektivität 

Die Ports für den Zugriff auf Kafka Instanzen aus dem K8s Geschäftscluster müssen offen sein 

```js
telnet IP 9092
```



## Weitere Anforderungen
- Intranet RTT sollte < 5ms sein; über Rechenzentren/Regionen hinweg sollte < 20ms sein.
- Die Bandbreite muss den Spitzen-Durchsatz erfüllen, um Nachrichtenstau aufgrund von Netzwerksättigung zu vermeiden.
- Stellen Sie sicher, dass die Kafka Broker- und ShimoDocs Anwendungsserver-Zeit sind synchronisiert (NTP), da Zeitabweichungen die Nachrichtenreihenfolge und TTL Berechnung beeinflussen können.
