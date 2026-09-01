# Überwachungsmetriken Referenz

[← ShimoDocs Suite Bereitstellungsdokumentation](../README.md)

Dieses Dokument organisiert häufig verwendete Metriken im Überwachungssystem und deckt Knoten, containerd-Container ab Kubernetes Cluster, Middleware und Anwendungsdienste, die eine einheitliche Referenz für tägliche Inspektionen, Kapazitätsbewertungen und Fehlerbehebungen bieten. 

Metriknamen basieren auf den tatsächlich in Prometheus gesammelten Exporter-Metriken. Unterschiedliche Exporter-Versionen können geringfügige Unterschiede aufweisen, und die tatsächliche Fehlerbehebung sollte sich auf die Online-Abfrageergebnisse als endgültige Referenz stützen. 

## Umfang 

| Kategorie | Abgedeckte Objekte | 
| --- | --- | 
| Knotenüberwachung | Linux-Hosts, Systemressourcen, Festplatten, Netzwerk, Prozesse | 
| Containerüberwachung | Container, die auf containerd laufen, Ressourcen der Pod-Container | 
| Kubernetes Cluster | Knoten, Pod, Deployment, StatefulSet, Job, PVC, APIServer | 
| MySQL | MySQL Instanzen, Verbindungen, Abfragen, Cache, Sperren, Netzwerk | 
| MongoDB | MongoDB Instanzen, Verbindungen, Operationen, Speicher, Netzwerk, Replikationspuffer | 
| Redis | Redis Instanzen, Clients, Befehle, Speicher, Keyspace, Trefferrate | 
| Kafka | Broker, Thema, Partition, Verbrauchergruppe, Verzögerung, Replik | 
| MinIO | Cluster-Knoten, Festplatten, Bucket, S3 Anfragen, Objektkapazität | 
| Elasticsearch | Cluster-Gesundheit, Knoten, Shards, Indizes, JVM, Thread-Pools, Netzwerk |
| Anwendungsdienste | Allgemeiner Server, Client-Aufrufe, kollaboratives Bearbeiten, RS-Dienste, Laufzeit |

## Metrik-Leseregeln

| Metriktyp | Lesemethode | Gängige PromQL-Syntax | Beschreibung |
| --- | --- | --- | --- |
| Zähler | Betrachte Wachstumsrate oder Zuwachs innerhalb eines Zeitfensters | `rate(x_total[5m])`, `increase(x_total[5m])` | Anzahl der Anfragen, Anzahl der Fehler, Byte-Anzahl, IO-Zeiten gehören im Allgemeinen zum Zähler |
| Messgerät | Betrachte aktuellen Wert, Durchschnitt, Maximum | `avg(x)`, `max(x)`, `sum(x)` | Speicher, Verbindungsanzahl, Kapazität, Statuswerte gehören im Allgemeinen zum Messgerät |
| Histogramm | Betrachte Perzentil-Latenz | `histogram_quantile(0.95, sum(rate(x_bucket[5m])) by (le))` | Anfrage-Latenz, Verarbeitungs-Latenz, Warteschlangen-Latenz verwenden in der Regel ein Histogramm |
| Verhältnis | Auf Prozentsatz schauen | `A / B * 100` | Auslastung, Fehlerrate, Trefferquote gehören alle zu Verhältnis-Metriken |

Es wird empfohlen, Schwellenwerte nicht direkt aus festen Zahlen zu übernehmen. Metriken wie CPU, Speicher, Festplatte, Verbindunganzahl, QPS, und Verzögerung sollten im Kontext von Geschäftsspitzen, Kapazitätsplanung und historischem Baseline bewertet werden. Die in dem Dokument genannten abnormalen Verhaltensweisen dienen dazu, Risiken schnell zu erkennen und entsprechen nicht den endgültigen Alarmgrenzen.

## 1. Knoten-Servicemonitoring

Knotenüberwachung dient dazu, festzustellen, ob der Host gesund ist, ob Ressourcen ausreichend sind und ob es Engpässe bei Festplatte oder Netzwerk gibt. Knotenmetriken stammen hauptsächlich von node-exporter, kombiniert mit dem Systemprozess-Dashboard zur Prozess-Ebene-Lokalisierung.

### 1.1 Grundstatus

| Überwachungsdimension | Metrik | Metrikbedeutung | Üblicher Standard/Einheit | Abnormale Leistung |
| --- | --- | --- | --- | --- |
| Knoten Alive | `up` | Ob der Exporter oder das Sammlungziel zugänglich ist | `1` zeigt an, dass es sammelbar ist, `0` zeigt an, dass es nicht sammelbar ist | Kontinuierlich `0` zeigt ein Problem mit dem Knoten, dem Netzwerk oder dem Exporter an |
| Boot-Zeit | `node_boot_time_seconds` | Letzte Startzeit des Knotens | Unix-Zeitstempel | Änderung der Boot-Zeit zeigt an, dass der Knoten neu gestartet wurde |
| Knoteninformation | `node_uname_info`, `node_os_info` | Betriebssystem-, Kernel- und Distributionsinformationen | Label-Informationen | Dient zur Überprüfung der Knoten-Versionen, wird nicht direkt als Alarmmetrik verwendet |

Fehlerbehebungsvorschlag: prüfen Sie zuerst `up` , dann `node_boot_time_seconds`. Wenn der Knoten nicht sammelbar ist und die Boot-Zeit sich kürzlich geändert hat, priorisieren Sie die Bestätigung des Host-Neustarts, Netzwerk ACL, und Status des node-exporter-Prozesses.

### 1.2 CPU Metriken

| Überwachungsdimension | Metrik | Metrikbedeutung | Üblicher Standard/Einheit | Abnormale Leistung |
| --- | --- | --- | --- | --- |
| CPU Verwendung | `node_cpu_seconds_total` | Kumulierte Zeit, die jeder CPU Kern in verschiedenen Modi verbringt | Prozentsatz | `user` und `system` Bleibt langfristig hoch, Rechenleistung des Knotens ist knapp |
| Leerlauf CPU | `node_cpu_seconds_total{mode="idle"}` | CPU Leerlaufzeit | Prozentsatz | Leerlaufzeit ist dauerhaft niedrig, was zu Warteschlangen und erhöhter Latenz führen kann |
| IO-Wartezeit | `node_cpu_seconds_total{mode="iowait"}` | Zeit CPU Wartet auf Festplatten-IO | Prozentsatz | Anhaltender Anstieg der iowait weist normalerweise auf langsamere Festplatte oder Speicherverbindung hin |
| Systemlast | `node_load1`, `node_load5`, `node_load15` | 1/5/15-Minuten-Durchschnittslast | Lastwert | Last, die dauerhaft über der Anzahl der CPU Kerne liegt, deutet auf deutliche Aufgabenwarteschlangen hin |
| CPU Druck | `node_pressure_cpu_waiting_seconds_total` | Kumuliert CPU PSI Wartezeit | Sekunden/Sekunde | CPU Ressourcenkonflikte sind erheblich, Prozesse warten auf CPU Planung |

Häufige Abfragen:

```promql
100 - avg by (instance) (rate(node_cpu_seconds_total{mode="idle"}[5m])) * 100
```

```promql
avg by (instance) (rate(node_cpu_seconds_total{mode="iowait"}[5m])) * 100
```

Untersuchungsvorschläge: Wenn CPU Die Nutzung ist hoch, zuerst unterscheiden zwischen `user`, `system`und `iowait`. Hoch `user` ist meist auf den Berechnungsdruck im Geschäftsbetrieb zurückzuführen, hoch `system` kann mit Systemaufrufen und der Verarbeitung von Netzwerkpaketen zusammenhängen, und hoch `iowait` erfordert die Überprüfung des Festplattendurchsatzes, IOPSund der Latenz.

### 1.3 Speichermetriken

| Überwachungsdimension | Metrik | Metrikbedeutung | Übliche Einheit | Abnorme Erscheinung |
| --- | --- | --- | --- | --- |
| Gesamtspeicher | `node_memory_MemTotal_bytes` | Gesamter physischer Speicher des Knotens | Bytes | Wird zur Berechnung der Nutzungsrate verwendet |
| Verfügbarer Speicher | `node_memory_MemAvailable_bytes` | Speicher, den das System Prozessen zuweisen kann | Bytes / Prozentsatz | Konsistent niedriger verfügbarer Speicher kann das Auslösen von OOM oder häufigen Rückforderungen begünstigen |
| Freier Speicher | `node_memory_MemFree_bytes` | Vollständig ungenutzter Speicher | Bytes | Kann in Linux nicht allein zur Beurteilung des Speicherdrucks verwendet werden |
| Speicherdruck | `node_pressure_memory_waiting_seconds_total` | Gesammelte Speicher PSI Wartezeit | Sekunden/Sekunde | Zunahme der Speicherfreigabe oder Wartezeit bei Speicherzuweisungen |
| OOM Anzahl | `node_vmstat_oom_kill` | Anzahl der System OOM Abbrüche | Anzahl/Erhöhung | Wenn sie zunimmt, identifizieren Sie die beendeten Prozesse und den maximalen Speicherverbrauch |

Häufige Abfragen:

```promql
(1 - node_memory_MemAvailable_bytes / node_memory_MemTotal_bytes) * 100
```

```promql
increase(node_vmstat_oom_kill[10m])
```

Untersuchungsvorschlag: Nicht nur auf `MemFree` für Speicher achten. Die tatsächliche Verfügbarkeit sollte eher anhand von `MemAvailable`, kombiniert mit dem Arbeitssatzspeicher des Containers, Prozess RSSund OOM Protokollen bewertet werden.

### 1.4 Festplattenkapazität und Inodes

| Überwachungsdimension | Metrik | Metrikbedeutung | Häufige Messung/Einheit | Abnormale Leistung |
| --- | --- | --- | --- | --- |
| Gesamtes Dateisystem | `node_filesystem_size_bytes` | Gesamtkapazität des Mount-Punkts | Bytes | Wird verwendet, um die Festplattenauslastung zu berechnen |
| Verfügbares Dateisystem | `node_filesystem_avail_bytes` | Für normale Benutzer verfügbarer Speicherplatz | Bytes | Unzureichender verfügbarer Speicherplatz kann Schreibfehler verursachen |
| Freies Dateisystem | `node_filesystem_free_bytes` | Freier Speicher im Dateisystem | Bytes | Beinhaltet für root reservierten Speicherplatz; wird normalerweise zusammen mit `avail` |
| Schreibgeschütztem Status betrachtet | `node_filesystem_readonly` | Ob das Dateisystem schreibgeschützt ist | `0/1` | Wenn `1`, Geschäfts-Schreibvorgänge schlagen fehl |
| Gesamt-Inodes | `node_filesystem_files` | Gesamtzahl der Inodes im Dateisystem | Anzahl | Benötigt besondere Aufmerksamkeit bei kleinen Dateien |
| Verbleibende Inodes | `node_filesystem_files_free` | Anzahl der verbleibenden Inodes | Anzahl/Prozentsatz | Wenn die Inodes erschöpft sind, können Dateien nicht erstellt werden, selbst wenn noch Festplattenspeicher vorhanden ist |

Häufige Abfragen:

```promql
(1 - node_filesystem_avail_bytes{fstype!~"tmpfs|overlay"} / node_filesystem_size_bytes{fstype!~"tmpfs|overlay"}) * 100
```

```promql
(1 - node_filesystem_files_free / node_filesystem_files) * 100
```

Untersuchungsvorschläge: Festplattenkapazitätswarnungen sollten nach Mount-Punkt überprüft werden, insbesondere für Daten-Datenträger, Log-Datenträger und Container-Laufzeitverzeichnisse. Hohe Inode-Nutzung stammt in der Regel von einer großen Anzahl kleiner Dateien, Log-Slices oder nicht bereinigten temporären Dateien.

### 1.5 Festplatte IOPS, Durchsatz und Latenz

| Überwachungsdimension | Metrik | Metrikbedeutung | Häufige Messung/Einheit | Abnormale Leistung |
| --- | --- | --- | --- | --- |
| Lesen IOPS | `node_disk_reads_completed_total` | Anzahl der abgeschlossenen Festplatten-Leseanforderungen | mal/Sekunde | Lesen IOPS Nahe der Gerätegrenze, Lese-Latenz steigt |
| Schreiben IOPS | `node_disk_writes_completed_total` | Anzahl der abgeschlossenen Schreibanforderungen auf die Festplatte | mal/Sekunde | Schreib-Backlog, Protokoll- oder Datenbankübertragungen verlangsamen sich |
| Lese-Durchsatz | `node_disk_read_bytes_total` | Kumulative Bytes, die von der Festplatte gelesen wurden | Bytes/s | Hoher Durchsatz und hohe iowait zeigen an, dass der Speicher ausgelastet ist |
| Schreib-Durchsatz | `node_disk_written_bytes_total` | Kumulierte Bytes, die auf die Festplatte geschrieben wurden | Bytes/s | Langfristig hoher Schreibdurchsatz kann Datenbanken und Objektspeicher beeinträchtigen |
| Lesezeit | `node_disk_read_time_seconds_total` | Kumulierter Zeitaufwand für Leseanforderungen | Sekunden/Sekunde | Leselatenz steigt |
| Schreibzeit | `node_disk_write_time_seconds_total` | Kumulierte Zeit von Schreibanforderungen | Sekunden/Sekunde | Erhöhte Schreiblatenz |
| IO-Auslastung | `node_disk_io_time_seconds_total` | Kumulative Zeit, die die Festplatte mit der Verarbeitung von IO verbringt | Prozentsatz | Bei nahezu voller Auslastung warten Anwendungen auf IO |
| Gewichtete IO-Zeit | `node_disk_io_time_weighted_seconds_total` | IO-Zeit unter Berücksichtigung der Warteschlangenlänge | Sekunden/Sekunde | Aufbau von Warteschlangen zeigt schwere Gerätewarteschlangen an |
| IO-Druck | `node_pressure_io_waiting_seconds_total` | Kumulative IO PSI Wartezeit | Sekunden/Sekunde | Prozesse warten länger auf IO |

Häufige Abfragen:

```promql
rate(node_disk_reads_completed_total[5m])
```

```promql
rate(node_disk_writes_completed_total[5m])
```

```promql
rate(node_disk_read_bytes_total[5m])
```

```promql
rate(node_disk_written_bytes_total[5m])
```

```promql
rate(node_disk_io_time_seconds_total[5m]) * 100
```

```promql
rate(node_disk_read_time_seconds_total[5m]) / rate(node_disk_reads_completed_total[5m])
```

```promql
rate(node_disk_write_time_seconds_total[5m]) / rate(node_disk_writes_completed_total[5m])
```

Untersuchungsvorschlag: Betrachten Sie bei der Überprüfung von Problemen nicht nur die Festplattenkapazität. Selbst wenn die Kapazität normal ist, kann die Geschäftsausführung sich verlangsamen, wenn IOPSDurchsatz, IO-Auslastung und iowait gleichzeitig zunehmen. Starke IO-Dienste wie Datenbanken, Kafkaund MinIO sollten sich auf Schreiblatenz und Warteschlangen konzentrieren.

### 1.6 Netzwerkmetriken

| Überwachungsdimension | Metrik | Metrikbedeutung | Übliche Einheit | Abnormale Anzeichen |
| --- | --- | --- | --- | --- |
| Eingehender Verkehr | `node_network_receive_bytes_total` | Kumulative Bytes, die von der Netzwerkkarte empfangen wurden | Bytes/s | Plötzlicher Anstieg des eingehenden Verkehrs, möglicherweise aufgrund von Anfrageanstiegen oder Daten-Synchronisation |
| Ausgehender Verkehr | `node_network_transmit_bytes_total` | Kumulative Bytes, die von der Netzwerkkarte gesendet wurden | Bytes/s | Plötzlicher Anstieg des ausgehenden Verkehrs, möglicherweise aufgrund von Downloads, Backups oder Replikation |
| Eingehende Fehler | `node_network_receive_errs_total` | Kumulative Anzahl der empfangenen Fehlerpakete | Zählung/s | Netzwerkkarten-, Verbindungs- oder Treiberprobleme |
| Ausgehende Fehler | `node_network_transmit_errs_total` | Kumulative Anzahl der gesendeten Fehlerpakete | Zählung/s | Verbindungsprobleme oder Probleme mit der Netzwerkkartenwarteschlange |
| Eingehender Paketverlust | `node_network_receive_drop_total` | Kumulative Anzahl der verworfenen empfangenen Pakete | Zählung/s | Kernelwarteschlange oder Netzwerkkarte kann nicht mithalten |
| Ausgehender Paketverlust | `node_network_transmit_drop_total` | Kumulativer Wert des gesendeten Paketverlusts | Mal/Sekunde | Ausgehende Überlastung oder NIC Warteschlangendruck |

Häufige Abfragen:

```promql
rate(node_network_receive_bytes_total{device!~"lo|veth.*|cni.*"}[5m])
```

```promql
rate(node_network_transmit_bytes_total{device!~"lo|veth.*|cni.*"}[5m])
```

```promql
rate(node_network_receive_drop_total[5m]) + rate(node_network_transmit_drop_total[5m])
```

Untersuchungsvorschläge: Bei Netzwerk-Anomalien sollten Verkehr, Fehlerpakete und Paketverlust zusammen betrachtet werden. Hoher Datenverkehr allein deutet nicht unbedingt auf einen Fehler hin; hoher Datenverkehr zusammen mit Fehlerpaketen oder Paketverlust deutet eher auf ein Verbindungs- oder Host-Netzwerkstack-Problem hin.

### 1.7 TCP, Dateihandles und Systembelastung

| Überwachungsdimension | Metrik | Metrikbedeutung | Gängige Einheit / Messung | Abnormales Verhalten |
| --- | --- | --- | --- | --- |
| Aktuell TCP Verbindungen | `node_netstat_Tcp_CurrEstab` | Aktuelle Anzahl der etablierten TCP Verbindungen | Zählung | Ein plötzlicher Anstieg der Verbindungen kann auf einen Verkehrsspitze oder einen Verbindungsleck hinweisen |
| TIME_WAIT | `node_sockstat_TCP_tw` | Anzahl der TIME_WAIT Verbindungen | Zählung | Zu viele kurzlebige Verbindungen können Ports erschöpfen oder Kernelbelastung erhöhen |
| TCP Zugewiesen | `node_sockstat_TCP_alloc` | Anzahl der zugewiesenen TCP Sockets | Zählung | Ein kontinuierlicher Anstieg der Socket-Anzahl erfordert die Untersuchung der Verbindungsfreigabe |
| TCP In Gebrauch | `node_sockstat_TCP_inuse` | Anzahl der TCP in Gebrauch befindliche Sockets | Zählung | Erhöhter Verbindungsdruck |
| TCP Verwaist | `node_sockstat_TCP_orphan` | Anzahl verwaister Sockets | Zählung | Abnormer Anstieg kann mit abnormalem Verbindungsabbau zusammenhängen |
| Verwendete Dateihandles | `node_filefd_allocated` | Anzahl der vom System zugewiesenen Dateihandles | Stück | Zu hoch kann neue Verbindungen und Dateiöffnungen beeinträchtigen |
| Limit für Dateihandles | `node_filefd_maximum` | System-Limit für Dateihandles | Stück | Wird verwendet, um die Nutzungshäufigkeit von Handles zu berechnen |

Häufige Abfragen: 

```promql
node_filefd_allocated / node_filefd_maximum * 100
```

Untersuchungsempfehlungen: Datei-Handles und TCP Verbindungen werden normalerweise zusammen betrachtet. Wenn die Anzahl der Serververbindungen steigt, und die System-Handles sich ihrem Limit nähern, kann es bei der Anwendung zu Annahmefehlern, Dateiöffnungsfehlern oder Fehlern bei abhängigen Verbindungen kommen.

### 1.8 Prozessüberwachung

| Überwachungsdimension | Metrik | Metrikbedeutung | Häufige Messung/Einheit | Abnormales Verhalten |
| --- | --- | --- | --- | --- |
| Prozess CPU | `process_cpu_seconds_total` | Gesamt CPU Zeit des Prozesses | Sekunden/Sekunde | Langfristig hohe CPU Nutzung durch einen einzelnen Prozess |
| Physischer Speicher | `process_resident_memory_bytes` | Prozess RSS Speicher | Bytes | Kontinuierliches Wachstum von RSS kann auf ein Speicherleck hinweisen |
| Virtueller Speicher | `process_virtual_memory_bytes` | Virtueller Speicher des Prozesses | Bytes | Abnormales Wachstum muss zusammen mit RSS |
| Offene Handles | `process_open_fds` | Anzahl der offenen Datei-Handles des Prozesses | Zählung | Kontinuierliches Wachstum kann auf ein Handle-Leck hinweisen |
| Maximale Handles | `process_max_fds` | Maximale Anzahl von Datei-Handles, die der Prozess öffnen kann | Zählung | Wird verwendet, um die Nutzungshäufigkeit von Prozess-Handles zu berechnen |
| Prozessstartzeit | `process_start_time_seconds` | Prozessstartzeit | Unix-Zeitstempel | Änderungen der Startzeit deuten auf einen Prozessneustart hin |

Untersuchungsempfehlungen: Prozessmetriken werden verwendet, um spezifische Dienste für Probleme auf Knotenebene zu identifizieren. Wenn Knoten CPU hoch ist, überprüfen Sie den Prozess CPU; wenn der Speicherdruck des Knotens hoch ist, überprüfen Sie RSS; wenn die Handles des Knotens hoch sind, überprüfen Sie `process_open_fds`. 

## 2. containerd Containerüberwachung

Containerüberwachung stammt hauptsächlich von kubelet/cAdvisor und zeigt die Ressourcennutzung der von containerd verwalteten Container an. Das Dokument verwendet weiterhin die `container_*` Benennung der Prometheus-Metriken, aber die zugrunde liegende Containerlaufzeit während des tatsächlichen Betriebs ist containerd. 

### 2.1 Container CPU

| Überwachungsdimension | Metrik | Metrikbedeutung | Häufiger Umfang/Einheit | Abnormale Leistung |
| --- | --- | --- | --- | --- |
| CPU Verwendung | `container_cpu_usage_seconds_total` | Gesamt CPU Nutzungszeit des Containers | Cores/Sekunden | Nutzungsrate lange nahe dem Limit, mögliche Erhöhung der Geschäfts-Latenz |
| CPU Gedrosselte Zeit | `container_cpu_cfs_throttled_seconds_total` | Gesamtzeit CPU wird gedrosselt von CFS | Sekunden/Sekunden | Bedeutend CPU Deutliche Drosselung zeigt, dass das Limit zu eng oder die Last zu hoch ist |
| CPU Quote | `container_spec_cpu_quota` | Container CPU Kontingent | Kontingentwert | Wird verwendet, um zu erkennen, ob ein CPU Limit gesetzt ist |

Häufige Abfragen: 

```promql
sum by (namespace, pod, container) (rate(container_cpu_usage_seconds_total{container!="",image!=""}[5m]))
```

```promql
sum by (namespace, pod, container) (rate(container_cpu_cfs_throttled_seconds_total{container!="",image!=""}[5m]))
```

Untersuchungsempfehlung: Hoher Container CPU erfordert nicht unbedingt Skalierung. Überprüfen Sie zunächst, ob er gedrosselt wird, dann prüfen Sie, ob die Anfragen/Limits des Pods zu niedrig sind, und betrachten Sie schließlich die Service-Anfragelatenz, um festzustellen, ob dies wirklich das Geschäft beeinträchtigt.

### 2.2 Container-Speicher

| Überwachungsdimension | Metrik | Metrikbedeutung | Übliche Einheit | Abnormales Verhalten |
| --- | --- | --- | --- | --- |
| RSS Speicher | `container_memory_rss` | Anonyme Seiten des Containers und RSS Speicher | Bytes | Kontinuierliches Wachstum ist näher am tatsächlichen Speicherbedarf des Prozesses |
| Genutzter Speicher | `container_memory_usage_bytes` | Gesamter Container-Speicherverbrauch | Bytes | Beinhaltet Cache, kann alleine keine Lecks bestimmen |
| Arbeitsset-Speicher | `container_memory_working_set_bytes` | Aktiver Arbeitsset-Speicher des Containers | Bytes | Annäherung an das Limit kann OOMKilled verursachen |
| Speicherlimit | `container_spec_memory_limit_bytes` | Container-Speicherlimit | Bytes | Wird verwendet, um die Speichernutzungsrate zu berechnen |

Häufige Abfragen:

```promql
container_memory_working_set_bytes{container!="",image!=""} / container_spec_memory_limit_bytes{container!="",image!=""} * 100
```

Untersuchungsvorschlag: Bei Speicher-Risiko in Geschäftscontainern sollte vorrangig das Arbeitsset betrachtet werden und RSS. `usage_bytes` ist stark vom Page-Cache beeinflusst, geeignet zur Kapazitätsbeobachtung, aber nicht als alleinige Grundlage für OOM Urteil.

### 2.3 Container-Disk und temporärer Speicher

| Überwachungsdimension | Metrik | Metrikbedeutung | Häufige Messung/Einheit | Abnormale Leistung |
| --- | --- | --- | --- | --- |
| Lese-Durchsatz | `container_fs_reads_bytes_total` | Kumulative Bytes, die der Container von der Festplatte gelesen hat | Bytes/s | Plötzlicher Anstieg des Leseverkehrs kann auf Scannen, Importieren oder Abruf vom Cache-Ursprung hinweisen |
| Schreib-Durchsatz | `container_fs_writes_bytes_total` | Kumulative Bytes, die der Container auf die Festplatte geschrieben hat | Bytes/s | Schreibspitzen können Node-IO-Druck verursachen |
| Lesen IOPS | `container_fs_reads_total` | Anzahl der Leseanforderungen durch den Container | Operationen/s | Hohe Häufigkeit von Lesevorgängen kleiner Blöcke kann IO-Wartezeiten erhöhen |
| Schreiben IOPS | `container_fs_writes_total` | Anzahl der Schreibanforderungen durch den Container | Operationen/s | Übermäßiges Schreiben von Protokollen oder temporären Dateien |
| Dateisystemnutzung | `container_fs_usage_bytes` | Dateisystemnutzung des Containers | Bytes | Ansammlung von temporären Dateien oder Protokollen |
| Dateisystemlimit | `container_fs_limit_bytes` | Dateisystemlimit des Containers | Bytes | Schreibvorgänge können fehlschlagen, wenn das Limit erreicht wird |

Häufige Abfragen: 

```promql
sum by (namespace, pod, container) (rate(container_fs_reads_bytes_total{container!="",image!=""}[5m]))
```

```promql
sum by (namespace, pod, container) (rate(container_fs_writes_bytes_total{container!="",image!=""}[5m]))
```

Untersuchungsvorschlag: Wenn das Schreiben des Containers auf der Festplatte abnormal ist, prüfen Sie zuerst das Pod-Protokollvolumen, das Verzeichnis für temporäre Dateien und Batch-Aufgaben. Wenn die Festplatten-IO des Nodes hoch ist, können Container-FS-Metriken verwendet werden, um herauszufinden, welcher Pod schreibt.

### 2.4 Container-Netzwerk

| Überwachungsdimension | Metrik | Metrikbedeutung | Häufiger Umfang/Einheit | Abnormale Leistung |
| --- | --- | --- | --- | --- |
| Eingehender Datenverkehr | `container_network_receive_bytes_total` | Gesamte vom Container empfangene Bytes | Bytes/s | Plötzlicher Anstieg des Anfrageverkehrs oder Replikationsverkehrs |
| Ausgehender Datenverkehr | `container_network_transmit_bytes_total` | Gesamte vom Container gesendete Bytes | Bytes/s | Erhöhter Download-, Synchronisations-, Origin-Abruf- oder Exportverkehr |
| Eingehender Paketverlust | `container_network_receive_packets_dropped_total` | Gesamtzahl der vom Container beim Empfang verworfenen Pakete | Mal/s | Paketverlust verursacht durch das Netzwerk-Stack oder Knotenbelastung |
| Ausgehender Paketverlust | `container_network_transmit_packets_dropped_total` | Gesamtzahl der vom Container beim Senden verworfenen Pakete | Mal/s | Ausgehende Überlastung, NIC Warteschlange oder CNI Probleme |

Häufige Abfragen:

```promql
sum by (namespace, pod) (rate(container_network_receive_bytes_total[5m]))
```

```promql
sum by (namespace, pod) (rate(container_network_transmit_bytes_total[5m]))
```

Untersuchungsvorschläge: Das Container-Netzwerk sollte zusammen mit dem Knoten analysiert werden NIC Metriken. Wenn der Paketverlust auf Pod-Ebene zunimmt, aber keine Anomalie am Knoten vorliegt, überprüfen Sie weiterhin CNI, iptables und die Last auf dem Knoten, auf dem sich der Pod befindet. 

### 2,5 Container-Threads und Lebenszyklus

| Überwachungsdimension | Metrik | Metrikbedeutung | Häufiger Umfang/Einheit | Abnormales Verhalten |
| --- | --- | --- | --- | --- |
| Anzahl der Threads | `container_threads` | Anzahl der Threads im Container | Zählung | Kontinuierliches Wachstum der Threads kann auf ein Thread-Leck hinweisen |
| Zuletzt gesehen | `container_last_seen` | Das letzte Mal, dass der Container von cAdvisor gesehen wurde | Unix-Zeitstempel | Keine Aktualisierung über einen langen Zeitraum kann darauf hinweisen, dass der Container beendet wurde oder eine Sammelanomalie vorliegt |
| Neustartanzahl | `kube_pod_container_status_restarts_total` | Gesamtzahl der Containerneustarts | Zählen/Inkrement | Häufige Neustarts weisen auf Absturz, Prüfungsfehler oder OOM |
| Wartegrund | `kube_pod_container_status_waiting_reason` | Grund, warum sich der Container im Wartezustand befindet | Bezeichnungswert | `CrashLoopBackOff`, `ImagePullBackOff`, usw., müssen behoben werden |
| Laufender Status | `kube_pod_container_status_running` | Ob der Container läuft | `0/1` | Schlüsselcontainer nicht `1` zeigt an, dass der Dienst nicht verfügbar ist |

Untersuchungsempfehlungen: Bei Containeranomalien zuerst den Statusgrund prüfen, dann die Neustartanzahl und die letzte Neustartzeit ansehen. Wenn Neustarts häufig auftreten, mit Anwendungsprotokollen, OOM Ereignissen und Prüfungs-Konfigurationen weiter untersuchen. 

## 3. Kubernetes Cluster-Überwachung

Kubernetes Die Überwachung wird verwendet, um die Ressourcennutzung des Clusters, die Gesundheit der Steuerungsebene, den Status der Arbeitslast-Replikate und den Status der Speicherobjekte zu beurteilen. Die Hauptmetriken stammen von kube-state-metrics, kubelet und APIServer. 

### 3.1 Knoten-Kapazität und planbare Ressourcen

| Überwachungsdimension | Metrik | Metrikbedeutung | Häufiger Umfang/Einheit | Abnorme Erscheinung |
| --- | --- | --- | --- | --- |
| Knoten-Kapazität | `kube_node_status_capacity` | Gesamtkapazität des Knotens | CPU, Speicher, Anzahl der Pods, usw. | Wird für Kapazitätsplanung verwendet |
| Zuordnungsfähige Ressourcen | `kube_node_status_allocatable` | Planbare Ressourcen des Knotens | CPU, Speicher, Anzahl der Pods, usw. | Unzureichende planbare Ressourcen führen dazu, dass Pods ausstehend bleiben |
| Knotenbedingungen | `kube_node_status_condition` | Knotenbereit, Speicherbelastung und andere Zustände | `0/1` | Abnormale Bereit- oder Druckzustände erfordern sofortige Aufmerksamkeit |
| Planung verboten | `kube_node_spec_unschedulable` | Ist der Knoten gesperrt (Cordon) | `0/1` | Wenn auf '1' gesetzt, plant der Knoten keinen neuen Pod |
| Knoteninformation | `kube_node_info` | Knoten-Version, Kernel, Informationen zur Containerlaufzeit | Tag-Informationen | Wird verwendet, um Versionsunterschiede zu beheben |

Fehlerbehebungsvorschlag: Wenn Pod ausstehend ist, zuerst Allocatable und Requests überprüfen, dann prüfen, ob der Node 'nicht planbar' ist, und schließlich prüfen, ob der Node-Zustand Ressourcenengpässe aufweist. 

### 3.2 Pod-Status 

| Überwachungsdimensionen | Indikatoren | Bedeutung der Indikatoren | Gängige Aperturen/Einheiten | Abnormales Verhalten |
| --- | --- | --- | --- | --- |
| Pod-Informationen | `kube_pod_info` | Pod-Namespace, Node, etc. Informationen | Tag-Informationen | Wird verwendet, um Pod-Verteilung zu lokalisieren |
| Pod-Phase | `kube_pod_status_phase` | Ausstehender, Laufender, Erfolgreich, Fehlgeschlagen Status, etc. | `0/1` | Erhöhung von Ausstehend/Fehlgeschlagen weist auf Planungs- oder Laufanomalien hin |
| Pod Bereit | `kube_pod_status_ready` | Ist der Pod bereit | `0/1` | Nicht bereit beeinträchtigt die Serviceverfügbarkeit |
| Pod-Grund | `kube_pod_status_reason` | Abnormaler Pod-Grund | Label-Wert | Evicted, NodeLost usw. müssen untersucht werden |
| Container-Neustarts | `kube_pod_container_status_restarts_total` | Anzahl der Container-Neustarts | Mal/Zunahme | Wachstum der Neustarts weist auf Stabilitätsprobleme hin |
| Container im Warten | `kube_pod_container_status_waiting` | Ob der Container sich im Wartestatus befindet | `0/1` | Wenn der Wartestatus anhält, kann der Pod den Dienst nicht normal bereitstellen |
| Wartegrund | `kube_pod_container_status_waiting_reason` | Grund für den Wartestatus | Label-Wert | Fehler beim Abrufen des Images, CrashLoop usw. |
| Container beendet | `kube_pod_container_status_terminated` | Ob der Container beendet ist | `0/1` | Unerwartete Beendigung sollte zusammen mit Neustarts und Logs überprüft werden |

Häufige Abfragen:

```promql
sum by (namespace, phase) (kube_pod_status_phase == 1)
```

```promql
increase(kube_pod_container_status_restarts_total[10m])
```

Untersuchungsvorschlag: Schauen Sie bei einer Pod-Anomalie nicht nur auf die Pod-Phase. Der Bereit-Status, der Grund und der Wartestatus des Containers veranschaulichen das spezifische Problem besser.

### 3.3 Ressourcenanforderungen und -limits

| Überwachungsdimension | Metrik | Metrikbedeutung | Häufige Messung/Einheit | Anomales Verhalten |
| --- | --- | --- | --- | --- |
| Angeforderte Ressourcen | `kube_pod_container_resource_requests` | Container-Anforderungen | CPU, Speicher | Anfragen, die zu hoch sind, beeinträchtigen die Planung, zu niedrig beeinträchtigen die Stabilität |
| Ressourcenlimits | `kube_pod_container_resource_limits` | Container-Limits | CPU, Speicher | Zu niedrige Limits können verursachen CPU Drosselung oder OOM |
| Zuordnungsfähiger Knoten | `kube_node_status_allocatable` | Ressourcen, die für die Planung auf einem Knoten verfügbar sind | CPU, Speicher | Wird verwendet, um die Ressourcenzuweisungsrate im Cluster zu berechnen |
| Container-Nutzung | `container_cpu_usage_seconds_total`, `container_memory_working_set_bytes` | Tatsächlich CPU und Speichernutzung | Kerne/Sekunden, Bytes | Wird verwendet, um zu bestimmen, ob Anfragen/Limits angemessen sind |

Häufige Abfragen:

```promql
sum(kube_pod_container_resource_requests{resource="cpu"}) / sum(kube_node_status_allocatable{resource="cpu"}) * 100
```

```promql
sum(kube_pod_container_resource_requests{resource="memory"}) / sum(kube_node_status_allocatable{resource="memory"}) * 100
```

Untersuchungsvorschlag: Die Ressourcenplanung sollte sowohl den 'angeforderten Wert' als auch den 'tatsächlichen Nutzungswert' berücksichtigen. Nur auf Anfragen zu schauen, kann den Geschäftsaufwand falsch einschätzen, während nur auf die Nutzung zu schauen, die Planungsfähigkeit übersehen kann.

### 3.4 Arbeitslast-Replikas

| Überwachungsdimension | Metrik | Metrikbedeutung | Häufiger Umfang/Einheit | Abnormale Leistung |
| --- | --- | --- | --- | --- |
| Deployment-Replikas | `kube_deployment_status_replicas` | Aktuelle Anzahl der Deployment-Replikas | Einheiten | Nicht konsistent mit erwarteten Replikas |
| Aktualisierte Replikas | `kube_deployment_status_replicas_updated` | Anzahl der auf die neue Version aktualisierten Replikas | Einheiten | Langes Ausbleiben von Wachstum während der Veröffentlichung |
| Nicht verfügbare Replikas | `kube_deployment_status_replicas_unavailable` | Anzahl der nicht verfügbaren Replikas | Einheiten | Dienstkapazität nimmt ab, wenn größer als 0 |
| StatefulSet-Replikas | `kube_statefulset_status_replicas` | Aktuelle Anzahl der StatefulSet-Replikas | Einheiten | Abnormale Replikas in zustandsbehafteten Diensten |
| StatefulSet Bereit | `kube_statefulset_status_replicas_ready` | Anzahl der bereiten StatefulSet-Replikas | Einheiten | Wenn Bereitschaft kleiner als die erwarteten Replikas ist, ist der Dienst unvollständig |

Untersuchungsempfehlungen: Wenn eine Veröffentlichungsanomalie vorliegt, prüfen Sie `updated` und `unavailable`. Bei StatefulSet-Anomalien achten Sie auf PVC, Startreihenfolge von Pods und Knotenaffinität.

### 3.5 Jobs und Batch-Aufgaben

| Überwachungsdimension | Metrik | Metrikbedeutung | Üblicher Standard/Einheit | Abnormale Leistung |
| --- | --- | --- | --- | --- |
| Laufende Jobs | `kube_job_status_active` | Anzahl der derzeit aktiven Jobs | Anzahl | Langfristig aktiv kann auf einen feststeckenden Job hinweisen |
| Fehlgeschlagene Jobs | `kube_job_status_failed` | Anzahl der fehlgeschlagenen Jobs | Anzahl | Ein Anstieg der Fehlgeschläge erfordert die Überprüfung der Job-Protokolle |
| Erfolgreiche Jobs | `kube_job_status_succeeded` | Anzahl der erfolgreich abgeschlossenen Jobs | Anzahl | Wird verwendet, um den Abschluss von Aufgaben zu bestimmen |
| Abschlusszeit | `kube_job_status_completion_time` | Job-Abschlusszeit | Unix-Zeitstempel | Fehlende Abschlusszeit kann auf unvollständige Jobs hinweisen |

Untersuchungsempfehlungen: Wenn Batch-Aufgaben Anomalien aufweisen, überprüfen Sie `active`, `failed`und `succeeded` gemeinsam. Nur die Fehlgeschläge zu betrachten, kann langanhaltend blockierte Aufgaben übersehen.

### 3.6 PVC und Speicherobjekte

| Überwachungsdimension | Metrik | Metrikbedeutung | Üblicher Standard/Einheit | Abnormale Leistung |
| --- | --- | --- | --- | --- |
| PVC Status | `kube_persistentvolumeclaim_status_phase` | PVC Bound, Ausstehend und andere Status | `0/1` | Ausstehend wird dazu führen, dass das Pod den Speicher nicht mounten kann |
| PVC Angeforderte Kapazität | `kube_persistentvolumeclaim_resource_requests_storage_bytes` | Die vom Speicher angeforderte Kapazität PVC | Bytes | Wird für Kapazitätsplanung und Quotenverwaltung verwendet |

Fehlerbehebungsvorschlag: Wenn ein zustandsbehafteter Dienst nicht startet, sollten Sie zusätzlich zum Überprüfen des Pods auch prüfen, ob PVC er Bound ist, ob die Storage-Klasse verfügbar ist und ob der zugrundeliegende Speicher unzureichende Kapazität hat.

### 3.7 APIServer, etcd und Control Plane

| Überwachungsdimension | Metrik | Metrikbedeutung | Häufig verwendete Maßeinheit | Abnormale Leistung |
| --- | --- | --- | --- | --- |
| APIServer-Anfrageanzahl | `apiserver_request_total` | Kumulative Anzahl der APIServer-Anfragen | Anfragen/Sek. | Plötzliche Anfrage-Spitzen können von Controllern, kubectloder Geschäftskomponenten stammen |
| APIServer-Latenz | `apiserver_request_duration_seconds_bucket` | APIServer-Anfragen-Dauer-Buckets | P50/P95/P99 | Erhöhte Latenz wirkt sich auf Planung, Bereitstellung und Controller-Synchronisierung aus |
| etcd-Latenz | `etcd_request_duration_seconds_bucket` | etcd-Anfragen-Dauer-Buckets | P50/P95/P99 | Langsames etcd kann die gesamte Control Plane verlangsamen |
| Warteschlangen-Wartezeit | `workqueue_queue_duration_seconds_bucket` | Controller-Warteschlangen-Wartezeit-Dauer | Perzentil-Dauer | Warteschlangenrückstand, Ressourcensynchronisierung verlangsamt sich |
| Warteschlangenverarbeitung | `workqueue_work_duration_seconds_bucket` | Controller-Verarbeitungsdauer | Perzentil-Dauer | Controller-Verarbeitung verlangsamt sich |

Häufige Abfragen:

```promql
sum by (verb, resource) (rate(apiserver_request_total[5m]))
```

```promql
histogram_quantile(0.95, sum(rate(apiserver_request_duration_seconds_bucket[5m])) by (le, verb, resource))
```

Untersuchungsempfehlungen: Probleme der Steuerungsebene zeigen sich normalerweise durch langsame Bereitstellung, langsame Pod-Statusaktualisierungen und langsame kubectl Antworten. Wenn sowohl die Latenz des APIServers als auch die Latenz von etcd gleichzeitig steigen, priorisieren Sie die Überprüfung von etcd, Festplatten-I/O und der Auslastung der Steuerungsebene-Knoten.

## 4. MySQL Überwachung

MySQL Monitoring wird verwendet, um die Verfügbarkeit von Instanzen, Verbindungsdruck, SQL Anfragevolumen, langsame Abfragen, Cache-Treffer, temporäre Tabellen, Sperrwarten, Dateihandles und Netzwerkdurchsatz zu beobachten.

### 4.1 Instanzstatus und Anfragevolumen

| Überwachungsdimension | Metrik | Metrikbedeutung | Häufiger Umfang/Einheit | Abnorme Erscheinung |
| --- | --- | --- | --- | --- |
| Instanz lebt | `up` | Ob mysql exporter gesammelt werden kann | `0/1` | Wenn `0`, Instanz, Netzwerk oder Exporter ist abnormal |
| Betriebszeit | `mysql_global_status_uptime` | MySQL Laufzeit | Sekunden | Abnahme deutet auf Instanzneustart hin |
| Gesamtanzahl der Abfragen | `mysql_global_status_queries` | Kumulative Anzahl der Abfragen | Mal/Sekunde | QPS Spitze kann auf Geschäftsspitze oder abnormale Anfragen hinweisen |
| Fragen | `mysql_global_status_questions` | Kumulative Anzahl der von Clients initiierten Anweisungen | Mal/Sekunde | Zusammen mit Abfragen zu betrachten, um den Anfrageaufwand zu bewerten |
| Befehlsstatistiken | `mysql_global_status_commands_total` | Kumulative Anzahl verschiedener Befehle | Mal/Sekunde | Kann Befehle wie select, insert, update, delete unterscheiden |

Häufige Abfragen: 

```promql
rate(mysql_global_status_queries[5m])
```

```promql
sum by (command) (rate(mysql_global_status_commands_total[5m]))
```

Untersuchungsvorschläge: Wenn QPS steigt, überprüfen Sie zuerst die Befehlsverteilung. Wenn `select` zusammen mit Scan-Art-Indikatoren steigt, achten Sie auf Indizes und langsame Abfragen; wenn Schreibbefehle zunehmen, überwachen Sie weiterhin Sperrwarten, Festplatten-I/O und Schreiblatenz des Hosts.

### 4.2 Verbindungen und Threads

| Überwachungsdimension | Metrik | Metrikbedeutung | Häufig verwendete Maßeinheit | Abnormale Leistung |
| --- | --- | --- | --- | --- |
| Aktuelle Verbindungen | `mysql_global_status_threads_connected` | Anzahl der derzeit verbundenen Threads | Zählung | Wenn die Grenze erreicht wird, können neue Verbindungen fehlschlagen |
| Aktive Threads | `mysql_global_status_threads_running` | Anzahl der Threads, die derzeit ausgeführt werden | Zählung | Kontinuierlicher Anstieg deutet normalerweise auf langsame SQL Ausführung oder Sperr-Wartung |
| Historische Maximale Verbindungen | `mysql_global_status_max_used_connections` | Historische maximale Anzahl verwendeter Verbindungen | Zählung | Annäherung an das Maximum_Verbindungen deutet darauf hin, dass der Verbindungspool überprüft werden muss |
| Maximale Verbindungen | `mysql_global_variables_max_connections` | MySQL Maximale Verbindungskonfiguration | Zählung | Wird verwendet, um die Verbindungsnutzungsrate zu berechnen |
| Abnormale Clients | `mysql_global_status_aborted_clients` | Kumulative Anzahl abnormaler Client-Verbindungen | mal/Sekunde | Netzwerkprobleme, Zeitüberschreitungen oder Client-seitige Ausnahmen |
| Verbindung fehlgeschlagen | `mysql_global_status_aborted_connects` | Gesamtanzahl der Verbindungsfehler | Mal/Sekunde | Authentifizierungsfehler, Verbindungsbegrenzung, Netzwerk-Anomalien usw. |

Häufige Abfragen:

```promql
mysql_global_status_threads_connected / mysql_global_variables_max_connections * 100
```

Untersuchungsvorschläge: Eine hohe Anzahl von Verbindungen bedeutet nicht unbedingt, dass die Datenbank langsam ist; dies kann auch auf einen falsch konfigurierten Anwendung-Verbindungspool zurückzuführen sein. `Threads_running` Lange Zeit hohe Werte sind besorgniserregender, da sie normalerweise SQL Ausführungs- oder Sperrwarterei-Problemen entsprechen.

### 4.3 Langsame Abfragen, Scans und Sortierungen

| Überwachungsdimension | Metrik | Metrikbedeutung | Häufige Messung/Einheit | Abnormales Verhalten |
| --- | --- | --- | --- | --- |
| Langsame Abfragen | `mysql_global_status_slow_queries` | Kumulative Anzahl langsamer Abfragen | mal/Sekunde | Anstieg deutet auf mehr langsame Abfragen hin SQL |
| Vollständige Join-Scans | `mysql_global_status_select_full_join` | Anzahl der Joins ohne Indizes | mal/Sekunde | Deutet darauf hin, dass Join-Bedingungs-Indizes fehlen könnten |
| Vollständige Tabellenscans | `mysql_global_status_select_scan` | Anzahl der vollständigen Tabellenscans | mal/Sekunde | Vollständige Scans auf großen Tabellen können die Instanz verlangsamen |
| Sort Merge | `mysql_global_status_sort_merge_passes` | Anzahl der Sortierungen, die mehrere Merges erfordern | mal/Sekunde | Sortierpuffer unzureichend oder zu viele Daten zum Sortieren |

Untersuchungsvorschläge: Wenn die Anzahl langsamer Abfragen steigt, prüfen Sie sie anhand der Geschäftsfreigabezeiten und SQL Änderungsaufzeichnungen. Wenn Scan- und Sort-Metriken steigen, beziehen Sie sich normalerweise auf die Langprotokolle, Ausführungspläne und Indexdesign.

### 4.4 InnoDB-Pufferpool

| Überwachungsdimension | Metrik | Metrikbedeutung | Häufig verwendete Maßeinheit | Abnormale Leistung |
| --- | --- | --- | --- | --- |
| Pufferpoolgröße | `mysql_global_variables_innodb_buffer_pool_size` | Konfigurationsgröße des InnoDB-Pufferpools | Bytes | Zu klein erhöht die Festplattenzugriffe |
| Pufferpool-Seiten | `mysql_global_status_buffer_pool_pages` | Anzahl der verschiedenen Arten von Pufferpool-Seiten | Seiten | Wird verwendet, um schmutzige, freie, Daten- und andere Seiten zu überwachen |
| Seitengröße | `mysql_global_status_innodb_page_size` | InnoDB-Seitengröße | Bytes | Wird verwendet, um die Seitenanzahl in Kapazität umzurechnen |

Untersuchungsvorschlag: Wenn die Pufferpool-Trefferquote schlecht ist, greift die Datenbank häufiger auf die Festplatte zu. Es ist notwendig, dies zusammen mit dem Festplatten-Durchsatz und iowait des Knotens zu bewerten IOPS

### 4.5 Temporäre Tabellen, Tabellen-Caches und Dateihandler

| Überwachungsdimension | Metrik | Metrikbedeutung | Häufig verwendete Maßeinheit | Abnormale Leistung |
| --- | --- | --- | --- | --- |
| Temporäre Tabelle | `mysql_global_status_created_tmp_tables` | Gesamtzahl der erstellten temporären Tabellen | Mal/Sekunde | Steigende Abfragekomplexität |
| Temporäre Festplatten-Tabellen | `mysql_global_status_created_tmp_disk_tables` | Gesamtzahl der erstellten temporären Festplatten-Tabellen | mal/Sekunde | Erhöhter Festplatten-I/O-Druck, SQL kann die Geschwindigkeit verringern |
| Temporäre Dateien | `mysql_global_status_created_tmp_files` | Gesamtzahl der erstellten temporären Dateien | mal/Sekunde | Zunahme der temporären Dateien |
| Tabellensperren sofort | `mysql_global_status_table_locks_immediate` | Anzahl der Male, bei denen Tabellensperren sofort erworben wurden | mal/Sekunde | Normale Referenzmetrik |
| Wartende Tabellensperren | `mysql_global_status_table_locks_waited` | Anzahl der Male, bei denen auf Tabellensperren gewartet wurde | mal/Sekunde | Erhöhte Sperrkonkurrenz |
| Table-Cache-Treffer | `mysql_global_status_table_open_cache_hits` | Anzahl der Table-Open-Cache-Treffer | mal/Sekunde | Niedrige Treffer können auf häufiges Öffnen von Tabellen hinweisen |
| Table-Cache-Fehler | `mysql_global_status_table_open_cache_misses` | Anzahl der Table-Open-Cache-Fehler | mal/Sekunde | Table-Cache-Bewertung erforderlich |
| Table-Cache-Überläufe | `mysql_global_status_table_open_cache_overflows` | Anzahl der Table-Open-Cache-Überläufe | mal/Sekunde | Unzureichende Konfiguration oder zu viele Tabellen |
| Geöffnete Tabellen | `mysql_global_status_open_tables` | Aktuelle Anzahl geöffneter Tabellen | Stück | Risiko steigt beim Annähern an das Cache-Limit |
| Tabellen-Cache-Konfiguration | `mysql_global_variables_table_open_cache` | Tabelle_offen_konfigurierter Cache-Wert | Stück | Wird zur Berechnung der Nutzungsrate verwendet |
| Offene Dateien | `mysql_global_status_open_files` | Aktuelle Anzahl offener Dateien | Stück | Kann beeinflussen SQL die Ausführung beim Erreichen des Dateilimits |
| Dateilimit | `mysql_global_variables_open_files_limit` | MySQL Limit für Dateihandles | Stück | Wird verwendet, um die Nutzung von Dateihandles zu berechnen |

Fehlerbehebungsvorschläge: Temporäre Tabellen, Sperrwarten und Tabellen-Cache-Misses treten häufig zusammen mit langsamen Abfragen auf. Wenn die Anzahl der temporären Tabellendateien auf der Festplatte zunimmt, achten Sie auf Schreib-I/O des Knotens, Festplattenlatenz und SQL Sortierung/Gruppierung.

### 4.6 Netzwerkdurchsatz

| Überwachungsdimension | Metrik | Metrikbedeutung | Übliche Einheit | Abnormale Leistung |
| --- | --- | --- | --- | --- |
| Eingehender Datenverkehr | `mysql_global_status_bytes_received` | Kumuliert MySQL empfangene Bytes | Bytes/s | Zunahme des Anfragekörpers oder des Schreibverkehrs |
| Ausgehender Datenverkehr | `mysql_global_status_bytes_sent` | Kumulative gesendete Bytes von MySQL | Bytes/s | Große Abfragen, Full-Table-Scans und Bulk-Exporte erhöhen den ausgehenden Verkehr |

Häufige Abfragen:

```promql
rate(mysql_global_status_bytes_received[5m])
```

```promql
rate(mysql_global_status_bytes_sent[5m])
```

Untersuchungsvorschläge: Wenn MySQL Wenn der ausgehende Verkehr plötzlich zunimmt, sollte normalerweise auf große Ergebnissets, Exportaufgaben und Abfragen ohne Pagination geachtet werden.

## 5. MongoDB Überwachung

MongoDB Überwachung wird verwendet, um den Status der Instanz, die Anzahl der Verbindungen, das Operationsvolumen, das Abfrage-Scanning, die Speichernutzung, den Netzwerkdurchsatz und den Zustand des Replikationspuffers zu beobachten.

### 5.1 Instanzen und Verbindungen

| Überwachungsdimension | Metrik | Metrikbedeutung | Häufige Messung/Einheit | Abnormale Leistung |
| --- | --- | --- | --- | --- |
| Instanz lebt | `up` | Ob der Mongo-Exporter Daten sammeln kann | `0/1` | Wenn `0`, die Instanz oder der Exporter ist abnormal |
| Betriebszeit | `mongodb_ss_uptime` | MongoDB Laufzeit | Sekunden | Kleinere Werte weisen auf einen Instanzneustart hin |
| Anzahl der Verbindungen | `mongodb_ss_connections` | Aktuelle verbindungsbezogene Statistiken | Zählung | Abnormal hohe Anzahl an Verbindungen kann auf Pool-Probleme oder Geschäftsspitzen hinweisen |

Untersuchungsvorschläge: Wenn die Verbindungsanzahl steigt, bestätigen Sie zunächst, ob es eine Geschäftsspitze, Änderungen an der Verbindungspool-Konfiguration oder abnormale Client-Reconnects gibt.

### 5.2 Operationen und Dokumentenverarbeitung

| Überwachungsdimension | Metrik | Metrikbedeutung | Häufige Messung/Einheit | Abnormale Leistung |
| --- | --- | --- | --- | --- |
| Operationsanzahl | `mongodb_ss_opcounters` | Die kumulative Anzahl von Operationen wie Einfügen, Abfragen, Aktualisieren, Löschen | Mal/Sekunde | Ein plötzlicher Anstieg einer bestimmten Art von Operation weist auf eine Veränderung der Geschäftsaccessmuster hin |
| Dokumentenhandhabung | `mongodb_ss_metrics_document` | Kumulative Anzahl von eingefügten, aktualisierten, gelöschten, zurückgegebenen Dokumenten usw. | Mal/Sekunde | Wenn zurückgegebene deutlich höher als tatsächlich benötigt sind, könnte das Ergebnis zu groß sein |
| Indexeinträge gescannt | `mongodb_ss_metrics_queryExecutor_scanned` | Anzahl der während Abfragen gescannten Indexeinträge | Mal/Sekunde | Übermäßiges Scannen kann auf falsche Indexierung hinweisen |
| Dokumente gescannt | `mongodb_ss_metrics_queryExecutor_scannedObjects` | Anzahl der während Abfragen gescannten Dokumente | Mal/Sekunde | Hohe Dokumentenscans weisen auf niedrige Abfrageeffizienz hin |

Häufige Abfragen: 

```promql
sum by (type) (rate(mongodb_ss_opcounters[5m]))
```

Untersuchungsempfehlungen: Eine häufige Erscheinung von MongoDB langsamen Abfragen ist ein Anstieg der gescannten/gescanntenObjekte. Es ist notwendig, dies in Kombination mit langsamen Logs und Index-Treffern zu analysieren.

### 5.3 Speicher, Netzwerk und Festplatte

| Überwachungsdimension | Metrik | Metrikbedeutung | Gängige Einheit/Messung | Abnorme Erscheinung |
| --- | --- | --- | --- | --- |
| Residenter Speicher | `mongodb_ss_mem_resident` | MongoDB Residenter Speicher | MB oder Bytes | Ein kontinuierlicher Anstieg erfordert die Überprüfung des Host-Speichers |
| Virtueller Speicher | `mongodb_ss_mem_virtual` | MongoDB Virtueller Speicher | MB oder Bytes | Ein alleiniger Anstieg zeigt nicht unbedingt echten Druck an |
| Eingehender Verkehr | `mongodb_ss_network_bytesIn` | MongoDB Kumulativ empfangene Bytes | Bytes/s | Anstieg des Schreib- oder Anforderungsverkehrs |
| Ausgehender Verkehr | `mongodb_ss_network_bytesOut` | MongoDB Kumulativ gesendete Bytes | Bytes/s | Große Abfragen oder Exportaufgaben verursachen erhöhten ausgehenden Verkehr |
| Host Lese-IO | `node_disk_reads_completed_total` | Lesen IOPS auf dem Knoten, auf dem MongoDB liegt | Mal/s | Abfragescans verursachen erhöhten Lese-IO |
| Host Schreib-IO | `node_disk_writes_completed_total` | Schreiben IOPS auf dem Knoten, auf dem MongoDB befindet sich | mal/Sekunde | Erhöhter Schreib- oder Journaldruck | 

Fehlerbehebungsvorschlag: MongoDB Speicher- und Festplattenleistung sollte zusammen mit dem Speicher und der Festplatten-IO des Knotens betrachtet werden. Die Betrachtung von Instanzmetriken zusammen mit Host-Lese-/Schreibvorgängen erleichtert die Bestimmung, ob MongoDB die Instanz selbst langsam ist oder die zugrunde liegenden Ressourcen langsam sind. 

### 5.4 Replikationspuffer 

| Überwachungsdimension | Metrik | Metrikbedeutung | Häufige Messung/Einheit | Abnormale Leistung | 
| --- | --- | --- | --- | 
| Replikationspuffergröße | `mongodb_ss_metrics_repl_buffer_sizeBytes` | Größe des Replikationspuffers | Bytes | Kontinuierliches Wachstum des Puffers zeigt, dass die Replikation nicht rechtzeitig konsumiert wird | 

Fehlerbehebungsvorschlag: Abnormale Replikationspuffer hängen meist mit der Verarbeitungskapazität des Slaves, dem Netzwerk oder den Schreibvorgängen auf der Festplatte zusammen. Sie müssen zusammen mit Replikationsverzögerung, Netzwerkknoten und Festplattenschreibmetriken analysiert werden. 

## 6. Redis Überwachung 

Redis Überwachung wird verwendet, um die Verfügbarkeit von Instanzen, Verbindungsanzahl, Befehlsbearbeitung, Speicherniveaus, Keyspace, Trefferquote, Auslagerung und Netzwerkdurchsatz zu beobachten. 

### 6.1 Instanzen und Clients 

| Überwachungsdimension | Metrik | Metrikbedeutung | Häufige Messung/Einheit | Abnormale Leistung | 
| --- | --- | --- | --- | --- |
| Instanz lebt | `up` | Ob Redis Exporter kann gesammelt werden | `0/1` | Wenn `0`, die Instanz oder der Exporter ist abnormal |
| Betriebszeit | `redis_uptime_in_seconds` | Redis Laufzeit | Sekunden | Abnahme zeigt Neustart der Instanz an |
| Client-Verbindungen | `redis_connected_clients` | Aktuelle Anzahl an Client-Verbindungen | Zählung | Plötzlicher Anstieg kann auf Connection-Pool- oder Wiederverbindungssturm hinweisen |

### 6.2 Befehle, Speicher und Keyspace

| Überwachungsdimension | Metrik | Metrikbedeutung | Übliche Einheit | Abnormales Verhalten |
| --- | --- | --- | --- | --- |
| Verarbeitete Befehle | `redis_commands_processed_total` | Gesamtanzahl der Redis verarbeiteten Befehle | Mal/Sekunde | Plötzliche QPS Spitze kann Instanz belasten CPU |
| Befehlsklassifikation | `redis_commands_total` | Gesamtanzahl der Befehle nach Typ | Mal/Sekunde | Kann Änderungen bei get-, set-, del-Befehlen usw. erkennen |
| Benutzter Speicher | `redis_memory_used_bytes` | Aktuell Redis Speichernutzung | Bytes | Annäherung an maxmemory kann Auslagerung auslösen |
| Maximaler Speicher | `redis_memory_max_bytes` | Redis maxmemory-Konfiguration | Bytes | Wird verwendet, um die Speichernutzungsrate zu berechnen |
| Anzahl der Schlüssel | `redis_db_keys` | Anzahl der Schlüssel in jeder DB | Zählung | Abnormales Wachstum der Schlüssel kann auf Cache ohne Ablauf oder Schreibanomalien hinweisen |
| Ablaufende Schlüssel | `redis_db_keys_expiring` | Anzahl der Schlüssel mit konfigurierter Ablaufzeit | Zählung | Niedriger Anteil erfordert Aufmerksamkeit für Cache-Lebenszyklus |

Häufige Abfragen:

```promql
rate(redis_commands_processed_total[5m])
```

```promql
redis_memory_used_bytes / redis_memory_max_bytes * 100
```

### 6.3 Trefferquote, Auslagerung und Netzwerk

| Überwachungsdimension | Metrik | Metrikbedeutung | Häufig verwendete Maßeinheit | Abnormale Leistung |
| --- | --- | --- | --- | --- |
| Trefferzähler | `redis_keyspace_hits_total` | Gesamtanzahl der Schlüssel-Treffer | Mal/s | Trefferquote zusammen mit Fehlschlägen berechnen |
| Fehlerzähler | `redis_keyspace_misses_total` | Gesamtanzahl der Schlüsselverfehlungen | Mal/s | Ein Anstieg der Verfehlungen kann zu höherem Druck zurück zur Quelle führen |
| Abgelaufene Schlüssel | `redis_expired_keys_total` | Gesamtanzahl der abgelaufenen Schlüssel | Mal/s | Ablaufstürme können verursachen CPU Jitter |
| Ausgelöschte Schlüssel | `redis_evicted_keys_total` | Gesamtanzahl der ausgelöschten Schlüssel | Mal/s | Wachstum weist auf Speicherbelastung oder unzureichendes Maximalgedächtnis hin |
| Eingehender Datenverkehr | `redis_net_input_bytes_total` | Gesamte empfangene Bytes von Redis | Bytes/s | Anstieg des Schreib- oder Anforderungsverkehrs |
| Ausgehender Datenverkehr | `redis_net_output_bytes_total` | Gesamte gesendete Bytes von Redis | Bytes/s | Hoher ausgehender Datenverkehr verursacht durch große Werte oder Batch-Lesevorgänge |

Häufige Abfragen:

```promql
rate(redis_keyspace_hits_total[5m]) / (rate(redis_keyspace_hits_total[5m]) + rate(redis_keyspace_misses_total[5m])) * 100
```

```promql
rate(redis_evicted_keys_total[5m])
```

Untersuchungsempfehlung: Für Redis, konzentrieren Sie sich auf Speicher- und Auslöschungsrisiken. Ein Rückgang der Trefferquote überträgt den Druck auf die Backend-Datenbank. Ein Anstieg der Auslöschungen zeigt, dass die Cache-Kapazität oder die Auslöschungsstrategie bewertet werden muss.

## 7. Kafka Überwachung

Kafka Monitoring wird verwendet, um die Anzahl der Broker, den Status von Topic/Partition, Erstellungs- und Verbrauchs-Offsets, Consumer-Group-Verzögerungen, Mitgliederanzahl und den Replikationsstatus zu beobachten.

### 7.1 Broker, Topic und Partition

| Überwachungsdimension | Metrik | Metrikbedeutung | Häufig verwendete Maßeinheit | Abnormale Leistung |
| --- | --- | --- | --- | --- |
| Anzahl der Broker | `kafka_brokers` | Die Anzahl der derzeit sichtbaren Broker | Stück | Ein Rückgang der Anzahl zeigt an, dass der Broker nicht verfügbar ist oder auf den Exporter nicht zugegriffen werden kann |
| Topic-Partitionen | `kafka_topic_partitions` | Anzahl der Partitionen eines Topics | Stück | Änderungen an Partitionen beeinflussen die Parallelität und Verbrauchskapazität |
| Aktueller Partition-Offset | `kafka_topic_partition_current_offset` | Neuester Offset der Partition | Offset / Wachstumsrate | Soll während fortlaufender Schreibvorgänge kontinuierlich ansteigen |
| Ältester Partition-Offset | `kafka_topic_partition_oldest_offset` | Ältester Partition-Offset | Offset | Wird verwendet, um den Bereich der gespeicherten Daten zu beobachten |

Häufige Abfragen: 

```promql
sum by (topic) (rate(kafka_topic_partition_current_offset[5m]))
```

Untersuchungsvorschlag: Wenn die Produktionsrate abnormal ist, überprüfen Sie zuerst das aktuelle Offset-Wachstum des Themas. Wenn das Geschäft bestätigt, dass Schreibvorgänge vorhanden sind, aber das Offset nicht zunimmt, überprüfen Sie auf Fehler auf der Produzentenseite, den Broker-Status und die Topic-Konfiguration.

### 7.2 Verbrauchergruppe und Rückstand

| Überwachungsdimension | Metrik | Metrikbedeutung | Häufige Messung/Einheit | Abnormale Leistung |
| --- | --- | --- | --- | --- |
| Konsum-Offset | `kafka_consumergroup_current_offset` | Aktuell vom Verbrauchergruppe konsumiertes Offset | Offset / Wachstumsrate | Kein Wachstum zeigt an, dass der Konsum gestoppt oder blockiert ist |
| Partition-Rückstand | `kafka_consumergroup_lag` | Rückstand der Verbrauchergruppe auf der Partition | Zählung | Zunehmender Rückstand zeigt an, dass der Konsum hinter der Produktion zurückbleibt |
| Gesamtrückstand der Gruppe | `kafka_consumergroup_lag_sum` | Gesamtrückstand der Verbrauchergruppe | Zählung | Kontinuierlicher Anstieg des Gesamtrückstands weist auf wachsende Verzögerungen im Geschäft hin |
| Gruppenmitglieder | `kafka_consumergroup_members` | Anzahl der Mitglieder in der Verbrauchergruppe | Zählung | Ein Rückgang der Mitgliederzahl kann zu einer verringerten Konsumkapazität führen |

Häufige Abfragen:

```promql
sum by (consumergroup, topic) (kafka_consumergroup_lag)
```

```promql
sum by (consumergroup, topic) (rate(kafka_consumergroup_current_offset[5m]))
```

Fehlerbehebungsvorschläge: Die Kernkennzahl des Geschäfts von Kafka ist Lag. Wenn der Lag zunimmt, prüfen Sie zunächst, ob die Anzahl der Konsumentenmitglieder abgenommen hat, dann, ob die Konsumrate gesunken ist, und schließlich die Anwendungsbearbeitungszeit, nachgelagerte Abhängigkeiten und Broker-IO.

### 7.3 Replikate und ISR

| Überwachungsdimension | Metrik | Metrikbedeutung | Häufige Messung/Einheit | Abnormale Leistung |
| --- | --- | --- | --- | --- |
| Anzahl der Replikate | `kafka_topic_partition_replicas` | Anzahl der Partitionsreplikate | Zählung | Weniger Replikate als erwartet verringern die Zuverlässigkeit |
| ISR Replikate | `kafka_topic_partition_in_sync_replica` | Anzahl der Partitions-In-Sync-Replikate | Zählung | Ein Rückgang von ISR weist auf nachlaufende Replikate oder Broker-Probleme hin |
| Bevorzugter Leader | `kafka_topic_partition_leader_is_preferred` | Ob der Leader die bevorzugte Replik ist | `0/1` | Langfristige Ungleichgewichte können hohen Druck auf einige Broker verursachen |

Fehlerbehebungsvorschläge: Ein Rückgang von ISR stellt ein Zuverlässigkeitsrisiko dar, mehr als gewöhnliche Verzögerung. Überprüfen Sie den Broker-Status, das Netzwerk, die Schreiblatenz der Festplatte und die Replikasynchronisation.

## 8. MinIO Objektspeicherüberwachung

MinIO Überwachung wird verwendet, um die Verfügbarkeit des Objektspeicher-Clusters, den Knoten- und Festplattenstatus, die Bucket-Kapazität, S3 Anfragen, Fehler, Traffic, Prozess-Handles und Aktivitäten von Reparaturaufgaben zu beobachten. 

### 8.1 Clusterknoten und Festplatten 

| Überwachungsdimension | Metrik | Metrikbedeutung | Häufig verwendete Maßeinheit | Abnorme Erscheinung |
| --- | --- | --- | --- | --- |
| Online-Knoten | `minio_cluster_nodes_online_total` | Anzahl der Online- MinIO Knoten | Stück | Eine Abnahme der Anzahl zeigt an, dass Knoten nicht verfügbar sind |
| Offline-Knoten | `minio_cluster_nodes_offline_total` | Anzahl der Offline- MinIO Knoten | Stück | Größer als 0 erfordert Aufmerksamkeit für die Clusterverfügbarkeit |
| Online-Festplatten | `minio_cluster_disk_online_total` | Anzahl der Online-Festplatten | Stück | Eine Abnahme der Festplatten beeinflusst Redundanz und Schreibfähigkeit |
| Offline-Festplatten | `minio_cluster_disk_offline_total` | Anzahl der Offline-Festplatten | Stück | Größer als 0 erfordert Fehlerbehebung bei Festplatten oder Mounts |
| Verwendbare Kapazität | `minio_cluster_capacity_usable_free_bytes` | Verwendbare Clusterkapazität | Bytes | Kontinuierlicher Rückgang zeigt Risiko unzureichender Kapazität an |

Fehlerbehebungsvorschlag: Prüfen Sie beim Objektspeicher zuerst den Online-Status der Knoten und Festplatten. Beurteilen Sie Offline-Festplatten nicht allein anhand der Anzahl; das Risiko sollte in Kombination mit der Redundanzstrategie des Erasure-Codes bewertet werden. 

### 8.2 Bucket-Kapazität und Objektanzahl

| Überwachungsdimension | Metrik | Metrikbedeutung | Häufig verwendete Maßeinheit | Abnormale Leistung |
| --- | --- | --- | --- | --- |
| Bucket-Kapazität | `bucket_usage_size` | Genutzte Kapazität des Buckets | Bytes | Schnelles Wachstum der Kapazität, Erweiterung muss evaluiert werden |
| Objektanzahl | `bucket_objects_count` | Anzahl der Objekte im Bucket | Anzahl | Zu viele kleine Objekte erhöhen die Metadaten- und Scanlast |
| Objektgrößenverteilung | `minio_bucket_objects_size_distribution` | Verteilung der Objektgrößen im Bucket | Bucket-Statistiken | Änderungen in der Objektverteilung beeinflussen Speicher- und Anfrageleistung |

Häufige Abfragen:

```promql
sum by (bucket) (bucket_usage_size)
```

```promql
sum by (bucket) (bucket_objects_count)
```

Untersuchungsempfehlungen: Das Kapazitätswachstum sollte separat nach Bucket analysiert werden. Wenn die Anzahl der Objekte schnell wächst, das Kapazitätswachstum jedoch nicht offensichtlich ist, liegt dies normalerweise an einer Zunahme kleiner Objekte. Aufmerksamkeit sollte auf die Bereinigung des Lebenszyklus und das Schreibverhalten des Geschäfts gelegt werden.

### 8.3 S3 Anfragen, Fehler und Verkehr

| Überwachungsdimension | Metrik | Metrikbedeutung | Häufige Messung/Einheit | Abnormale Leistung |
| --- | --- | --- | --- | --- |
| S3 Anfrageanzahl | `minio_s3_requests_total` | Kumulative Anzahl von S3 API Anfragen | Mal/Sekunde | Plötzlicher Anstieg der Anfragen, kann Geschäftsspitze oder Wiederholungen sein |
| S3 Fehleranzahl | `minio_s3_requests_errors_total` | Kumulative Anzahl von S3 API Fehler | Mal/Sekunde | Erhöhte Fehlerrate, die das Lesen/Schreiben von Objekten beeinflusst |
| Empfangener Verkehr | `minio_s3_traffic_received_bytes` | Kumuliert S3 empfangene Bytes | Bytes/s | Erhöhter Upload-Verkehr |
| Gesendeter Verkehr | `minio_s3_traffic_sent_bytes` | Kumuliert S3 gesendete Bytes | Bytes/s | Erhöhter Download- oder Origin-Abrufverkehr |

Häufige Abfragen:

```promql
sum by (api) (rate(minio_s3_requests_total[5m]))
```

```promql
sum(rate(minio_s3_requests_errors_total[5m])) / sum(rate(minio_s3_requests_total[5m])) * 100
```

Untersuchungsempfehlung: Wenn die S3 Fehlerrate steigt, unterteilen Sie sie zuerst nach API Typ, prüfen Sie dann den entsprechenden Bucket, den Status der Knotendisk und den Netzwerkverkehr.

### 8.4 Knotenprozesse, Datei-Handles und IO

| Überwachungsdimension | Metrik | Metrikbedeutung | Häufig verwendete Maßeinheit | Abnorme Erscheinung |
| --- | --- | --- | --- | --- |
| Knotendisknutzung | `minio_node_disk_used_bytes` | Disk Nutzung des MinIO Knotens | Bytes | Kapazitätsungleichgewicht einzelner Knoten |
| Offene Datei-Handles | `minio_node_file_descriptor_open_total` | Anzahl der vom MinIO Prozess geöffneten Datei-Handles | Anzahl | Anfragen können fehlschlagen, wenn das Systemlimit erreicht wird |
| Lese-Systemaufrufe | `minio_node_syscall_read_total` | Kumulative Anzahl der Lese-Systemaufrufe | Mal/Sekunde | Abnormer Anstieg der Leseaufrufe |
| Schreib-Systemaufrufe | `minio_node_syscall_write_total` | Kumulative Anzahl der Schreib-Systemaufrufe | Mal/Sekunde | Abnormer Anstieg der Schreibaufrufe |
| Prozess Lese-Bytes | `minio_node_io_rchar_bytes` | Kumulative vom Prozess gelesene Bytes | Bytes/s | Anstieg der Leseauslastung |
| Prozess Schreib-Bytes | `minio_node_io_wchar_bytes` | Kumulative vom Prozess geschriebene Bytes | Bytes/s | Anstieg der Schreiblast |
| Anzahl der Goroutinen | `minio_node_go_routine_total` | Anzahl der Goroutinen in der MinIO Prozess geöffneten Datei-Handles | Anzahl | Stetiges Wachstum kann auf Anfragenrückstand oder Leckagen hinweisen |
| Startzeit | `minio_node_process_starttime_seconds` | MinIO Prozessstartzeit | Unix-Zeitstempel | Änderungen weisen auf Neustart des Prozesses hin |

Untersuchungsvorschlag: Für MinIO Leistungsprobleme sollten S3 Anfragen, Knotenlaufwerke, Prozess-IO und Goroutinen zusammen betrachtet werden. Ein hohes Anfragenvolumen allein ist nicht unbedingt abnormal; Fehlerrate, IO-Latenz und Laufwerks-Offline-Status sind klarere Risikosignale.

### 8.5 Heil- und Nutzungsaktivitäten

| Überwachungsdimension | Metrik | Metrikbedeutung | Üblicher Standard/Einheit | Abnormales Verhalten |
| --- | --- | --- | --- | --- |
| Heilaktivität | `minio_heal_time_last_activity_nano_seconds` | Letzte Heilaktivitätszeit | Nanosekunden-Zeitstempel | Lange oder häufige Heils erfordern Aufmerksamkeit für die Laufwerksgesundheit |
| Nutzungsaktivität | `minio_usage_last_activity_nano_seconds` | Letzte Nutzungs-Scan-Aktivitätszeit | Nanosekunden-Zeitstempel | Abnormale Nutzungsscans können die Genauigkeit der Kapazitätsstatistiken beeinflussen |

Untersuchungsvorschlag: Nach abnormaler Wiederherstellung von Knoten oder Laufwerk überwachen, ob Heilaktivitäten normal fortschreiten, um zu verhindern, dass Objektredundanz lange gefährdet bleibt.

## 9. Elasticsearch Überwachung

Elasticsearch Monitoring wird verwendet, um die Gesundheit des Suchclusters, die Anzahl der Knoten, die Verteilung der Shards, Lese-/Schreiboperationen von Indizes, Cache, JVM, Threadpools, Laufwerk und Netzwerk zu beobachten. ES-Fehler werden normalerweise nicht durch eine einzelne Metrik bestimmt; häufiger treten "Shard-Anomalien   JVM Druck   Thread-Pool-Ablehnungen   Laufwerks-Wasserzeichen" zusammen auf.

### 9.1 Cluster-Gesundheit und Knoten

| Überwachungsdimension | Metrik | Metrikbedeutung | Häufige Messung/Einheit | Abnormales Verhalten |
| --- | --- | --- | --- | --- |
| Cluster-Gesundheit | `elasticsearch_cluster_health_status` | ES-Cluster-Gesundheitsstatus | Statuswert | Gelb/Rot weist auf Anomalien bei Replikaten oder Primär-Shards hin |
| Anzahl der Knoten | `elasticsearch_cluster_health_number_of_nodes` | Anzahl der Clusteknoten | Anzahl | Abnahme der Knotenanzahl kann darauf hinweisen, dass ein Knoten offline ist |
| Anzahl der Datenknoten | `elasticsearch_cluster_health_number_of_data_nodes` | Anzahl der Datenknoten im Cluster | Anzahl | Eine Abnahme der Datenknoten beeinflusst die Shard-Kapazität und die Lese-/Schreibfähigkeit |
| Ausstehende Aufgaben | `elasticsearch_cluster_health_number_of_pending_tasks` | Anzahl der ausstehenden Aufgaben im Cluster | Anzahl | Kontinuierliches Wachstum weist darauf hin, dass Master- oder Clusterstatusaktualisierungen langsam sind |
| Aktive Primär-Shards | `elasticsearch_cluster_health_active_primary_shards` | Anzahl der aktiven Primär-Shards | Stück | Hohes Risiko bei Abnahme, kann die Indexverfügbarkeit beeinträchtigen |
| Aktive Shards | `elasticsearch_cluster_health_active_shards` | Gesamtanzahl aktiver Shards | Stück | Abnahme weist darauf hin, dass Shards nicht vollständig wiederhergestellt sind |
| Initialisierende Shards | `elasticsearch_cluster_health_initializing_shards` | Anzahl der initialisierenden Shards | Stück | Keine Abnahme über einen langen Zeitraum weist auf langsame Wiederherstellung hin |
| Verschiebende Shards | `elasticsearch_cluster_health_relocating_shards` | Anzahl der verschiebenden Shards | Stück | Zu viele Verschiebungen erhöhen Netzwerk- und Festplattendruck |
| Nicht zugewiesene Shards | `elasticsearch_cluster_health_unassigned_shards` | Anzahl der nicht zugewiesenen Shards | Stück | Größer als 0 weist darauf hin, dass Shards keinem Knoten zugewiesen sind |
| Verzögerte Nichtzuweisung | `elasticsearch_cluster_health_delayed_unassigned_shards` | Anzahl der verzögert nicht zugewiesenen Shards | Stück | Warten auf Neuzuweisung nach Ausfall eines Knotens |

Häufige Abfragen: 

```promql
elasticsearch_cluster_health_status
```

```promql
elasticsearch_cluster_health_unassigned_shards
```

Untersuchungsvorschläge: ES sollte zuerst den Gesundheitsstatus und nicht zugewiesene Shards prüfen. Roter Status sollte die Handhabung der Primär-Shards priorisieren; Gelb wird meist durch nicht zugewiesene Replikate verursacht, die auch nicht lange unbeachtet bleiben dürfen. 

### 9.2 Festplattenkapazität und Dateisystem

| Überwachungsdimension | Metrik | Metrikbedeutung | Gängige Messung / Einheit | Abnormale Leistung |
| --- | --- | --- | --- | --- |
| Gesamter Daten-Datenträger | `elasticsearch_filesystem_data_size_bytes` | Gesamtkapazität des ES-Datenverzeichnisses | Bytes | Wird verwendet, um die Festplattenauslastung zu berechnen |
| Verfügbarer Daten-Datenträger | `elasticsearch_filesystem_data_available_bytes` | Verfügbare Kapazität des ES-Datenverzeichnisses | Bytes | Unzureichender verfügbarer Speicherplatz kann Shard-Migrationen oder Schreibbeschränkungen auslösen |

Häufige Abfragen:

```promql
(1 - elasticsearch_filesystem_data_available_bytes / elasticsearch_filesystem_data_size_bytes) * 100
```

Untersuchungsvorschlag: ES ist sehr empfindlich gegenüber Festplattennutzung. Wenn die Festplattennutzung zu hoch ist, können Shard-Migrationen, schreibgeschützte Indizes oder Schreibfehler auftreten. Es ist notwendig, das Wachstum der Indizes, Aufbewahrungsrichtlinien und die Distributionsverteilung der Knotenlaufwerke zu überwachen.

### 9.3 Dokumente, Indizes und Löschungen

| Überwachungsdimension | Metrik | Metrikbedeutung | Übliche Einheit | Abnormales Verhalten |
| --- | --- | --- | --- | --- |
| Dokumentanzahl | `elasticsearch_indices_docs` | Aktuelle Anzahl der Dokumente | Zählung | Schnelles, stetiges Wachstum der Dokumente erfordert Kapazitätsbewertung |
| Gelöschte Dokumente | `elasticsearch_indices_docs_deleted` | Anzahl der gelöschten Dokumente | Zählung | Hohe Löschrate kann Merge-Druck verursachen |
| Index-Schreibanzahl | `elasticsearch_indices_indexing_index_total` | Kumulative Anzahl von Indexoperationen | mal/Sekunde | Plötzlicher Anstieg der Schreibvorgänge erhöht CPU, Festplatten- und Aktualisierungsdruck |
| Index-Schreibzeit | `elasticsearch_indices_indexing_index_time_seconds_total` | Kumulative Zeit der Indexoperationen | Sekunden/Sekunde | Anstieg der Schreibzeit verlangsamt den Schreibpfad |
| Löschvorgang Anzahl | `elasticsearch_indices_indexing_delete_total` | Kumulative Anzahl von Löschoperationen | mal/Sekunde | Plötzlicher Anstieg der Löschungen kann Segment-Merge-Druck verursachen |
| Löschvorgang Dauer | `elasticsearch_indices_indexing_delete_time_seconds_total` | Kumulative Dauer von Löschvorgängen | Sekunden/Sekunde | Anstieg der Löschdauer |

Häufige Abfragen:

```promql
sum by (cluster) (rate(elasticsearch_indices_indexing_index_total[5m]))
```

```promql
rate(elasticsearch_indices_indexing_index_time_seconds_total[5m]) / rate(elasticsearch_indices_indexing_index_total[5m])
```

Fehlerbehebungsempfehlung: Wenn Schreibvorgänge langsam sind, betrachten Sie nicht nur das Schreiben QPS. Sie sollten auch Aktualisierung, Merge, Translog, Threadpool-Ablehnungen und Festplatten-IO berücksichtigen.

### 9.4 Abfragen und Get-Anfragen

| Überwachungsdimension | Metrik | Metrikbedeutung | Häufige Messung/Einheit | Abnormales Verhalten |
| --- | --- | --- | --- | --- |
| Anzahl der Abfrageanfragen | `elasticsearch_indices_search_query_total` | Kumulative Anzahl von Suchabfragen | Mal/Sekunde | Plötzlicher Anstieg der Abfragen |
| Abfrage-Latenz | `elasticsearch_indices_search_query_time_seconds` | Kumulative Zeit der Suchabfragen | Sekunden/Sekunde | Anstieg der durchschnittlichen Abfragelatenz |
| Anzahl der Fetch-Anfragen | `elasticsearch_indices_search_fetch_total` | Kumulative Anzahl in der Such-Fetch-Phase | Mal/Sekunde | Große Ergebnismengen können die Fetch-Anzahl erhöhen |
| Fetch-Latenz | `elasticsearch_indices_search_fetch_time_seconds` | Kumulative Zeit des Such-Fetch | Sekunden/Sekunde | Langsamer Fetch hängt normalerweise mit großen Ergebnismengen, Festplatte oder Netzwerk zusammen |
| Anzahl der Get-Anfragen | `elasticsearch_indices_get_exists_total`, `elasticsearch_indices_get_missing_total` | Kumulative Anzahl von Get-Treffern und -Fehlschlägen | Mal/Sekunde | Ein Anstieg der Fehlschläge kann auf den Zugriff des Geschäfts auf nicht vorhandene Dokumente hinweisen |
| Get-Dauer | `elasticsearch_indices_get_time_seconds`, `elasticsearch_indices_get_exists_time_seconds`, `elasticsearch_indices_get_missing_time_seconds` | Kumulative Zeit der Get-Anfragen | Sekunden/Sekunde | Langsames Abrufen zeigt steigenden Druck auf den Lesepfad an |

Häufige Abfragen: 

```promql
rate(elasticsearch_indices_search_query_time_seconds[5m]) / rate(elasticsearch_indices_search_query_total[5m])
```

```promql
rate(elasticsearch_indices_search_fetch_time_seconds[5m]) / rate(elasticsearch_indices_search_fetch_total[5m])
```

Fehlerbehebungsempfehlungen: Langsame Abfragen sollten zwischen Abfrage und Abruf unterschieden werden. Langsame Abfragen hängen eher mit Abfragebedingungen, Indexstruktur und Shard-Druck zusammen; langsame Abrufe treten häufiger auf, wenn viele zurückgegebene Felder, große Ergebnissets oder langsame Festplattenzugriffe vorhanden sind.

### 9.5 Segment, Merge, Aktualisierung und Translog

| Überwachungsdimension | Metrik | Metrikbedeutung | Häufig verwendete Maßeinheit | Abnormale Symptome |
| --- | --- | --- | --- | --- |
| Anzahl der Segmente | `elasticsearch_indices_segments_count` | Aktuelle Anzahl der Segmente | Zählung | Zu viele Segmente können Abfragen und Speicher beeinträchtigen |
| Segment-Speicher | `elasticsearch_indices_segments_memory_bytes` | Von Segmenten belegter Speicher | Bytes | Kontinuierlicher Anstieg kann zusammendrücken JVM |
| Anzahl der Zusammenführungen | `elasticsearch_indices_merges_total` | Kumulative Anzahl von Merge-Operationen | mal/Sekunde | Häufige Zusammenführungen weisen auf hohen Schreib- oder Löschdruck hin |
| Anzahl der Dokumente in Merge | `elasticsearch_indices_merges_docs_total` | Kumulative Anzahl der von Merges verarbeiteten Dokumente | Anzahl/Sekunde | Steigende Merge-Arbeitslast |
| Merge-Datenvolumen | `elasticsearch_indices_merges_total_size_bytes_total` | Kumulative von Merge verarbeitete Daten | Bytes/s | Große Zusammenführungen können die Festplatten-E/A auslasten |
| Zusammenführungsdauer | `elasticsearch_indices_merges_total_time_seconds_total` | Kumulative Zeit für Zusammenführungen | Sekunden/Sekunde | Langsame Zusammenführungen können die Schreib- und Abfrageleistung beeinträchtigen |
| Aktualisierungsanzahl | `elasticsearch_indices_refresh_total` | Kumulative Anzahl an Aktualisierungen | Mal/Sekunde | Häufige Aktualisierungen erhöhen den Overhead |
| Aktualisierungsdauer | `elasticsearch_indices_refresh_time_seconds_total` | Kumulative Aktualisierungszeit | Sekunden/Sekunde | Langsame Aktualisierung wirkt sich auf die nahezu Echtzeit-Sichtbarkeit aus |
| Flush-Anzahl | `elasticsearch_indices_flush_total` | Kumulative Anzahl der Flushes | Mal/Sekunde | Häufige Flushes können mit Translog- und Schreibdruck zusammenhängen |
| Flush-Dauer | `elasticsearch_indices_flush_time_seconds` | Kumulative Flush-Zeit | Sekunden/Sekunde | Langsame Flushes können die Stabilität beeinträchtigen |
| Translog-Operationen | `elasticsearch_indices_translog_operations` | Aktuelle Anzahl der Translog-Operationen | Zählung | Kontinuierliche Ansammlung erfordert Aufmerksamkeit beim Flush |
| Translog-Größe | `elasticsearch_indices_translog_size_in_bytes` | Aktuelle Translog-Größe | Bytes | Übermäßige Größe kann die Wiederherstellungszeit beeinflussen |
| Speicher-Drosselung | `elasticsearch_indices_store_throttle_time_seconds_total` | Kumulative Zeit der Indexspeicher-Drosselung | Sekunden/Sekunde | Verstärkte Drosselung, Schreibvorgänge durch Festplatte beeinflusst |

Untersuchungsvorschlag: Bei hohem Schreibdruck ändern sich Merge, Refresh, Flush und Translog gleichzeitig. Ein Anstieg der Merge-Zeit und der Speicher-Drosselung deutet normalerweise darauf hin, dass die Festplatte begonnen hat, Elasticsearch zu beeinflussen.

### 9.6 Cache und Circuit Breaker

| Überwachungsdimension | Metrik | Bedeutung der Metrik | Gängige Einheit/Messung | Abnormales Verhalten |
| --- | --- | --- | --- | --- |
| Query-Cache-Speicher | `elasticsearch_indices_query_cache_memory_size_bytes` | Vom Query-Cache verwendeter Speicher | Bytes | Übermäßige Nutzung kann die JVM |
| Query-Cache-Auslagerungen | `elasticsearch_indices_query_cache_evictions` | Kumulative Anzahl der Query-Cache-Auslagerungen | Mal/Sekunde | Häufige Auslagerungen deuten auf instabilen Cache hin |
| Fielddata-Speicher | `elasticsearch_indices_fielddata_memory_size_bytes` | Vom Fielddata verwendeter Speicher | Bytes | Hohe Fielddata-Nutzung kann leicht Speicherprobleme auslösen |
| Fielddata-Auslagerungen | `elasticsearch_indices_fielddata_evictions` | Kumulative Anzahl der Fielddata-Auslagerungen | Mal/Sekunde | Hoher Abfrage- oder Aggregationsdruck |
| Filter-Cache-Auslagerungen | `elasticsearch_indices_filter_cache_evictions` | Kumulative Anzahl der Filter-Cache-Auslagerungen | Mal/Sekunde | Häufige Filter-Cache-Invalidierung |
| Geschätzte Größe des Breakers | `elasticsearch_breakers_estimated_size_bytes` | Geschätzter Speicher des Circuit Breakers | Bytes | Abfragen können abgelehnt werden, wenn das Limit erreicht wird |
| Breaker-Limit | `elasticsearch_breakers_limit_size_bytes` | Limit des Circuit Breakers | Bytes | Wird zur Berechnung der Breaker-Auslastungsrate verwendet |
| Breaker-Auslösung | `elasticsearch_breakers_tripped` | Anzahl der Auslösungen des Circuit Breakers | Mal/Zunahme | Wachstumsbeschreibung: Anfragen aufgrund von Speichergefahr blockiert |

Häufige Abfragen: 

```promql
elasticsearch_breakers_estimated_size_bytes / elasticsearch_breakers_limit_size_bytes * 100
```

```promql
increase(elasticsearch_breakers_tripped[10m])
```

Untersuchungsempfehlungen: Aggregationsabfragen, Sortierung und Skriptabfragen können leicht die Fielddata- und Breaker-Nutzung erhöhen. Wenn der Breaker ausgelöst wird, ist es normalerweise notwendig, die Abfragegröße zu begrenzen, die Indexabbildung zu optimieren oder die Abfragemethode anzupassen.

### 9.7 JVM, CPUund Auslastung

| Überwachungsdimension | Metrik | Metrikbedeutung | Häufige Messung/Einheit | Abnormale Leistung |
| --- | --- | --- | --- | --- |
| JVM Genutzter Speicher | `elasticsearch_jvm_memory_used_bytes` | Aktuell JVM verwendeter Speicher | Bytes | Kontinuierlich nahe dem Limit, erhöhter GC-Druck |
| JVM Maximaler Speicher | `elasticsearch_jvm_memory_max_bytes` | Maximal verfügbar JVM Speicher | Bytes | Verwendet zur Berechnung JVM Nutzung |
| JVM Zugewiesener Speicher | `elasticsearch_jvm_memory_committed_bytes` | JVM zugewiesener Speicher | Bytes | Beobachten JVM Speicherzuweisung |
| JVM Speicherpool-Peak | `elasticsearch_jvm_memory_pool_peak_used_bytes` | Spitzenutzung jedes Speicherpools | Bytes | Hohe Spitzen in der alten Generation erfordern Aufmerksamkeit |
| GC-Anzahl | `elasticsearch_jvm_gc_collection_seconds_count` | Anzahl der GC-Vorkommen | Mal/Sekunde | Häufige GC, Latenz kann schwanken |
| GC-Zeit | `elasticsearch_jvm_gc_collection_seconds_sum` | Gesamt-GC-Zeit | Sekunden/Sekunde | Hohe GC-Zeit kann Abfragen und Schreibvorgänge beeinflussen |
| Prozess CPU | `elasticsearch_process_cpu_percent` | ES-Prozess CPU Nutzung | Prozentsatz | Langanhaltend hoch CPU kann auf starke Abfrage- oder Schreiblast hindeuten |
| Systemlast | `elasticsearch_os_load1`, `elasticsearch_os_load5`, `elasticsearch_os_load15` | Node 1/5/15 Minuten Last | Lastwert | Last höher als CPU Kerne zeigt deutliche Aufgabenwarteschlangen an |
| Anzahl geöffneter Dateien | `elasticsearch_process_open_files_count` | Anzahl der vom ES-Prozess geöffneten Dateien | Zählung | Annäherung an Systemgrenzen kann Indexdateizugriff beeinträchtigen |

Häufige Abfragen: 

```promql
elasticsearch_jvm_memory_used_bytes / elasticsearch_jvm_memory_max_bytes * 100
```

```promql
rate(elasticsearch_jvm_gc_collection_seconds_sum[5m])
```

Untersuchungsvorschlag: Größerer ES JVM Speicher ist nicht immer besser. JVM Nutzung, GC-Zeit, Fielddata, Query-Cache und Breakers sollten zusammen überwacht werden, um festzustellen, ob Speicherstress durch Abfragen oder eine Diskrepanz zwischen Heap-Größe und Datenumfang verursacht wird.

### 9.8 Thread-Pool und Netzwerk

| Überwachungsdimension | Metrik | Metrikbedeutung | Häufige Messung/Einheit | Abnormale Leistung |
| --- | --- | --- | --- | --- |
| Aktive Threads | `elasticsearch_thread_pool_active_count` | Anzahl aktiver Threads im Thread-Pool | Anzahl | Langfristig hohe aktive Threads weisen auf starken Verarbeitungsdruck hin |
| Abgeschlossene Aufgaben | `elasticsearch_thread_pool_completed_count` | Kumulative Anzahl der vom Thread-Pool abgeschlossenen Aufgaben | Mal/Sekunde | Wird zur Beobachtung des Verarbeitungsthroughputs verwendet |
| Abgelehnte Aufgaben | `elasticsearch_thread_pool_rejected_count` | Kumulative Anzahl der vom Thread-Pool abgelehnten Aufgaben | Mal/Sekunde | Zunahme zeigt, dass Thread-Pool oder Warteschlange voll ist |
| Eingehender Verkehr | `elasticsearch_transport_rx_size_bytes_total` | Kumulative empfangene Bytes durch Transport | Bytes/s | Erhöhte Kommunikation oder Anfrageverkehr zwischen Knoten |
| Ausgehender Verkehr | `elasticsearch_transport_tx_size_bytes_total` | Kumulative gesendete Bytes durch den Transport | Bytes/s | Erhöhter Verkehr durch Shard-Umverlagerung, Abfragen oder Replikation |

Häufige Abfragen: 

```promql
sum by (type) (rate(elasticsearch_thread_pool_rejected_count[5m]))
```

```promql
rate(elasticsearch_transport_rx_size_bytes_total[5m]) + rate(elasticsearch_transport_tx_size_bytes_total[5m])
```

Untersuchungsvorschlag: Die Ablehnung im Thread-Pool ist ein sehr direktes Signal für geschäftliches Risiko. Bei Schreibablehnungen prüfen Sie den Bulk-/Index-Thread-Pool; bei Suchablehnungen prüfen Sie den Such-Thread-Pool und ermitteln dann Engpässe in Kombination mit CPU, JVM, und Festplatten-IO.

## 10. Überwachung des Anwendungsdienstes

Die Anwendungsüberwachung umfasst typische serverseitige Anfragen, clientseitige Abhängigkeitsaufrufe, Laufzeitressourcen, kollaborative Bearbeitungsgeschäftslinks und RS-Dienstaufgaben. Der Fokus der Anwendungsmetriken liegt nicht auf einzelnen Ressourcenschwellenwerten, sondern auf Anfragevolumen, Fehlern, Latenz und Gesundheitszustand der Abhängigkeiten.

### 10.1 Typische serverseitige Metriken

| Überwachungsdimension | Metrik | Metrikbedeutung | Häufiger Umfang/Einheit | Abnormale Leistung |
| --- | --- | --- | --- | --- |
| Dienstlaufzeit | `up` | Ob der Anwendungs-Exporter oder Metrik-Endpunkt sammelbar ist | `0/1` | `0` bedeutet, dass Metriken nicht zugänglich sind oder der Dienst abnormal ist |
| Build-Informationen | `ego_build_info` | Anwendungs-Build-Version, Branch und andere Informationen | Label-Informationen | Wird verwendet, um die Release-Version zu überprüfen |
| Startanzahl | `ego_server_started_total` | Kumulative Anzahl der Serverstarts | Mal/Zunahme | Zunahme weist auf einen Prozessneustart oder Release hin |
| Serveranfragen | `ego_server_handle_total` | Kumulative Anzahl der Serveranfragen | Mal/Sekunde | Ein plötzlicher Anstieg oder Abfall der Anfragen muss in Kombination mit dem Geschäftskontext beurteilt werden |
| Serverseitiger Zeitaufwand | `ego_server_handle_seconds_count`, `ego_server_handle_seconds_bucket` | Statistik der serverseitigen Anforderungszeit | P50/P95/P99 | Erhöhte Latenz beeinträchtigt die Benutzererfahrung | 

Häufige Abfragen: 

```promql
sum by (service, method) (rate(ego_server_handle_total[5m]))
```

```promql
histogram_quantile(0.95, sum(rate(ego_server_handle_seconds_bucket[5m])) by (le, service, method))
```

Untersuchungsvorschläge: Bei Anomalien auf der Serverseite überprüfen Sie zunächst, ob das Anfragevolumen sich verändert hat, und sehen Sie sich dann Fehler und Latenz an. Wenn die Latenz zunimmt, die Ressourcen jedoch nicht hoch sind, untersuchen Sie weiterhin die Aufrufe und Warteschlangen der nachgelagerten Abhängigkeiten.

### 10.2 Client-Abhängigkeitsaufrufe

| Überwachungsdimension | Metrik | Bedeutung der Metrik | Übliche Granularität/Einheit | Abnormales Verhalten |
| --- | --- | --- | --- | --- |
| Client-Aufrufvolumen | `ego_client_handle_total` | Die Anzahl der Male, die die Anwendung als Client nachgelagerte Dienste aufruft | Mal/Sekunde | Plötzlicher Anstieg des Aufrufvolumens nachgelagerter Dienste, was den Abhängigkeitsdruck verstärken kann |
| Client-Latenz | `ego_client_handle_seconds_count`, `ego_client_handle_seconds_bucket` | Statistiken zur Latenz nachgelagerter Aufrufe | P50/P95/P99 | Langsame nachgelagerte Dienste können den aktuellen Dienst verlangsamen |
| Client-Status | `ego_client_stats_gauge` | Verbindungs-Pool oder Statusmetriken des Clients | Aktueller Wert | Erschöpfung des Verbindungspools, abnormale Leerlaufverbindungen usw. |
| Kafka Produktionslatenz | `kafka_produce_duration_seconds_bucket` | Zeit, die die Anwendung zur Produktion benötigt Kafka Nachrichten | P50/P95/P99 | Erhöhte Produktionslatenz, möglicherweise aufgrund von Broker- oder Netzwerkproblemen |

Häufige Abfragen:

```promql
histogram_quantile(0.95, sum(rate(ego_client_handle_seconds_bucket[5m])) by (le, service, target, method))
```

Untersuchungsvorschlag: Wenn eine Geschäftsschnittstelle langsam ist, vergleichen Sie die auf der Serverseite verbrauchte Zeit mit der von Client-Abhängigkeiten verbrachten Zeit. Wenn der Client-Zeitanteil hoch ist, überprüfen Sie vorrangig die entsprechenden nachgelagerten Dienste, Middleware oder das Netzwerk.

### 10.3 Laufzeit und Prozesse

| Überwachungsdimension | Metrik | Metrikbedeutung | Üblicher Standard/Einheit | Abnorme Erscheinung |
| --- | --- | --- | --- | --- |
| Go-Goroutine | `go_goroutines` | Anzahl der Goroutinen im Go-Prozess | Anzahl | Kontinuierliches Wachstum kann auf Blockierung oder Speicherlecks hinweisen |
| Go-GC-Dauer | `go_gc_duration_seconds` | Go-GC-Dauer | Sekunden/Perzentil | Erhöhte GC-Zeit kann die Latenz beeinflussen |
| Go-Heap-Speicher | `go_memstats_alloc_bytes`, `go_memstats_heap_inuse_bytes` | Go-Heap-Allokation und -Nutzung | Bytes | Kontinuierliches Wachstum erfordert Überprüfung auf Speicherlecks |
| Go-Systemspeicher | `go_memstats_sys_bytes` | Vom Go-Runtime vom System angeforderter Speicher | Bytes | Zusammen beobachten mit RSS |
| Go-Stack-Speicher | `go_memstats_stack_inuse_bytes` | Goroutine-Stack-Nutzung | Bytes | Erhöht sich mit dem Wachstum der Goroutine |
| Node.js GC-Anzahl | `nodejs_gc_duration_seconds_count` | Node.js GC-Anzahl | mal/Sekunde | Häufige GC kann auf Heap-Druck hinweisen |
| Node.js GC-Dauer | `nodejs_gc_duration_seconds_sum` | Node.js Gesamtdauer der GC | Sekunden/Sekunde | Zunahme der GC-Dauer kann die Antwortzeit beeinträchtigen |
| Node.js Heap-Speicher | `nodejs_heap_space_size_used_bytes` | Nutzung jedes Speichers Node.js Heap-Speicher | Bytes | Aufmerksamkeit erforderlich, wenn in der Nähe des Limits oder kontinuierlich wachsend |
| Prozess CPU | `process_cpu_seconds_total` | Prozess CPU Zeit | Kerne/Sek | Hoch CPU Nutzung |
| Prozess RSS | `process_resident_memory_bytes` | Physischer Speicher des Prozesses | Bytes | Kontinuierlich RSS Wachstum |
| Prozess-Handles | `process_open_fds` | Anzahl der offenen Dateideskriptoren im Prozess | Zählung | Handle-Lecks, Verbindungslecks |

Untersuchungsvorschlag: Runtime-Metriken von Go und Node.js werden hauptsächlich verwendet, um Anwendungs-Latenz und Ressourcensteigerung zu erklären. Wenn die Anwendung P95 steigt, prüfen Sie vorrangig Speicherzuweisung und Objektlebenszyklus, falls die GC-Dauer gleichzeitig steigt.

### 10.4 Kollaborativer Bearbeitungsdienst

| Überwachungsdimension | Metrik | Metrikbedeutung | Gängige Einheiten | Abnormale Anzeichen |
| --- | --- | --- | --- | --- |
| Kafka Consumer-Verzögerung | `kafka_consumergroup_lag` | Rückstand der zugehörigen Consumer-Gruppen in der kollaborativen Bearbeitung | Anzahl | Verzögerungszunahme kann Verzögerungen bei der Ereignisverarbeitung verursachen |
| Verarbeitungsdauer | `process_flow_duration_seconds_bucket` | Dauer des kollaborativen Bearbeitungsprozesses | P50/P95/P99 | Verlangsamung im Dokumentenzusammenarbeitsverlauf |
| Prozessanzahl | `process_total` | Gesamtzahl der bearbeiteten Prozesse | Mal/Sekunde | Abnormale Veränderungen im Verarbeitungsvolumen |
| Dateiinhaltsgröße | `file_content_size_bytes_bucket` | Verteilung der Dateiinhaltsgrößen | Statistiken nach Buckets | Zunahme des Anteils großer Dateien kann die Verarbeitungszeit beeinflussen |
| Changeset-Dauer | `handle_changeset_cost_seconds_bucket` | Zeit zur Verarbeitung eines Changesets | P50/P95/P99 | Zunahme der Verzögerung bei Bearbeitungssynchronisation |
| Modoc-Berechnungsanzahl | `modocComputeCount` | Anzahl der Modoc-Berechnungen | Mal/Sekunde | Abnormer Anstieg des Berechnungsvolumens |
| Serverless-Aufrufe | `serverless_invocations` | Anzahl der Serverless-Aufrufe | Mal/Sekunde | Aufrufausfälle oder Spitzen können die Verbindung beeinträchtigen |

Häufige Abfragen:

```promql
histogram_quantile(0.95, sum(rate(handle_changeset_cost_seconds_bucket[5m])) by (le))
```

Untersuchungsvorschläge: Für kollaborative Bearbeitungslinks, Kafka Lag, Prozessdauer, Changeset-Dauer und Dateigröße sollten zusammen untersucht werden. Wenn der Anteil großer Dateien steigt, könnte eine längere Dauer normale Kapazitätsbelastung statt eines Einzelfehlers darstellen.

### 10.5 RS-Dienst

| Überwachungsdimension | Metrik | Metrikbedeutung | Häufiger Umfang/Einheit | Abnormale Leistung |
| --- | --- | --- | --- | --- |
| HTTP Anfrageanzahl | `http_requests_total` | Kumulative Anzahl von HTTP Anfragen | Mal/Sekunde | Plötzlicher Anstieg oder Rückgang von Anfragen |
| HTTP Dauer | `http_requests_duration_seconds_bucket`, `http_requests_duration_seconds_sum`, `http_requests_duration_seconds_count` | HTTP Anfrage-Dauer | P50/P95/P99 | Erhöhte Schnittstellenlatenz |
| gRPC Anfrageanzahl | `grpc_requests_total` | Kumulative Anzahl von gRPC Anfragen | Mal/Sekunde | gRPC Aufruf-Ausnahmen |
| gRPC Dauer | `grpc_requests_duration_seconds` | gRPC Anfrage-Dauer | P50/P95/P99 | Langsamere nachgelagerte oder interne Verarbeitung |
| Dauer der Exportaufgabe | `export_task_duration_milliseconds_count` | Anzahl und Dauer der Exportaufgabenverarbeitung | ms/Zeit | Exportaufgaben verlangsamt oder aufgestaut |
| Dauer der Importaufgabe | `import_task_duration_milliseconds_count` | Anzahl der Importaufgabenprozesse und Dauer | ms / pro Aufgabe | Verlangsamte oder gestapelte Importaufgaben |
| Laufende Exportaufgaben | `export_task_in_progress` | Derzeit ausgeführte Exportaufgaben | Zählung | Wenn sie lange nicht abnimmt, zeigt dies, dass Aufgaben hängen |
| Laufende Importaufgaben | `import_task_in_progress` | Derzeit ausgeführte Importaufgaben | Zählung | Wenn sie lange nicht abnimmt, zeigt dies, dass Aufgaben hängen |
| Tokio-Metriken | `tokio_metrics` | Rust-Tokio-Runtime-Metriken | aktueller Wert / Rate | Abnorme Runtime-Warteschlange oder Aufgabenplanung |
| jemalloc-Metriken | `jemalloc` | Speicherallokator-Metriken | Bytes / Anzahl | Speicherfragmentierung oder Allokationsanomalie |
| TCP Metriken | `tcp` | RS-Dienst TCP verbindungsbezogene Metriken | Anzahl / Rate | Verbindungsdruck oder Netzwerk-Anomalie |

Untersuchungsvorschlag: Der RS-Dienst sollte sowohl Online-Anfragen als auch lang laufende Aufgaben wie Import/Export untersuchen. Eine kontinuierlich nicht abnehmende Anzahl von laufenden Aufgaben weist in der Regel zuverlässiger auf ‚hängende Aufgaben‘ hin als die durchschnittliche Dauer.

## 11. Metriken lesen und Untersuchungsvorschläge

### 11.1 Allgemeine Untersuchungsreihenfolge

| Schritt | Beobachtungspunkt | Zweck |
| --- | --- | --- |
| 1 | `up`, Startzeit, Pod bereit | Bestätigen, ob der Dienst noch aktiv ist und ob er kürzlich neu gestartet wurde |
| 2 | Anfragevolumen, Fehlerrate, P95/P99 Latenz | Feststellen, ob es tatsächlich das Geschäft beeinträchtigt |
| 3 | CPU, Speicher, Festplatte, Netzwerk | Feststellen, ob eine Ressourcenengpass besteht |
| 4 | Abhängigkeitslatenz nachgelagerter Dienste, Kafka Verzögerung, langsame Datenbankabfragen | Feststellen, ob es durch Abhängigkeiten verlangsamt wird |
| 5 | Release-Version, Konfiguration, Verkehrsänderungen | Feststellen, ob es im Zusammenhang mit Änderungen steht |

Beim eigentlichen Troubleshooting sollte man nicht sofort alle Diagramme ansehen. Zuerst bestätigen, ‚ob ein Geschäftseinfluss besteht‘, dann herausfinden, ‚woher der Einfluss stammt‘. Zum Beispiel, wenn eine Schnittstelle langsam ist, zuerst die Anwendung anschauen P95, dann die Latenz der Client-Abhängigkeit prüfen; wenn die Abhängigkeit normal ist, auf den Dienst zurückblicken CPU, GC, Speicher und Container-Drosselung.

### 11.2 Häufige Ausnahme-Kombinationen

| Symptom | Übliche Metrikleistung | Prioritäre Untersuchungsrichtung |
| --- | --- | --- |
| Schnittstelle langsam | Anwendung P95/P99 steigend, CPU nicht hoch | Nachgelagerte Abhängigkeiten, langsame Datenbankabfragen, Kafka Verzögerung |
| CPU voll genutzt | `container_cpu_usage_seconds_total` hoch, Drosselung hoch | CPU Grenzen, heiße Schnittstellen, Batch-Verarbeitungsaufgaben |
| Speicher OOM | Arbeitsmenge nahe am Limit, Neustartanzahl steigt | Speicherlecks, Limit zu klein, Verarbeitung großer Objekte |
| Langsame Festplatte | iowait, IO beschäftigt, Lese-/Schreiblatenz steigt überall | Datenbank, Kafka, MinIO, Protokoll schreiben |
| Netzwerk abnormal | Verkehrsanstieg begleitet von Ausfällen/Fehlern | Node NIC, CNI, Link, Anzahl der Verbindungen |
| Kafka Latenz | `kafka_consumergroup_lag` kontinuierlich steigend | Consumer-Instanzen, Verbrauchszeit, nachgelagerte Abhängigkeiten |
| Redis Rückstau | Trefferquote sinkt, Fehler steigen | Schlüsselauslaufpolitik, Cache-Durchdringung, Kapazität |
| MySQL Langsam | langsame Abfragen, Scan, Sperrwarten steigen | SQL, Indizes, Sperren, Festplatten-IO |
| MinIO Risiko | Offline-Festplatte, Fehlerquote, Kapazitätsstufen steigen | Festplatte, Knoten, Bucket-Wachstum, Heilungsstatus |
| Elasticsearch Langsame Abfrage | Suchabfrage-/Abrufzeit steigt, Thread-Pool-Ablehnungen steigen | Abfragebedingungen, Indexstruktur, JVM, Festplatten-IO |
| Elasticsearch Langsames Schreiben | Indizierungszeit, Zusammenführungszeit, Speicher-Drosselung steigt | Schreibspitzen, Aktualisierung, Zusammenführung, Festplattenlevels |
