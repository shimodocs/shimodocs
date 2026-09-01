# Systemanforderungen

[← ShimoDocs Suite Bereitstellungsdokumentation](README.md)

## 1. Ressourcen gemäß Szenario vorbereiten

| Einsatzszenario | Empfohlene Bereitstellung | Ressourcenvorbereitung |
| --- | --- | --- |
| Leichtgewichtiges kleines Team, PoC, Demonstration, Funktionsprüfung | Einzelserver-Bereitstellung | 1 Server |
| Offizieller Start, langfristiger Betrieb, erforderlich hohe Verfügbarkeit oder zukünftige Skalierung | Hochverfügbarkeits-Cluster | 3 oder mehr Server |

- Einzelserver-Bereitstellung eignet sich für schnelle Überprüfung und kleinmaßstäbliche Nutzung.
- Cluster-Bereitstellung ist geeignet für den offiziellen Start, den langfristigen Betrieb und zukünftige Skalierungen.

## 2. Anforderungen an das Betriebssystem

| Betriebssystem | Unterstützte Versionen | Unterstützte Architektur |
| --- | --- | --- |
| Ubuntu | 22.04, 24.04 | x86 |

Auf jedem Server ausführen:

```bash
cat /etc/os-release
uname -m
```

Bestätigungsergebnisse: 

- Das Betriebssystem ist Ubuntu 22.04 oder Ubuntu 24.04. 
- CPU Die Architektur ist x86. 
- Installationkonto ist `root`, oder verfügt über gleichwertige Systemverwaltungsrechte. 

Hinweis: Gründe für die Nichtunterstützung von CentOS-Systemen 
- CentOS Linux 7 und 8 haben das Ende ihres Lebenszyklus erreicht, CentOS stellt offiziell CentOS 9 und nachfolgende Versionen nicht mehr bereit und erhält keine neuen Sicherheitsupdates, Fehlerbehebungen oder Patches mehr. 
- Grundlegende Systemkomponenten können langfristig keine Sicherheitspatches erhalten, wodurch potenziell nicht behebbare Schwachstellen entstehen, die die Sicherheitsanforderungen einer Produktionsumgebung nicht erfüllen. 
- Kernel, glibc, OpenSSL und andere grundlegende Komponenten in CentOS 7/8 sind relativ alt und können die Anforderungen neuer Kubernetes Laufzeit- und Abhängigkeitsbibliotheken nicht erfüllen. 
- CentOS Stream hat eine andere Versionspositionierung und einen anderen Aktualisierungsmechanismus im Vergleich zu traditionellem CentOS Linux, und CentOS Stream-Umgebungen, die keine spezielle Kompatibilitätsüberprüfung durchlaufen haben, sind ebenfalls nicht offiziell unterstützt. 


## 3. Serverkonfigurationsanforderungen 

### 3.1 Einzelnoden-Bereitstellung 

- Geeignet für leichte kleine Teams mit weniger als 200 Personen. 
- PoC, Demonstrations- und Funktionsprüfungsszenarien können entsprechend den Ressourcen eines Einzelnodens vorbereitet werden. 

| Projekt | Anforderung | 
| --- | --- | 
| Anzahl der Server | 1 | 
| CPU | 16 Kerne oder mehr |
| Speicher | 32 GB oder mehr |
| Systemfestplatte | Root `/` Partition 100 GB oder mehr |
| Datenfestplatte | Eigenständig montiert `/data`, verfügbarer Speicher 300 GB oder mehr, unterstützt Erweiterung |

### 3.2 Cluster-Bereitstellung

Für Szenarien, die einen offiziellen Start, einen langfristigen Betrieb, hohe Verfügbarkeit oder zukünftige Erweiterungen erfordern, bereiten Sie Ressourcen gemäß den Cluster-Anforderungen vor.

| Artikel | Anforderung |
| --- | --- |
| Anzahl der Server | 3 oder mehr |
| Empfohlene Rollen | `3 master   N worker` |
| CPU pro Knoten | 16 Kerne oder mehr |
| Speicher pro Knoten | 32 GB oder mehr |
| Systemfestplatte pro Knoten | Root `/` Partition 100 GB oder mehr |
| Datenfestplatte pro Knoten | Eigenständig montiert `/data`, verfügbarer Speicher 300 GB oder mehr, unterstützt Erweiterung |

Partition-Hinweise:

- Halten Sie mindestens 100 GB für die Root- `/` Partition bereit.
- Es wird empfohlen, `/root`, `/var`, `/tmp` unter der Root-Partition für eine einheitliche Verwaltung zu platzieren.
- Verwenden Sie eine unabhängige Datenfestplatte für das Datenverzeichnis, eingebunden unter `/data`.

## 4. Server-Selbstprüfungsbefehle

Auf jedem Server ausführen: 

```bash
# ============================================
# 1. View CPU architecture and core information
#    - Architecture type (x86_64/aarch64)
# ============================================
lscpu

# ============================================
# 2. Display memory and swap usage in GiB
# ============================================
free -g

# ============================================
# 3. File System Disk Space Usage
# ============================================
df -h

# ============================================
# 4. Find the executable file path
# ============================================
which iptables gzip tar

# ============================================
# 5. Display system time, time zone, and NTP synchronization status
#    Distributed clusters must have strict time synchronization, otherwise it will affect authentication and log sequencing.
# ============================================
timedatectl status
```

Vergleichscheckliste:

| Prüfpunkt | Bedingung für Bestehen |
| --- | --- |
| CPU | 16 Kerne oder mehr |
| Speicher | 32 GB oder mehr |
| Systemfestplatte | Root `/` Verfügbarer Speicherplatz der Partition 100 GB oder mehr |
| Datenfestplatte | `/data` Eingehängt, verfügbarer Speicherplatz 300 GB oder mehr |
| Grundlegende Befehle | `iptables`, `gzip`, `tar` kann gefunden werden |
| Zeitsynchronisation | Systemzeitsynchronisation ist normal |

## 5. Browseranforderungen

| Browser | Versionsanforderung |
| --- | --- |
| Chrome | 86 oder höher |
| Safari | 11 oder höher |
| Firefox | 102 oder höher |
| Edge | 84 oder höher |

Es wird empfohlen, vorzugsweise den neueren Chrome oder Edge zu verwenden, um auf den Installer zuzugreifen und ShimoDocs Suite.

## 6. Middleware-Anforderungen

| Komponente | Versionsanforderung |
| --- | --- |
| Elasticsearch | 8.18.x |
| MongoDB | 4.4.x |
| Redis | 6.2.x |
| MySQL | 8.0 |
| Dameng | V8 03134284194-20240920-243574-20108 |
| Kafka | 2.7 bis 3.5 |
| Objektspeicher | Kompatibel mit S3 Protokoll<br>und sicherstellen, dass seine Endpunktadresse direkt von Client-Browsern aus dem öffentlichen Netzwerk zugänglich ist (da ShimoDocs die statischen Ressourcenladen und Dokumenten-Lese-/Schreiboperationen der Anwendung über direkte Browserverbindungen zum Objektspeicher erfolgen müssen). |

Als Objektspeicher kann Huawei Cloud OBS, Alibaba Cloud OSS, Tencent Cloud COS, AWS S3gewählt werden. Für die lokale Bereitstellung, Verwendung MinIO kann in Betracht gezogen werden.

Wenn die in den Installer integrierte Middleware verwendet wird, fahren Sie auf der Installationsseite mit den Standardoptionen fort. 
Wenn bestehende externe Middleware verwendet wird, bereiten Sie die Adresse, den Port, das Konto, PASSWORD, DATABASE_NAME oder den Bucket-Namen vor der Installation vor.

## 7. Portanforderungen

Stellen Sie vor der Bereitstellung sicher, dass der Server, die Sicherheitsgruppe, die Firewall, der Load Balancer und die Netzwerkrichtlinien die folgenden Ports erlauben.

| Port | Ziel | Zweck |
| --- | --- | --- |
| `18080/TCP` | Installer-Web-UI | Zugriff auf die Installationsseite |
| `80/TCP` oder `443/TCP` | ShimoDocsSERVICE_DOMAIN | Benutzerzugangseintrag |
| `22/TCP` | Alle Bereitstellungsknoten | SSH Login- und Installationaufgabenverteilung |
| `3306/TCP` | MySQL | Datenbankverbindung |
| `6379/TCP` | Redis | Cache-Verbindung |
| `27017/TCP` | MongoDB | Dokumentendatenbankverbindung |
| `9092/TCP` | Kafka | Nachrichtenwarteschlangenverbindung |
| `9200/TCP` | Elasticsearch | Suchdienstverbindung |
| Nach Service-Port | S3 / OBS / OSS / COS / MinIO | Objektspeicherverbindung |

## 8. Anforderungen an die Festplatten-E/A

Es wird empfohlen, SSDs für Datenfestplatten zu verwenden. Die Festplattenleistung sollte die folgenden Standards erfüllen:

| Artikel | Anforderung |
| --- | --- |
| Gemischtes Lesen/Schreiben IOPS | Über 5000 |
| Sequentieller Lese-/Schreibdurchsatz | Über 150 MB/s |
| Durchschnittliche Latenz | Etwa 5 ms oder geringer |

Nach der Installation `fio`können Tests durchgeführt werden auf `/data`.

### 8.1 Test für gemischtes Lesen/Schreiben

```bash
fio --name=randrw-test \
  --filename=/data/testfile \
  --size=20G \
  --rw=randrw \
  --rwmixread=70 \
  --bs=4k \
  --ioengine=libaio \
  --direct=1 \
  --iodepth=32 \
  --numjobs=4 \
  --runtime=300 \
  --time_based \
  --group_reporting
```

Achten Sie auf das IOPS in den Ergebnissen; gemischtes Lesen/Schreiben IOPS sollte über 5000 liegen, um fortzufahren. 

### 8.2 Sequentieller Lesetest

```bash
fio --name=seqread-test \
  --filename=/data/testfile \
  --size=20G \
  --rw=read \
  --bs=1M \
  --ioengine=libaio \
  --direct=1 \
  --iodepth=32 \
  --numjobs=1 \
  --runtime=300 \
  --time_based \
  --group_reporting
```

### 8.3 Sequentieller Schreibtest

```bash
fio --name=seqwrite-test \
  --filename=/data/testfile \
  --size=20G \
  --rw=write \
  --bs=1M \
  --ioengine=libaio \
  --direct=1 \
  --iodepth=32 \
  --numjobs=1 \
  --runtime=300 \
  --time_based \
  --group_reporting
```

Sequentieller Lese- und Schreibdurchsatz über 150 MB/s kann fortgesetzt werden. 

Testdateien können nach dem Testen gelöscht werden: 

```bash
rm -f /data/testfile
```

## 9. Anforderungen an die Bandbreite des öffentlichen Netzwerks

Schätzen Sie die Bandbreite für öffentliche Netzwerkszenarien basierend auf der Anzahl der Benutzer:

```text
PUBLIC_NETWORK_BANDWIDTH = NUMBER_OF_USERS x 0.25 Mbps
```

Beispiel:

| Anzahl der Benutzer | Empfohlene öffentliche Netzwerkkapazität |
| --- | --- |
| 100 Benutzer | Über 25 Mbit/s |
| 200 Benutzer | Über 50 Mbit/s |
| 500 Benutzer | Über 125 Mbit/s |

Für Intranet-Zugriffsszenarien wird außerdem empfohlen, die ausgehende, eingehende und Lastenausgleichs-Bandbreite nach denselben Kriterien zu bewerten.

## 10. Empfehlungen zur Browser-Version für Installer und Betriebsplattform

Es wird empfohlen, Google Chrome ab Version 111 oder höher zu verwenden, idealerweise die neueste stabile Version.

## 11. Checkliste vor der Bereitstellung

Vor Beginn der Installation bestätigen Sie jeden Punkt:

- Die Betriebssystemversion erfüllt die Anforderungen.
- CPUSpeicher, Systemfestplatte und Datenfestplatte erfüllen die Anforderungen.
- `/data` ist auf einer separaten Datenfestplatte installiert.
- `iptables`, `gzip`und `tar` sind installiert.
- Die Systemzeitsynchronisation ist normal.
- Die Methode der Online- oder Offline-Installation wurde bestimmt.
- Installationsport `18080` ist zugänglich.
- Geschäftszugriffsports `80` oder `443` sind offen.
- Wenn externe Middleware verwendet wird, sind die Verbindungsinformationen vollständig vorbereitet.
- Objektspeicherung ist mit dem S3 Protokoll kompatibel, und Bucket- und Kontoberechtigungen sind bereit. 
- Datenplatten-IO-Tests erfüllen die Anforderungen. 
- Die Bandbreite des öffentlichen oder internen Netzwerks entspricht der erwarteten Anzahl von Benutzern.
