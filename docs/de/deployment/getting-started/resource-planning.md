# Ressourcenplanung

[← ShimoDocs Suite Bereitstellungsdokumentation](../README.md)

## 1. Zweck des Dokuments

Dieses Dokument dient zur Anleitung der Planung von Server- und Middleware-Ressourcen in privatisierten Bereitstellungsszenarien, zur Orientierung für Implementierungsingenieure, Betriebsingenieure und Pre-Sales-Technikpersonal.

Der Inhalt des Dokuments basiert auf historischer Projektressourcenplanung, Beispielkonfigurationen und Middleware-Baselines und kann für Pre-Sales-Schätzungen, Ressourcenanfragen, Implementierung und nachfolgende Ausbaubewertungen verwendet werden.

## 2. Umfang und Anweisungen

### 2.1 Umfang

Dieses Dokument gilt für die vorläufige Planung von Anwendungs-Knoten und Middleware-Ressourcen für verschiedene Benutzerzahlen in privatisierten Bereitstellungsszenarien.

### 2.2 Anweisungen

* Die Konfigurationen in diesem Dokument sind alle empfohlene Konfigurationen, die für die Kapazitätsbewertung und Ressourcenplanung in der Frühphase eines Projekts verwendet werden.

* Ressourcen des Anwendungsservers und Middleware-Ressourcen sollten separat berechnet werden; gemischte Planung wird nicht empfohlen.

* In Szenarien mit einer großen Anzahl von Benutzern müssen Middleware-Ressourcen basierend auf der Spitzenlast des Geschäfts, den Gleichzeitigkeitmodellen, den Ergebnissen von Kapazitätsstress-Tests und Produktionsüberwachungsdaten weiter kalibriert werden.

* In einer formalen Produktionsumgebung wird empfohlen, Erweiterungskapazität vorzusehen und die Hochverfügbarkeitsarchitektur zu priorisieren.

* Bei der Verwendung von inländischen CPU Architekturservern wird empfohlen, die Gesamtressourcen auf das Doppelte der Standardkonfiguration zu schätzen.

## 3. Planungsprinzipien

### 3.1 Prinzipien für die Bereitstellung von Anwendungen und Middleware

* Für Szenarien mit weniger als 10.000 Benutzern ist es möglich, basierend auf der tatsächlichen Projektsituation zu bewerten, ob einige Middleware innerhalb des K8s Clusters bereitgestellt werden sollen.

* Für Szenarien mit 10.000 oder mehr Benutzern wird empfohlen, Anwendungsserver und Middleware vollständig getrennt bereitzustellen.

* Kern-Middleware wie Datenbanken, Caches, Message Queues und Suchdienste sollten bevorzugt mit einer hochverfügbaren Architektur bereitgestellt werden.

* Wenn die Bedingungen es zulassen, wird empfohlen, vorrangig etablierte, verwaltete Middleware-Dienste der öffentlichen Cloud zu nutzen, um Stabilität und Wartbarkeit zu verbessern.

### 3.2 Planungsprinzipien für Objektspeicher

* Es wird bevorzugt, öffentliche Cloud-Objektspeicherdienste zu nutzen, wie Alibaba Cloud OSS, Huawei Cloud OBS, Tencent Cloud COS, AWS S3.

* Wenn ein privates Deployment von Objektspeicher verwendet wird, SSD Festplatten müssen verwendet werden, und die Leistung, Stabilität und Bedienbarkeit nach der Kapazitätserweiterung müssen sorgfältig bewertet werden.

* Wenn das Geschäft große Mengen an großen Dateiuploads, Downloads, Vorschauen oder Multi-User-Kollaborationsszenarien bei der Bearbeitung großer Tabellen umfasst, wird empfohlen, die Nutzung unabhängiger Objektspeicherdienste zu priorisieren.

## 4. Planung der Anwendungsnode

### 4.1 Klassifizierung der Spezifikationen der Anwendungsnode

#### Spezifikation A

* Empfohlene Spezifikation: `24C / 48G / >=500G SSD * N`

* Anwendungsbereich: weniger als 10.000 Benutzer

* Anwendbare Funktionen: 

   * Kann Szenarien für kleine bis mittlere Unternehmen unterstützen 

   * Middleware kann je nach Projekt im K8s Umfeld bereitgestellt werden 

   * Ein einzelner Node trägt eine hohe Last; wenn ein Node ausfällt, ist der Auswirkungenbereich relativ groß 

#### Spezifikation B 

* Empfohlene Spezifikation: `16C / 32G / >=300G SSD * N` 

* Anwendungsbereich: 10.000 Benutzer und mehr 

* Anwendbare Funktionen: 

   * Geeignet für großflächige, hochverfügbare Bereitstellungsszenarien 

   * Muss unabhängige Middleware verwenden 

   * Verwendet einen Ansatz mit mehreren Nodes niedriger Spezifikation, der eine ausgewogenere Planung und flexiblere Skalierung bietet 

   * Wenn ein Node gewartet wird oder auf ein Problem stößt, sind die Auswirkungen auf das gesamte Geschäft geringer 






### 4.2 Berechnungskriterien für Anwendungsnodes 

Basierend auf bestehenden Projektbeispielen und Kapazitätsberechnungsregeln wird empfohlen, Anwendungsnodes mit der folgenden Formel zu schätzen: 

`Number of nodes = Number of users × 0.03 ÷ 160` 

Es kann vereinfacht wie folgt verstanden werden: 

`Number of nodes ≈ Number of users ÷ 5300`

Wo:

* Der Gleichzeitigen-Benutzer-Koeffizient wird geschätzt auf `0.03`.

* Die Kapazität eines einzelnen `16C / 32G` Nodes beträgt ungefähr `150 ~ 180 QPS`.

* Es wird empfohlen, `160 QPS/node` als Berechnungsgrundlage zu verwenden.

* Das berechnete Ergebnis sollte aufgerundet werden, wobei zusätzliche Kapazität für Erweiterungen reserviert werden sollte.

### 4.3 Empfohlene Konfigurationstabelle für Anwendungsknoten

| Benutzerskala (Personen) | Knotenspezifikationen | Empfohlene Menge | Deployment-Empfehlungen |
|:----|:----|:----|:----|
|500|24C / 48G / 500G SSD|1 Einheit|Kann auf einer einzigen Maschine bereitgestellt werden; für hohe Verfügbarkeit wird empfohlen, mindestens 3 Server einzusetzen|
|3000|24C / 48G / 500G SSD|3 Einheiten|Cluster-Modus, Bereitstellung mit hoher Verfügbarkeit (Mindestanforderung für Cluster-Bereitstellung)|
|10,000|24C / 48G / 500G SSD|3 Einheiten|Cluster-Modus, Bereitstellung mit hoher Verfügbarkeit; externer Middleware-Einsatz kann je nach Projektbedarf evaluiert werden|
|30,000|16C / 32G / 300G SSD|5 Einheiten|Cluster-Modus, Bereitstellung mit hoher Verfügbarkeit, unter Verwendung unabhängiger Middleware|
|50,000|16C / 32G / 300G SSD|10 Einheiten|Cluster-Modus, Bereitstellung mit hoher Verfügbarkeit, unter Verwendung unabhängiger Middleware|
|100,000|16C / 32G / 300G SSD|18 ~ 20 Einheiten|Es wird empfohlen, mit 18 Einheiten zu starten und Kapazität für Erweiterungen zu reservieren, unter Verwendung unabhängiger Middleware|
|200,000|16C / 32G / 300G SSD|38 ~ 40 Einheiten|Es wird empfohlen, die Erstellung und Bereitstellung in Phasen durchzuführen|
|300,000|16C / 32G / 300G SSD|56 ~ 60 Einheiten|Es wird empfohlen, die Erstellung und Bereitstellung in Phasen durchzuführen|
|500,000|16C / 32G / 300G SSD|94 ~ 100 Einheiten|Es wird empfohlen, einen unabhängigen Ressourcenpool zu planen und die Erstellung und Bereitstellung in Phasen durchzuführen|
|700,000|16C / 32G / 300G SSD|132 ~ 140 Einheiten|Es wird empfohlen, einen unabhängigen Ressourcenpool zu planen und die Erstellung und Bereitstellung in Phasen durchzuführen|

### 4.4 Schlussfolgerungen zur Planung von Anwendungsknoten

* Benutzer unter 10.000 wird empfohlen, Spezifikation A zu verwenden.

* Benutzer ab 10.000 wird empfohlen, Spezifikation B zu verwenden.

* Für eine Benutzeranzahl von 100.000 kann mit 18 Einheiten wie im Beispielanker gestartet werden, andere Skalen werden nach einer einheitlichen Formel geschätzt und aufgerundet.

* Für kontinuierlich wachsende Projekte wird empfohlen, eine phasenweise Erweiterungsstrategie zu verfolgen, um übermäßige einmalige Investitionen zu vermeiden.

## 5. Middleware-Planung

### 5.1 Prinzipien zur Klassifizierung von Middleware

Die aktuelle Middleware-Ressourcenplanung wird gemäß zwei Basisstufen ausgeführt:

* `Users below 3,000`: Verwendung der Basis-Konfiguration für kleine Maßstäbe.

* `3000 users and above`: Verwendung der Basis-Konfiguration für große Maßstäbe. 

Für größere Szenarien wie 10.000, 30.000, 50.000, 100.000, 200.000, 300.000, 500.000, 700.000 Benutzer wird empfohlen, einheitlich mit der Basis-Konfiguration „3000 Benutzer und mehr“ zu beginnen und dynamisch entsprechend dem Geschäftswachstum zu skalieren. 

### 5.2 Basistabelle der Middleware-Spezifikation 

|Middleware|Empfohlene Version|Unter 3000 Benutzer|3000 Benutzer und mehr|Anforderungen an hohe Verfügbarkeit| 
|:----|:----|:----|:----|:----| 
|MySQL|MySQL 8.0|4C / 8G / 200G SSD|8C / 16G / 200G SSD|Master-Slave-Failover Hochverfügbarkeit<br>Zeichensatz: utf8mb4<br>Zeitzone: Asia/Shanghai oder UTC<br>Verbindungen: max_Verbindungen ≥ 1000| 
|MongoDB|MongoDB 4.4|2C / 8G / 100G SSD|4C / 16G / 100G SSD|Replica-Set Hochverfügbarkeitscluster| 
|Redis|Redis 6.2.21|2C / 4G / 100G SSD|2C / 8G / 100G SSD|Master-Slave/Sentinel Hochverfügbarkeit, Datenpersistenz; Cluster-Modus nicht unterstützt; Anzahl der DBs ≥ 64| 
|Kafka|Kafka 3.5|2C / 4G / 300G SSD|4C / 8G / 300G SSD|Brokers >= 3, Standard-Replikationsfaktor 3<br>Nachrichtenaufbewahrung: 72 Stunden (anpassbar basierend auf geschäftlichen Anforderungen)<br>Maximale Einzelnachrichtengröße pro Topic: 10 MB<br>Authentifizierung: Unterstützt SASL verschlüsselter Zugriff (PLAIN, SCRAM-SHA-256, SCRAM-SHA-512)|
|Elasticsearch|ES 8.18.5|2C / 4G / 200G SSD|4C / 8G / 200G SSD|Anzahl der Knoten >= 3<br>Erforderliche Installationen:<br>analysis-ik (Chinesische Wortsegmentierung),<br>analysis-pinyin (Pinyin-Segmentierung)|
|Objektspeicher|S3 Protokoll kompatibel|Kompatibel mit S3|Kompatibel mit S3 Protokoll|Bevorzugt öffentliche Cloud, muss unterstützen HTTPS externen Zugriff|

Hinweis: 

* Die oben genannten Middleware-Spezifikationen müssen entsprechend der tatsächlichen Last skaliert werden





## 6. Empfehlungen zur Implementierung und zum Betrieb & zur Wartung

### 6.1 Empfehlungen zur Bereitstellungsimplementierung

* MySQL, MongoDB, Redis, Kafka, Elasticsearch Empfohlen wird die Bereitstellung im Hochverfügbarkeits-Cluster-Modus.

* Wenn die Bedingungen es zulassen, wird empfohlen, vorrangig öffentliche Cloud-verwaltete Datenbanken und Middleware-Dienste zu verwenden, um Stabilität und Wartbarkeit zu verbessern.

* Bei Benutzerszenarien mit 10.000 oder mehr Benutzern wird empfohlen, Anwendungsnodes und Middleware getrennt bereitzustellen. 

* Für Kafkawird empfohlen, eine separate Instanz zu verwenden, um die Ressourcen nicht mit anderen Geschäftsbereichen zu teilen. 

### 6.2 Empfehlungen zur Implementierung von Objektspeicher 

* Empfohlen wird, vorrangig Produkte für Objektspeicher der öffentlichen Cloud zu verwenden. 

* Wenn privater Objektspeicher verwendet wird, SSD müssen Festplatten eingesetzt werden. 

* Wenn der Team-Speicherbereich eine große Anzahl von Szenarien für Hochladen, Herunterladen oder Vorschau großer Dateien umfasst, sollten Kapazität, Durchsatz und Bandbreite des Objektspeichers zentrale Bewertungskriterien sein. 

### 6.3 Skalierungsüberlegungen 

In den folgenden Geschäftsszenarien wird empfohlen, vorrangig die Evaluierung und Hinzufügung von Middleware-Ressourcen in Betracht zu ziehen: 

* Eine große Anzahl von Anlagen wird hochgeladen, heruntergeladen oder in der Vorschau angezeigt 

* Hochfrequente Volltextsuche 

* Nachrichtenstau oder intensive asynchrone Aufgaben 

* Batch-Schreibvorgänge und statistische Analysen während Spitzenzeiten 

* Kontinuierliches Wachstum des Protokollvolumens 

Wichtige Metriken, auf die geachtet werden sollte, umfassen: 

* Datenbank: CPU, Speicher, Festplatten-I/O 

* Redis: Anzahl der Verbindungen, Trefferquote, Bandbreitennutzung 

* Kafka: Anzahl der Broker, Nachrichtenstau, Speicherplatz 

* Elasticsearch: Anzahl der Knoten, Indexgröße, Speicherkapazität 

* Objektspeicherung: Lese-/Schreibleistung, Anfrage-Durchsatz, Kapazität, Bandbreite 






## 7. Fazit 

* Für kleinformatige Szenarien (Benutzer unter 10.000) wird empfohlen, Anwendungsknoten-Konfigurationen der Spezifikation A zu verwenden und basierend auf der Projektsituation zu bewerten, ob einige Middleware innerhalb des Clusters bereitgestellt werden sollen. 

* Für mittel- und großformatige Szenarien (10.000 Benutzer oder mehr) wird empfohlen, die Anwendungsknoten-Konfiguration der Spezifikation B zu verwenden, gepaart mit unabhängiger Middleware und einer hochverfügbaren Architektur. 
* Es wird empfohlen, Middleware basierend auf zwei Baselines zu konfigurieren: „unter 3000 Benutzer“ und „3000 und mehr“. Bei groß angelegten Projekten erfolgt die kontinuierliche Erweiterung auf Grundlage von Stresstests und Monitordaten. 
* Vor der offiziellen Implementierung sollten Ressourcenkonfirmation, Kompatibilitätsprüfung und Kapazitäts-Stresstests gleichzeitig abgeschlossen werden, um Diskrepanzen zwischen Bereitstellungsspezifikationen und tatsächlichem unterstützten Umfang zu vermeiden. 

* Bei der Nutzung eines inländischen CPU Architekturservers wird empfohlen, Ressourcen auf das Zweifache der Standard-Spezifikation zu schätzen. 

* Dieses Handbuch dient der Auswahl vor der Installation und ersetzt keine Vor-Ort-Stresstests oder die endgültige Implementierung.
