# Einzelknoten Kubernetes Bereitstellung

[← ShimoDocs Suite Bereitstellungsdokumentation](../README.md)

## 1. Anwendbare Szenarien
- **K8s Ein-Knoten-Bereitstellung**:
    - Geeignet für leichte, kleine Teams, den Einsatz von weniger als 200 Personen, PoC, Demonstrationen, Funktionsüberprüfung und Kurzzeittests.
- Es wird nur 1 Server benötigt, der gleichzeitig als Installationsknoten, der K8s Master-Knoten und der Business-Worker-Knoten dient.
- **Hinweis**
    - Für offiziellen Start, langfristigen Betrieb oder anschließende Hochverfügbarkeits-Skalierung wird empfohlen, eine K8s Cluster-Bereitstellung zu verwenden.

## 2. Überblick über den Bereitstellungsprozess

| Schritt | Vorgehensweise | Abschlussindikator |
| --- | --- | --- |
| 1. Systemumgebung prüfen | Serverressourcen, Festplatte, Netzwerk, Zeitsynchronisation und Grundbefehle bestätigen | Server erfüllt die Bereitstellungsanforderungen |
| 2. Installationsmaterialien vorbereiten | Installationsprogramm und Produktinstallationspaket beschaffen; eine Offline-Umgebung erfordert auch die Vorbereitung eines Offline-Image-Pakets | Dateiname entspricht der CPU Architektur |
| 3. Installationsmaterial hochladen | Installationsprogramm und Installationspaket auf den Bereitstellungsknoten hochladen | Dateien wurden im angegebenen Verzeichnis auf dem Server abgelegt |
| 4. Installationsprogramm starten | Starten Sie die `mdp-installer` Webseite | Die Installationsseite ist über den Browser zugänglich |
| 5. Installation über die Webseite | Verteilungspaket auswählen, Knoten konfigurieren, Umgebungsprüfung abschließen und Bereitstellung starten | Alle Installationaufgaben erfolgreich |
| 6. Abnahme nach der Installation | Cluster, Dienste, Anmeldung, Lizenz und Geschäftsfunktionen überprüfen | Kernfunktionen können normal genutzt werden |

## 3. Vorbereitung vor der Bereitstellung

### 3.1 Serverinformationen vorbereiten

| Informationen | Beispiel | Beschreibung |
| --- | --- | --- |
| INSTALL_NODE_IP | `<INSTALL_NODE_IP>` | Einzelnode K8s Bereitstellung verwendet nur 1 Server |
| CPU Architektur | `amd64` / `arm64` | Installationsprogramm und Installationspaket müssen zur Serverarchitektur passen |
| Netzwerkumgebung | Online / Offline | Online wählen, wenn öffentliches Netzwerk zugänglich ist; Offline für interne oder isolierte Umgebungen wählen |
| Ausführender Benutzer | `root` oder ein Benutzer mit `sudo` Rechten | Das Installationsprogramm muss Bereitstellungsaufgaben über SSH |
| SSH Port | `22` | ausführen SSH Port wurde geändert, füllen Sie den tatsächlichen Port aus |
| Zugriffsprotokoll | HTTP / HTTPS | HTTP kann für Testumgebungen verwendet werden; HTTPS wird für Produktion oder externen Zugriff empfohlen |
| ACCESS_DOMAIN | `<ACCESS_DOMAIN>` oder `<INSTALL_NODE_IP>` | Die Einstiegadresse für Benutzer zum Zugriff ShimoDocs Suite |
| Datenverzeichnis | `/data` | Es wird empfohlen, ein separates Datenlaufwerk zu mounten |

### 3.2 Vorbereitung der Installationsmaterialien

| Material | Beispieldateiname | Beschreibung |
| --- | --- | --- |
| Installer | `mdp-installer-amd64` | Beispiel für `amd64` Architektur; ersetzen Sie durch den tatsächlichen Dateinamen für andere Architekturen |
| Produkt-Installationspaket | `co1.8.20260807.3639-drive-release..tar.gz` | Für Single-Node K8s Deployment wählen Sie das Distributionspaket, dessen Dateiname kein `k3s`enthält; der Dateiname richtet sich nach der tatsächlichen Lieferung |
| Basis-Offlinedateipaket | `smbase_image-amd64.tar.gz` | Nur für die Offline-Installation erforderlich |
| Produkt-Offlinedateipaket | `offline_app_image.tar.gz` | Nur für Offline-Installation erforderlich, muss mit der Version des Produkt-Installationspakets übereinstimmen |

Hinweis:

- Dateinamen in Befehlen müssen durch die tatsächlichen Dateinamen ersetzt werden, wie z.B. `mdp-installer-amd64`, `co1.8.<VERSION>-drive-release.tar.gz`.
- Das Produkt-Installationspaket, das Offline-Image-Paket und der Server CPU Architektur müssen konsistent sein.
- Vor der Offline-Installation wird empfohlen, das Basis-Offlinedateipaket und das Produkt-Offlinedateipaket auf einmal vorzubereiten, um eine temporäre Paket-Ergänzung während der Bereitstellung zu vermeiden.

### 3.3 Überprüfen der Serverressourcen

| Artikel | Empfohlene Anforderungen |
| --- | --- |
| Anzahl der Server | 1 |
| CPU | 16 Kerne oder mehr |
| Speicher | 32 GB oder mehr |
| Systemlaufwerk | Root `/` Partition 100 GB oder mehr |
| Datenfestplatte | Unabhängig eingehängt bei `/data`, verfügbarer Speicherplatz über 300 GB |
| Offline-Installation | Es wird empfohlen, zusätzlich mehr als 100 GB auf dem Datenlaufwerk für Image-Pakete und temporäre Extraktionsdateien zu reservieren |

Auf dem Server ausführen: 

```bash
lscpu
free -g
df -h
timedatectl status
```

Bestätigen Sie die folgenden Ergebnisse: 

- CPU, Speicher und Festplatte entsprechen den Bereitstellungsspezifikationen. 
- `/data` wurde an eine separate Datenfestplatte angeschlossen. 
- Die Systemzeitsynchronisation ist normal. 
- Der Server kann über SSH Anmeldung. 
- Die Online-Installationsumgebung kann auf das öffentliche Netzwerk zugreifen; die Offline-Installationsumgebung hat fertige Offline-Image-Pakete. 

### 3.4 Ports überprüfen 

| Port | Zweck | 
| --- | --- | 
| `22/TCP` | SSH Anmeldung und Ausführung von Installationsaufgaben | 
| `18080/TCP` | Installer-Webseite | 
| `80/TCP` oder `443/TCP` | ShimoDocs Suite Zugangsbereich | 

Wenn der Server eine Firewall oder Sicherheitsgruppe aktiviert hat, öffnen Sie bitte im Voraus die oben genannten Ports. 

## 4. Hochladen von Installationstools und -paketen 

Das folgende Beispiel verwendet die `amd64` Architektur. Für andere Architekturen ersetzen Sie bitte durch die tatsächlichen Dateinamen. 

### 4.1 Installer hochladen 

Auf dem lokalen Computer ausführen: 

```bash
scp mdp-installer-amd64 root@<INSTALL_NODE_IP>:/root/
```

### 4.2 Offline-Image-Paket hochladen

Dieser Schritt kann bei Online-Installation übersprungen werden.

Bei Offline-Installation muss das Offline-Image-Paket auf den Bereitstellungsknoten hochgeladen werden:

```bash
scp smbase_image-amd64.tar.gz offline_app_image.tar.gz root@<INSTALL_NODE_IP>:/root/
```

### 4.3 Anmeldung auf dem Server

```bash
ssh root@<INSTALL_NODE_IP>
```

### 4.4 Ausführungsberechtigung zum Installer hinzufügen

```bash
chmod +x /root/mdp-installer-amd64
```

### 4.5 Installer-Webseite starten

Auf dem Server ausführen:

```bash
cd /root
./mdp-installer-amd64 server
```

Wenn Sie möchten, dass der Installer im Hintergrund ausgeführt wird, können Sie Folgendes verwenden: 

```bash
nohup /root/mdp-installer-amd64 server > /root/mdp-installer.log 2>&1 &
```

Zugriff über Browser: 

```text
http://<INSTALL_NODE_IP>:18080
```

## 5. Installation über Webseite

### 5.1 Auswahl des Verteilungspakets

Nach dem Betreten der Installer-Webseite wählen Sie das zu implementierende Produktverteilungspaket aus.

Für K8s Bei Einzelknoten-Bereitstellung wählen Sie bitte das Verteilungspaket, dessen Dateiname nicht enthält `k3s`, zum Beispiel:

```text
co1.8.20260807.3639-drive-release.tar.gz
```

### 5.2 Konfiguration SSH Verbindung

Der Installer wird sich über SSH und führen Sie die Installationsaufgaben aus. SSH Einstellungen unterstützen zwei Authentifizierungsmethoden:

- Private-Schlüssel-Authentifizierung.
- PASSWORD Authentifizierung.

Es wird empfohlen, den `root` Benutzer oder einen Benutzer mit `sudo` Berechtigungen für die Durchführung der Bereitstellung zu verwenden. Nach dem Ausfüllen der Informationen können Sie zuerst die Verbindung testen, um sicherzustellen, dass der Installer sich normal am Bereitstellungsknoten anmelden kann.

### 5.3 Bestätigen der Grundkonfiguration

Nach der Auswahl des Vertriebspakets fahren Sie mit dem nächsten Schritt fort. Wenn keine besonderen Anforderungen bestehen, können Sie die Standardkonfiguration der Seite beibehalten; wenn die Bereitstellungsumgebung bereits einen klaren Plan für Domänennamen, Zertifikate, Netzwerksegmente oder Middleware hat, füllen Sie diese gemäß dem tatsächlichen Plan aus.

Wichtige Punkte, die bei der Konfiguration zu bestätigen sind: 

- Stellen Sie sicher, dass das Zugriffsprotokoll und ACCESS_DOMAIN korrekt ausgefüllt sind. 
- Pod CIDR und Dienst CIDR dürfen nicht mit dem bestehenden Netzwerk, Büronetzwerk, VPN, oder IDC Netzwerksegmente. 
- Verwenden Sie `/data` oder das tatsächlich geplante Datenlaufwerkverzeichnis für das Datenverzeichnis. 
- Die Online-/Offline-Installationsmethode entspricht der aktuellen Netzwerkumgebung. 

### 5.4 Erstbereitstellung 

Nach Abschluss der Konfiguration klicken Sie auf Erstbereitstellung. Die Seite zeigt eine Übersicht dieser Bereitstellung. Bitte konzentrieren Sie sich auf die Überprüfung von: 

- Produktpaketversion. 
- Bereitstellung NODE_IP. 
- SSH Benutzer und Port. 
- ACCESS_DOMAIN und Protokoll. 
- Datenverzeichnis. 
- Online- oder Offline-Installationsmodus. 
- Middleware-Auswahl. 

Fahren Sie fort, nachdem alles korrekt bestätigt wurde. 

### 5.5 Überprüfung der Systemumgebung

Der Installer überprüft automatisch die Serverumgebung.

Bereitstellung nach Bestehen der Prüfung fortsetzen. Wenn Elemente fehlgeschlagen sind, behandeln Sie diese bitte gemäß den Seitenhinweisen und prüfen Sie erneut. Häufige Vorgehensweisen umfassen: 

- Unzureichender Speicherplatz: Speicherplatz bereinigen oder die Datenfestplatte erweitern. 
- Port nicht verfügbar: Den Port freigeben oder die Portnutzung anpassen. 
- SSH Verbindung fehlgeschlagen: Überprüfen Sie das Konto, PASSWORD, privater Schlüssel, Port und Sicherheitsgruppe. 
- Zeit-Synchronisation abnormal: Konfigurieren NTP oder die Serverzeit kalibrieren. 
- Fehlende Grundbefehle: Fehlende Befehle gemäß der Systemdistribution installieren. 

### 5.6 Bereitstellung starten 

Nach Bestehen der Umweltprüfung klicken Sie, um die Bereitstellung zu starten. 

Während des Bereitstellungsprozesses können Sie die Ausführungsprotokolle jeder Komponente einsehen. Stellen Sie während der Installation bitte sicher: 

- Der Installationsprozess bleibt aktiv. 
- Der Browser kann eine Verbindung zum Installationsknoten-Netzwerk herstellen. 
- Den Server nicht neu starten. 
- Verschieben oder löschen Sie nicht das Installationspaket, das Offline-Image-Paket oder das Datenverzeichnis. 

### 5.7 Warten Sie, bis die Installation abgeschlossen ist

Der Installationsprozess erfordert einige Wartezeit, die genaue Dauer hängt von der Serverleistung, der Netzwerkumgebung und der Download-Geschwindigkeit des Images ab.

Wenn die Seite zeigt, dass alle Aufgaben erfolgreich ausgeführt wurden und keine Komponenten fehlgeschlagen sind, bedeutet dies, dass die Bereitstellung abgeschlossen ist.

### 5.8 Bestätigung des Installationsergebnisses

Nach Abschluss der Installation zeigt der Installer eine Seite zur Bereitstellungsfertigstellung und Zugriffsinformationen an. Bitte bestätigen Sie zunächst, dass auf der Seite keine fehlgeschlagenen Aufgaben vorhanden sind, bevor Sie auf das Geschäftssystem und MDP die Operations-Plattform zugreifen.

Besuchen Sie die Geschäftsadresse: 

```text
http://<ACCESS_DOMAIN>/
```

Wenn HTTPS während der Installation konfiguriert wurde, besuchen Sie bitte: 

```text
https://<ACCESS_DOMAIN>/
```

Nach der Anmeldung mit dem Standardkonto oder Administratorkonto ändern Sie bitte sofort PASSWORD das Initialpasswort.

Zugriff auf MDP Operations-Plattform zugreifen: 

```text
http://<ACCESS_DOMAIN>/mdp/
```

Wenn Sie den MDP Administrator PASSWORDändern müssen, können Sie den folgenden Befehl auf dem Bereitstellungsknoten ausführen, um den PASSWORD.
zu ändern oder zurückzusetzen. PASSWORD Bitte ersetzen Sie {password} durch ein neues, komplexes starkes

```bash
kubectl exec -it $(kubectl get pods -l app=mdp -o jsonpath='{.items[0].metadata.name}') -- reset-admin-password {password}
```

## 6. Abnahme nach der Installation

### 6.1 Überprüfen K8s 5.1 Überprüfung

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

### 6.2 Zugriffsprüfung 

Zugriff auf ShimoDocs Suite 5.2 Überprüfung des Zugangs 

```text
http://<ACCESS_DOMAIN>/
```

Wenn HTTPS Zugang über den Browser: 

```text
https://<ACCESS_DOMAIN>/
```

Bestätigen, dass die Anmeldeseite normal geöffnet werden kann.

### 6.3 Verwaltungshintergrund und Lizenz überprüfen

Bestätigen Sie die folgenden Punkte:

- Der Admin-Hintergrund ist zugänglich.
- Der Administrator kann sich anmelden.
- Die Lizenz-Seite kann geöffnet werden.
- Maschineninformationen können angezeigt werden.
- Die Lizenz kann gemäß dem Autorisierungsprozess beantragt oder aktualisiert werden.

### 6.4 Überprüfung der Geschäftsabläufe

Nach dem Einloggen mit einem Testkonto oder einem vom Administrator erstellten Konto mindestens überprüfen:

- Sie können Dokumente, Tabellenkalkulationen und Präsentationen erstellen.
- Das Dokument kann bearbeitet und gespeichert werden, und der Inhalt ist nach dem Aktualisieren weiterhin vorhanden.
- Mehrbenutzer-Zusammenarbeit bei der Bearbeitung ist verfügbar.
- Dateiimport und -export funktionieren normal.
- Kernfunktionen wie Suche, Teamräume und Kontaktlisten sind verfügbar.

Nach dem ersten Einloggen mit dem standardmäßigen Testkonto bitte ändern Sie das PASSWORD das Initialpasswort.
Konto PASSWORD ist das Bereitstellungs-Lieferkonto PASSWORD!
```text
ACCOUNT:autotest@example.com
PASSWORD:xxxxx
```

### 6.5 Beenden des Installationsprogramms

Nach Abschluss der Bereitstellung und Bestehen der Abnahme kann der Webdienst des Installers gestoppt werden:
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

## 7. Häufig auftretende Probleme

### 7.1 Browser kann Installationsseite nicht öffnen

Überprüfen Sie Folgendes:

- Ob der Installateurprozess noch läuft.
- Ob Port `18080` durch eine Firewall oder Sicherheitsgruppe blockiert ist.
- Ob die vom Browser aufgerufene IP INSTALL_NODE_IP.

Sie können Folgendes auf dem Server ausführen:

```bash
ps -ef | grep mdp-installer | grep -v grep
ss -lntp | grep 18080
```

### 7.2 Umweltprüfung fehlgeschlagen

Behandeln Sie jeden Punkt gemäß den Aufforderungen auf der Seite. Nach der Bearbeitung kehren Sie zur Installateurseite zurück und führen die Umgebungsprüfung erneut aus.

Prioritätsprüfungen:

- Ob die CPU, der Speicher und die Festplatte die Anforderungen erfüllen.
- Ob `/data` eine eigenständige Datenfestplatte ist.
- Ob die Serverzeit synchronisiert ist.
- Ob die SSH Der Benutzer hat Bereitstellungsberechtigungen.

### 7.3 Abrufen des Offline-Installationsabbilds fehlgeschlagen

Überprüfen Sie folgende Richtungen:

- Ob das Offline-Image-Paket auf den Bereitstellungsknoten hochgeladen wurde.
- Ob das Basis-Offline-Image-Paket und das Produkt-Offline-Image-Paket vollständig sind.
- Ob die Version des Image-Pakets dem Produktinstallationspaket entspricht.
- Ob die Adresse des privaten Image-Repositorys, das Konto und PASSWORD sind korrekt ausgefüllt.

### 7.4 Pod bleibt lange in einem abnormalen Zustand

Überprüfen Sie zunächst den abnormen Pod:

```bash
kubectl get pod -A
```

Überprüfen Sie erneut die Protokolle: 

```bash
kubectl logs -n <namespace> <pod-name>
```

Behandeln Sie Probleme mit Images, Konfigurationen, Ressourcen oder Abhängigkeiten basierend auf den Protokollen.

## 8. Materialien nach der Installation aufbewahren

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
