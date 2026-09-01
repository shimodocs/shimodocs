# Hohe Verfügbarkeit Kubernetes Bereitstellung

[← ShimoDocs Suite Bereitstellungsdokumentation](../README.md)

## 1. Anwendbare Szenarien 

> [!TIP] 
> 
> K8s Cluster-Bereitstellung ist für Produktionsumgebungen geeignet. Im Vergleich zur Einzelmaschinenbereitstellung ist die Cluster-Bereitstellung besser für Langzeitbetrieb, Skalierung und Hochverfügbarkeits-Szenarien geeignet. 

- Für Produktionsumgebungen wird empfohlen zu verwenden `3 master   N worker`. 
- Mindestens 3 Server vorbereiten, alle 3 als Master. Arbeiter können zunächst Master-Knoten wiederverwenden und später je nach Umfang die Arbeiter erhöhen. 

## 2. Vorbereitungen vor der Bereitstellung 

### 2.1 Bereiten Sie die folgenden Informationen vor 

| Informationen | Beispiel | Beschreibung | 
| --- | --- | --- | 
| Netzwerkumgebung | Online / Offline | Online wählen, wenn der öffentliche Netzwerkzugang unterstützt wird; Offline für interne Netzwerke oder getrennte Umgebungen wählen | 
| INSTALL_NODE_IP | `<INSTALL_NODE_IP>` | Wählen Sie 1 Maschine als Installationsknoten, um die Webseite zu starten | 
| Geschäft NODE_IP | `<Node1IP>`, `<Node2IP>`, `<Node3IP>` | Mindestens 3 Server | 
| Benutzer für die Ausführung | `root` | Installationsbefehle sollten ausgeführt werden mit `root` | 
| Zugriffsprotokoll | HTTP / HTTPS | HTTPS Für Produktionsumgebungen wird empfohlen | 
| ACCESS_DOMAIN | `<ACCESS_DOMAIN>` | Die Adresse für den Benutzerzugriff ShimoDocs Suite |
| Datenverzeichnis | `/data` | Es wird empfohlen, es auf allen Knoten konsistent zu halten |
| Installationswerkzeug | `mdp-installer-${Arch}` | Installer bereitgestellt von ShimoDocs, `${Arch}` unterscheidet verschiedene Chip-Architekturen, sein Wert kann amd64 für x86-Architektur oder arm64 für ARM-Architektur sein |
| Produkt-Installationspaket | ShimoDocs Suite Installationspaket | Verwenden Sie den tatsächlich gelieferten Dateinamen |
| Offline-Image-Paket | `*.tar.gz` | Nur für die Offline-Installation erforderlich |
| Externe Middleware | Ja / Nein | Wenn externe Middleware vorhanden ist, bereiten Sie Adresse, Port, Konto vor, PASSWORD im Voraus |

### 2.2 Mindestanforderungen an den Server

| Artikel | Anforderung |
| --- | --- |
| Anzahl der Server | 3 oder mehr |
| Empfohlene Rollen | `3 master   N worker` |
| CPU pro Knoten | 16 Kerne oder mehr |
| Speicher pro Knoten | 32 GB oder mehr |
| Systemfestplatte | Root `/` Partition 100 GB oder mehr |
| Datenfestplatte | Separat einhängen `/data`, verfügbarer Speicherplatz 300 GB oder mehr |
| Offline-Installation | Es wird empfohlen, auf der Datenfestplatte des Installationsknotens zusätzlich 100 GB oder mehr vorzuhalten |

Hinweis:

- Nicht partitionieren `/root`, `/var`, oder `/tmp` getrennt. 
- Legen Sie keine Daten auf der Systemfestplatte ab; legen Sie alles in `/data`. 
- Die Zeit auf allen Knoten muss synchronisiert sein. 
- Installationsknoten müssen auf andere Knoten zugreifen können via SSH. 

Kann auf jedem Server ausgeführt werden: 

```bash
lscpu
free -g
df -h
timedatectl status
```

Bestätigen Sie, dass von dem Installationsknoten aus auf andere Knoten zugegriffen werden kann: 

```bash
ssh root@<NODE2IP>
ssh root@<NODE3IP>
```

Wenn die Anmeldung fehlschlägt, überprüfen Sie zuerst SSH, PASSWORD, Firewall- oder Sicherheitseinstellungen, bevor Sie mit der Installation fortfahren.

## 3. Laden Sie das Installationswerkzeug und das Installationspaket hoch
> [!TIP]
>
> - Stellen Sie sicher, dass Sie die Dateinamen in den Befehlen entsprechend der tatsächlichen Situation ändern. Zum Beispiel lautet der Name des Installationspakets in einer x86-Architekturumgebung mdp-installer-amd64.
> - Wählen Sie die geeignete Upload-Methode basierend auf der tatsächlichen Situation. Dieser Artikel verwendet die scp-Befehlszeile als Beispiel, aber auch andere grafische SSH Werkzeuge können zum Hochladen verwendet werden.

Führen Sie den folgenden Befehl auf Ihrem lokalen Computer aus, um den Installer auf den Installationsknoten zu übertragen:

```bash
scp mdp-installer-amd64 root@<INSTALL_NODE_IP>:/root/
```

Die Offline-Installation erfordert weiterhin das Hochladen des Offline-Image-Pakets: 

```bash
scp smbase_image-amd64.tar.gz offline_app_image.tar.gz root@<INSTALL_NODE_IP>:/root/
```

Melden Sie sich am Installationsknoten an: 

```bash
ssh root@<INSTALL_NODE_IP>
```

Geben Sie dem Installer Ausführungsrechte:

```bash
chmod +x /root/mdp-installer-amd64
```

Starten Sie die Installer-Webseite: 

```bash
nohup /root/mdp-installer-amd64 server --port 18080 &
```

Zugriff über Browser: 

```text
http://<INSTALL_NODE_IP>:18080
```

## 4. Installation über die Webseite

### 4.1 Hochladen des Produktinstallationspakets

1. Öffnen Sie `http://<INSTALL_NODE_IP>:18080`.
2. Laden Sie das ShimoDocs Suite Installationspaket hoch.
3. Nach Abschluss des Uploads klicken Sie `Continue`.

### 4.2 Konfigurieren ACCESS_DOMAIN

Geben Sie die ShimoDocs Suite Zugriffsadresse ein:

| Konfigurationselement | Wie ausfüllen |
| --- | --- |
| ACCESS_DOMAIN / IP | `<ACCESS_DOMAIN>` |

### 4.3 Bestätigen der Grundkonfiguration

| Konfigurationselement | Wie ausfüllen |
| --- | --- |
| NODE_IP | Füllen Sie Master-/Worker- NODE_IP nacheinander aus |
| SSH Port | Üblicherweise `22` |
| SSH PASSWORD | `root` Benutzer PASSWORD |
| Knotentyp | `master`, `worker`, Installationsknoten |
| Datenverzeichnis | `/data` |

Ausführungsschritte:

1. Hinzufügen INSTALL_NODE_IP.
2. Fügen Sie die IP-Adressen jedes Master-/Worker-Knotens hinzu.
3. Weisen Sie jeder Serverrolle eine Knotenzuweisung zu.
4. Testen Sie die Konnektivität vom Installationsknoten zu jedem Knoten.
5. Füllen Sie das Datenverzeichnis und das Container-Netzwerksegment aus.

Zu bestätigende Schlüsselpunkte während der Konfiguration:

- Das Zugriffsprotokoll und ACCESS_DOMAIN sind korrekt ausgefüllt.
- Pod CIDR und Dienst CIDR dürfen nicht mit dem bestehenden Netzwerk, Büronetzwerk, VPN, oder IDC Netzwerksegmenten in Konflikt stehen.
- Das Datenverzeichnis verwendet `/data` oder das Verzeichnis der tatsächlich geplanten Datenfestplatte.
- Die Online-/Offline-Installationsmethode ist mit der aktuellen Netzwerkumgebung konsistent.
- Die Offline-Installation erfordert das Hochladen des Offline-Basis-Image-Pakets und des Anwendungs-Image-Pakets. Standardmäßig handelt es sich um eine Online-Installation, und es muss sichergestellt werden, dass der Cluster Zugang zum öffentlichen Netzwerk hat.

### 4.4 Erste Bereitstellung

Nach Abschluss der Konfiguration klicken Sie auf Bereitstellung initialisieren. Die Seite zeigt eine Übersicht dieser Bereitstellung an; bitte achten Sie besonders auf:

- Produktpaketversion.
- Bereitstellen NODE_IP.
- SSH Benutzer und Port.
- ACCESS_DOMAIN und Protokoll.
- Datenverzeichnis.
- Online- oder Offline-Installationsmodus.
- Middleware-Auswahl.

Fortfahren, nachdem bestätigt wurde, dass keine Fehler vorliegen.

### 4.5 Systemumgebung überprüfen

Der Installer überprüft automatisch die Serverumgebung.

Nach erfolgreicher Prüfung mit der Bereitstellung fortfahren. Bei Fehlern folgen Sie bitte den Anweisungen auf der Seite, um diese zu beheben, und prüfen Sie erneut. Gängige Handlungsrichtlinien umfassen:

- Unzureichender Speicherplatz: Speicherplatz bereinigen oder die Datenfestplatte erweitern.
- Port nicht verfügbar: Port freigeben oder Portnutzung anpassen.
- SSH Verbindung fehlgeschlagen: Überprüfen Sie das Konto, PASSWORD, privaten Schlüssel, Port und Sicherheitsgruppe.
- Zeit-Synchronisationsabweichung: Konfigurieren NTP oder kalibrieren Sie die Serverzeit.
- Fehlende Basisbefehle: Installieren Sie fehlende Befehle gemäß der Systemdistribution.

### 4.6 Bereitstellung beginnen

Nachdem die Umgebungsüberprüfung bestanden wurde, klicken Sie auf Bereitstellung starten.

Sie können die Ausführungsprotokolle jeder Komponente während des Bereitstellungsprozesses einsehen. Bitte stellen Sie während der Installation sicher: 

- Der Installationsprozess bleibt aktiv. 
- Der Browser kann über das Netzwerk mit dem Installationsknoten kommunizieren. 
- Der Server wird nicht neu gestartet. 
- Verschieben oder löschen Sie nicht das Installationspaket, das Offline-Image-Paket oder das Datenverzeichnis. 

### 4.7 Warten Sie, bis die Installation abgeschlossen ist

Der Installationsprozess erfordert etwas Wartezeit, und die genaue Dauer hängt von der Serverleistung, der Netzwerkumgebung und der Bilddownloadgeschwindigkeit ab.

Wenn die Seite zeigt, dass alle Aufgaben erfolgreich ausgeführt wurden und keine Komponenten fehlgeschlagen sind, bedeutet dies, dass die Bereitstellung abgeschlossen ist.

### 4.8 Installationsergebnis bestätigen

Nach Abschluss der Installation zeigt der Installer die Seite zum Abschluss der Bereitstellung und die Zugangsinformationen an. Bitte bestätigen Sie zuerst, dass auf der Seite keine fehlgeschlagenen Aufgaben vorhanden sind, bevor Sie auf das Geschäftssystem und MDP die Operations-Plattform zugreifen.

Besuchen Sie die Geschäftsadresse: 

```text
http://<ACCESS_DOMAIN>/
```

Wenn HTTPS während der Installation konfiguriert wurde, besuchen Sie bitte: 

```text
https://<ACCESS_DOMAIN>/
```

Nach der Anmeldung mit dem Standardkonto oder Administratorkonto ändern Sie bitte sofort PASSWORD das Initialpasswort.

Zugriff auf MDP Operations-Plattform:

```text
http://<ACCESS_DOMAIN>/mdp/
```

Wenn Sie den MDP Administrator PASSWORDändern müssen, können Sie den folgenden Befehl auf dem Bereitstellungsknoten ausführen, um den PASSWORD.
zu ändern oder zurückzusetzen. PASSWORD Bitte ersetzen Sie {password} durch ein neues, komplexes starkes

```bash
kubectl exec -it $(kubectl get pods -l app=mdp -o jsonpath='{.items[0].metadata.name}') -- reset-admin-password {password}
```

## entsprechend den tatsächlichen Sicherheitsanforderungen.

### 5. Nach der Installation Abnahme K8s 5.1 Überprüfung

Knotenstatus

```bash
kubectl get node
```

Führen Sie auf dem Bereitstellungsknoten aus: `Ready`. 

Der Knotenstatus sollte 

```bash
kubectl get pod -A
```

Den Dienst weiterhin überprüfen: 

- `Running`Die normalen Zustände sind normalerweise: 
- `Completed`: Der Dienst läuft. 

: Die Aufgabe wurde ausgeführt. `CrashLoopBackOff`, `ImagePullBackOff`, `Error`, `Pending`Wenn Sie Zustände wie 

### begegnen, überprüfen Sie bitte zunächst die entsprechenden Pod-Protokolle und bearbeiten Sie sie. 

Zugriff auf ShimoDocs Suite 5.2 Überprüfung des Zugangs 

```text
http://<ACCESS_DOMAIN>/
```

Wenn HTTPS Zugang über den Browser: 

```text
https://<ACCESS_DOMAIN>/
```

ist konfiguriert, bitte besuchen Sie: 

### Bestätigen Sie, dass die Anmeldeseite normal geöffnet werden kann. 

5.3 Überprüfung des Verwaltungs-Backends und der Lizenz 

- Bestätigen Sie die folgenden Punkte: 
- Das Verwaltungs-Backend ist zugänglich. 
- Administratoren können sich anmelden. 
- Die Lizenzseite kann geöffnet werden. 
- Maschineninformationen können eingesehen werden. 

### Die Lizenz kann gemäß dem Autorisierungsprozess beantragt oder aktualisiert werden. 

5.4 Überprüfung der Geschäfts-Funktionen 

- Nach dem Einloggen mit einem Testkonto oder einem vom Administrator erstellten Konto mindestens überprüfen: 
- Dokumente, Tabellen und Präsentationen können erstellt werden. 
- Dokumente können bearbeitet, gespeichert oder aktualisiert werden, und der Inhalt bleibt erhalten. 
- Mehrbenutzer-Kollaborationsbearbeitung ist verfügbar. 
- Dateiimport und -export funktionieren normal. 

Kernfunktionen wie Suche, Teamspace, Kontakte usw. sind verfügbar. PASSWORD Nach dem ersten Einloggen des Standard-Testkontos bitte sofort Ihr 
Konto aktualisieren. PASSWORD ist das Bereitstellungs- und Lieferkonto PASSWORD! 

```text
ACCOUNT:autotest@example.com
PASSWORD:xxxxxxx
```

### 5.5 Installateurprozess stoppen

Nachdem die Bereitstellung abgeschlossen und akzeptiert wurde, kann der Installateur-Webdienst gestoppt werden
Stoppen Sie die Installationswebseite:
Befehl zum Stoppen des Installateurs: 

```bash
ps -ef | grep mdp-installer | grep -v grep
kill <PID>
```

Wenn der Installateur im Hintergrund gestartet wurde mit `nohup`, können Sie auch die Protokolle überprüfen: 

```bash
tail -f /root/nohup.out
```

## 6. Umgang mit häufigen Problemen

### 6.1 Browser kann Installateurseite nicht öffnen

Überprüfen Sie Folgendes:

- Ob der Installateurprozess noch läuft.
- Ob Port `18080` durch eine Firewall oder Sicherheitsgruppe blockiert ist.
- Ob die vom Browser aufgerufene IP INSTALL_NODE_IP.

Sie können auf dem Server ausführen:

```bash
ps -ef | grep mdp-installer | grep -v grep
ss -lntp | grep 18080
```

### 6.2 Umgebungsprüfung fehlgeschlagen

Behandeln Sie jeden Punkt gemäß den Aufforderungen auf der Seite. Nach der Bearbeitung kehren Sie zur Installateurseite zurück und führen die Umgebungsprüfung erneut aus.

Prioritätsprüfungen:

- Ob die CPU, der Speicher und die Festplatte die Anforderungen erfüllen.
- Ob `/data` eine eigenständige Datenfestplatte ist.
- Ob die Serverzeit synchronisiert ist.
- Ob die SSH Der Benutzer hat Bereitstellungsberechtigungen.

### 6.3 Offline-Installationsimage-Pull fehlgeschlagen

Überprüfen Sie folgende Richtungen:

- Ob das Offline-Image-Paket auf den Bereitstellungsknoten hochgeladen wurde.
- Ob das Basis-Offline-Image-Paket und das Produkt-Offline-Image-Paket vollständig sind.
- Ob die Version des Image-Pakets dem Produktinstallationspaket entspricht.
- Ob die Adresse des privaten Image-Repositorys, das Konto und PASSWORD sind korrekt ausgefüllt.

### 6.4 Pod bleibt lange in abnormalem Zustand

Überprüfen Sie zunächst den abnormen Pod:

```bash
kubectl get pod -A
```

Überprüfen Sie erneut die Protokolle: 

```bash
kubectl logs -n <namespace> <pod-name>
```

Behandeln Sie Probleme mit Images, Konfigurationen, Ressourcen oder Abhängigkeiten basierend auf den Protokollen.

## 7. Materialien nach der Installation aufbewahren

Nach der Bereitstellung wird empfohlen, die folgenden Materialien aufzubewahren, um nachfolgende Wartung, Upgrades und Fehlerbehebungen zu erleichtern:

- INSTALL_NODE_IP, ACCESS_DOMAINund Zugriffsprotokoll.
- Installationsdateiname und Version.
- Dateiname und Version des Produktinstallationspakets.
- Dateiname und Version des Offline-Image-Pakets.
- Screenshots der Schlüsselk Konfiguration der Webseite.
- `kubectl get node` Prüfergebnisse.
- `kubectl get pod -A` Prüfergebnisse.
- Lizenzautorisierungsaufzeichnungen.
- Aufzeichnungen zur Abnahme von Geschäftsprozessen.
- Während der Bereitstellung aufgetretene Probleme und deren Behandlungsergebnisse.
