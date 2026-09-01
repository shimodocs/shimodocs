# Objektspeicher-Konfiguration

[← ShimoDocs Suite Bereitstellungsdokumentation](../../README.md)

Dieses Dokument soll Implementierungs-, Betriebs- oder Integrationspersonal dabei anleiten, die ShimoDocs Verbindung zu einem externen Drittanbieter S3 Objektspeicher Schritt für Schritt.


# 1. Vor der Durchführung Bestätigung

## S3 Anforderungen an den Objektspeicher

`Only object storage fully compatible with the S3 protocol is supported. Huawei Cloud OBS, Alibaba Cloud OSS, Tencent Cloud COS, and AWS S3 are recommended. For local deployment, MinIO can be considered.`



> [!TIP]
>
> Bevorzugt öffentliche Cloud, muss unterstützen HTTPS externen Zugriff



## Netzwerkkonnektivität

Die Ports für die K8s Das Business-Cluster-Netzwerk, um eine Verbindung zur Objektspeicherinstanz herzustellen, muss geöffnet sein

```js
telnet IP 9000
```
## Zugriffskontrolle und Berechtigungen
- Vollständige AK/SK-Authentifizierungsinformationen bereitstellen
- Muss Kernschnittstellen wie PutObject, GetObject, DeleteObject, ListObjects, CopyObject, InitiateMultipartUpload vollständig unterstützen



# S3 Beschreibung der Speicheranforderungen
- Latenzanforderungen: In einer internen Netzwerkumgebung wird eine durchschnittliche Reaktionszeit des Speichers API von < 50 ms empfohlen; in einer öffentlichen Netzwerkumgebung wird eine Reaktionszeit von < 200 ms empfohlen. Hohe Latenz wirkt sich direkt auf die Dokumentenöffnungs- und Anhangsuploaderfahrung aus.
- Nebenläufigkeitsfähigkeit: muss den geschätzten Spitzenwert des Unternehmens unterstützen QPS. ShimoDocs wird während der Zusammenarbeit mehrerer Benutzer und beim Batch-Import/Export Spitzenverkehr erzeugen, daher darf die Speicherseite keine zu strengen Ratenbegrenzungsrichtlinien haben.
- Verfügbarkeit SLA: Es wird empfohlen, dass die Speicherverfügbarkeit in der Produktionsumgebung ≥ 99,9 % beträgt.
- Die Kommunikation über das öffentliche Netzwerk mit dem Speicher muss über HTTPS/TLS verschlüsselte Kanäle erfolgen.
- Zeitsynchronisation: Der S3 Speicherdienst und der ShimoDocs Anwendungsserver müssen über NTPsynchronisieren, andernfalls S3 schlägt die Signaturprüfung fehl.
