# Schnellstart

[← ShimoDocs Suite Bereitstellungsdokumentation](../README.md)

> [!TIP]
>
> Dieser Artikel beschreibt, wie man das `mdp-installer` verwendet, um schnell eine frische Umgebung von ShimoDocs Suite.
>
> Dieser Artikel ist für die **All-in-One-Einzelknoten-Online-Installation** Szenario, geeignet für die Erstinstallation, Produkterfahrung, Funktionsüberprüfung und Übung des Bereitstellungsprozesses. Nach Abschluss dieses Artikels können Sie die ShimoDocs Suite Geschäftszugangsadresse und die MDP Operations-Plattform-Adresse erhalten.

> Die IP-Adressen, Installationspaketnamen VERSIONund Verzeichnisse auf der Seite dienen als Beispiele. Während der tatsächlichen Bereitstellung beachten Sie bitte die aktuelle Umgebung und die gelieferten Materialien.

## 1. Übersicht über den Bereitstellungsprozess

Der gesamte Prozess kann in die folgenden 7 Schritte unterteilt werden:

| Schritt | Auszuführende Aktion | Abschlussindikator |
| --- | --- | --- |
| 1. Server und Installationsmaterialien vorbereiten | Stellen Sie sicher, dass die Server, der Installer und ShimoDocs Suite das Distributionspaket verfügbar sind | Fähig, sich am Server anzumelden und die Installationsdateien zu finden |
| 2. Installer starten | Führen Sie `mdp-installer` auf dem Installationsknoten aus | Das Terminal zeigt die Installationszugangsadresse an |
| 3. Laden Sie das Distributionspaket hoch | Wählen Sie das ShimoDocs Suite Distributionspaket im Browser aus | Die Seite zeigt an, dass das Distributionspaket die Überprüfung bestanden hat |
| 4. Konfigurieren Sie die Bereitstellungsumgebung | Geben Sie die Domain oder IP, den Bereitstellungsmodus, die Anmeldeinformationen des Knotens und die Datenverzeichnisse ein | Knoten werden erfolgreich überprüft und die Bereitstellungsübersicht kann aufgerufen werden |
| 5. Umgebung prüfen | Warten Sie, bis der Installer den Server und die Bereitstellungsumgebung überprüft hat | Keine Fehler, die die Installation blockieren |
| 6. Installation starten | Bestätigen Sie die Prüfergebnisse und führen Sie die Bereitstellung aus | Die Seite zeigt an, dass die Installation abgeschlossen ist |
| 7. Lieferinformationen speichern | Speichern Sie die Zugriffsadresse und schließen Sie die Anmeldung, Service- und Funktionsüberprüfung ab | Geschäftsseiten und MDP Betriebsplattform können zugegriffen werden |

## 2. Vorbereitungen vor der Bereitstellung

### 1. Bereiten Sie einen Installationsknoten vor

Der Installationsknoten wird zum Ausführen des Installers verwendet und dient als Zielserver für diese All-in-One-Einzelknoten-Bereitstellung.

Bitte bestätigen Sie vor dem Start:

- Ein nutzbarer Server wurde vorbereitet und die SERVER_IP-Adresse wurde erhalten.
- Der Server kann über SSH.
- Der SSH Benutzer ist `root`, oder verfügt über die notwendigen Berechtigungen, um Bereitstellungsaufgaben auszuführen.
- Die Server CPU Architektur stimmt mit dem Installer und dem Distributionspaket überein, zum Beispiel sind beide `x86_64`.
- Der Server erfüllt die aktuellen Bereitstellungsspezifikationen; es wird empfohlen, eine minimal installierte Ubuntu 24.04 zu verwenden LTS.
- Die Root-Partition und der Datenbereich entsprechen den aktuellen Bereitstellungsanforderungen, und das Datenverzeichnis wurde bestimmt.
- Die Serverzeit und Zeitzone sind korrekt, und die Zeitsynchronisierung ist normal.
- Der Computer mit dem Browser kann auf Port `18080/TCP` des Installationsknotens zugreifen.
- Der Server kann auf das Internet zugreifen, um Bereitstellungspakete und Image-Ressourcen online herunterzuladen.
- Wenn der Geschäftsverkehr Domainnamen verwendet, wurde die Namensauflösung im Voraus abgeschlossen (optional).

Die minimalen Serveranforderungen sind wie folgt:

| Betriebssystem | Architektur | CPU | Speicher | Festplatte |
| --- | --- | --- | --- | --- |
| Ubuntu 24.04 LTS | `x86_64` | 16 Kerne | 32 GB | 100 GB SSD |

Darüber hinaus bitte bestätigen:

- Nicht partitionieren `/root`, `/var`, `/tmp` getrennt.
- Vor der Installation keine zusätzlichen Komponenten wie Docker oder Kubernetes auf dem Server installieren, die die Überprüfungen des Installers beeinflussen könnten.
- Port `22/TCP` wird verwendet für SSH, `18080/TCP` wird für die Installer-Webseite verwendet, `80/TCP` und `443/TCP` werden für den Geschäftsverkehr verwendet.

> Vor der offiziellen Bereitstellung wird empfohlen, die Serverspezifikationen gemäß der tatsächlichen Gleichzeitigkeit, Dateigröße und Verfügbarkeitsanforderungen zu bestätigen; der Single-Node-Prozess in diesem Dokument ist für eine schnelle Bereitstellung und Überprüfung geeignet, für den langfristigen Betrieb oder Hochverfügbarkeit bitte das entsprechende Cluster-Bereitstellungsschema verwenden.

### 2. Installationsmaterialien vorbereiten

#### Installer beschaffen

Laden Sie den von ShimoDocs bereitgestellten Installer in das `/root/` Verzeichnis des Installationsknotens hoch. Sie können eine der folgenden Methoden wählen:

- **Methode 1: Hochladen über SSH Werkzeug**. Hochladen `mdp-installer-amd64` bereitgestellten Installer in das `/root/` Verzeichnis des Installationsknotens.

#### Verteilungspaket erhalten

Bereiten Sie das ShimoDocs Suite Verteilungspaket für diese Bereitstellung vor. Das Verteilungspaket wird auf der Web-Installationsseite hochgeladen, und der Dateiname sowie die Version sollten der tatsächlichen Lieferung entsprechen.

Beispielfilename: `co1.8.20260711.3286-drive-release.tar.gz`

Die Stückliste ist wie folgt:

| Datei | Beschreibung |
| --- | --- |
| `mdp-installer` Installer | Wählen Sie die entsprechende Datei je nach Serverarchitektur, zum Beispiel, `mdp-installer-amd64`. |
| ShimoDocs Suite Verteilungspaket | Der Dateiname und die Version sollten der tatsächlichen Lieferung entsprechen, zum Beispiel, `co1.8.20260711.3286-drive-release.tar.gz`. |

Es wird empfohlen, den Installer und die zugehörigen Installationsmaterialien im gleichen Arbeitsverzeichnis abzulegen, um das spätere Abrufen und Speichern zu erleichtern. Bitte stellen Sie vor der Verwendung des Verteilungspakets sicher, dass die Dateien vollständig und durch das Übertragungsprogramm nicht beschädigt sind.

## 3. Starten Sie den Installer

### 1. Melden Sie sich am Installationsknoten an

Melden Sie sich am Installationsknoten über SSH an und navigieren Sie zu dem Verzeichnis, in dem sich der Installer befindet. Zum Beispiel:

```bash
ssh root@<INSTALL_NODE_IP>
cd /root
```

### 2. Ausführungsberechtigung hinzufügen

Wenn der Installer noch keine Ausführungsberechtigung hat, führen Sie aus:

```bash
chmod +x ./mdp-installer-amd64
```
Der Dateiname im Befehl muss durch den tatsächlichen Installernamen ersetzt werden. 

### 3. Starten Sie die Web-Installationsseite 

Führen Sie aus: 

```bash
./mdp-installer-amd64 server
```

Wenn der Installer auch nach dem Beenden des aktuellen Terminals weiterlaufen soll, können Sie verwenden: 

```bash
nohup ./mdp-installer-amd64 server > nohup.out 2>&1 &
```

Nach erfolgreichem Start zeigt das Terminal zwei Adressen an: 

- `Local`: Nur für die Verwendung durch den Installationsknoten selbst. 
- `Network`: Von anderen Computern im selben Netzwerk zugänglich. 

Wenn im Hintergrundmodus gestartet, können Sie den folgenden Befehl ausführen, um die Installer-Ausgabe anzuzeigen: 

```bash
cat nohup.out
```

Öffnen Sie die `Network` Adresse, die im Terminal in einem Browser angezeigt wird, zum Beispiel:

```text
http://<INSTALL_NODE_IP>:18080/
```

> Bitte halten Sie während der Installation den Installationsprozess aktiv. Schließen Sie den Installationsprozess nicht oder stoppen Sie den aktuellen Dienst nicht.

## 4. Hochladen ShimoDocs Suite Release-Paket

### 1. Wählen Sie das Release-Paket aus

Nach dem Betreten der Installationsseite:

1. Klicken Sie **Bereitstellung starten** oder den Eintrag zur Release-Paket-Auswahl auf der Seite.
2. Wählen Sie das ShimoDocs Suite `.tar.gz` Release-Paket aus, das für diese Bereitstellung verwendet werden soll.
3. Warten Sie, bis der Datei-Upload und die Überprüfung abgeschlossen sind.

### 2. Überprüfungsergebnisse bestätigen

Nachdem die Überprüfung bestanden ist, zeigt die Seite den Namen des Release-Pakets und den Eintrag der Bereitstellungskonfiguration an.

Bitte bestätigen Sie, dass die folgenden Informationen korrekt sind:

- Der Paketname stimmt mit der dieses Mal gelieferten Version überein.
- Das Freigabepaket gehört zum ShimoDocs Suite Produkt.
- Die Seite zeigte keine Datei­beschädigung, Format­fehler oder Schema­abweichung an.

Nach der Bestätigung klicken Sie **Fortfahren** um zur Bereitstellungskonfiguration zu gehen.

Wenn die Überprüfung fehlschlägt, bitte bestätigen Sie erneut, ob das Distributionspaket vollständig ist, der Dateityp korrekt ist und ob das Distributionspaket zur Serverarchitektur passt. CPU 

## 5. Konfiguration der Bereitstellungsumgebung

### 1. Bestätigen Sie die Netzwerkadresse

Überprüfen Sie den auf der Seite angegebenen Hostnamen oder die IP-Adresse. Diese Adresse sollte die Installationsknotenadresse sein, die vom Benutzer normal erreicht werden kann.

Nicht eingeben `127.0.0.1`und verwenden Sie keine temporären Adressen, die nur vom aktuellen Computer aus zugänglich sind. Beim Zugriff über einen Domainnamen stellen Sie bitte sicher, dass der Domainname auf den richtigen Service-Eintrag aufgelöst wurde.

### 2. Wählen Sie den All-in-One-Einzelknoten-Modus

Auswählen **All-in-One-Einzelknoten** im Bereitstellungsmodus oder Zielumgebung (der tatsächlich auf der Seite angezeigte Name hängt von der aktuellen Version ab).

In diesem Modus übernimmt der Installationsknoten gleichzeitig die Steuerungs- und Geschäftsrollen für diese Bereitstellung, wodurch er für leichtgewichtige Umgebungen zur Produkterfahrung, Funktionsvalidierung und Einzelknotenplanung geeignet ist.

### 3. Knoten konfigurieren SSH

Der Installer verbindet sich mit dem Zielknoten über SSH und führt Bereitstellungsaufgaben aus. Bitte füllen Sie Folgendes aus:

- NODE_IP-Adresse.
- SSH Benutzer, normalerweise `root`.
- SSH Port, normalerweise `22` standardmäßig.
- PASSWORD oder private Schlüssel-Authentifizierungsinformationen.

Nach dem Ausfüllen klicken Sie auf **Überprüfen** um zu bestätigen, dass die SSH Verbindung erfolgreich ist.

> SSH Anmeldeinformationen sollten nur in einer kontrollierten Umgebung verwendet und gespeichert werden. Schreiben Sie nicht PASSWORD oder private Schlüssel in öffentliche Dokumente, Screenshots oder Chat-Aufzeichnungen.

### 4. Datenverzeichnis und weitere Konfigurationen festlegen

Füllen Sie die folgenden Konfigurationen aus oder bestätigen Sie sie gemäß den Seitenhinweisen:

| Konfigurationselement | Beschreibung |
| --- | --- |
| ACCESS_DOMAIN / IP | Adresse für den Benutzerzugriff ShimoDocs Suite; bei Verwendung einer IP geben Sie die tatsächlich zugängliche Adresse ein. |
| Bereitstellungsmodus | Wählen Sie den All-in-One-Einzelknoten-Modus. |
| Knotendatenverzeichnis | Dient zum Speichern der Bereitstellungsdaten. Bitte stellen Sie sicher, dass die Festplatte über genügend Speicherplatz verfügt und Lese-/Schreibberechtigungen vorhanden sind. |
| Offline-Repository | Dieser Leitfaden ist für die Online-Installation; behalten Sie den Standardwert auf der Seite bei. |
| Middleware von Drittanbietern | Dieser Leitfaden verwendet die Standardbereitstellung; bestätigen Sie je nach aktuellen Lieferanforderungen, ob externe Middleware benötigt wird. |

Wenn keine besonderen Konfigurationsanforderungen bestehen, können Sie die Standardwerte für das Offline-Repository und die Middleware von Drittanbietern beibehalten. Nach der Überprüfung klicken Sie **Bereitstellung initialisieren** am unteren Rand der Seite.

## 6. Übersicht der Bereitstellung bestätigen

Die Bereitstellungsübersicht dient dazu, die Installationskonfiguration vor der formellen Prüfung zu überprüfen.

Bitte achten Sie besonders auf Folgendes:

- Stellen Sie sicher, dass die Version des Release-Pakets und der Produktname korrekt sind.
- ACCESS_DOMAIN oder IP korrekt ist und nicht `127.0.0.1`.
- Der Bereitstellungsmodus ist All-in-One Single Node.
- NODE_IP, SSH Benutzer und Port sind korrekt.
- Das Datenverzeichnis ist korrekt und der Speicherplatz ausreichend.
- Die Konfiguration des Offline-Repositories und der Middleware von Drittanbietern entspricht der aktuellen Umgebung.

Nach Bestätigung, dass keine Fehler vorliegen, klicken Sie **Fortfahren** um mit der Umgebungsüberprüfung fortzufahren.

## 7. Umgebungsprüfung durchführen

Der Installer überprüft die Knoten und die Bereitstellungsumgebung. Der Prüfvorgang kann einige Minuten dauern, bitte lassen Sie die Seite geöffnet.

### 1. Knotenübersicht anzeigen

Die Knotenübersicht zeigt den Fortschritt von Prüfungen wie SSH Konnektivität, System- und Leistung, Speicher und Festplatte, Netzwerk und Bereitstellungsumgebung an.

Um die detaillierten Ergebnisse einer bestimmten Prüfung anzuzeigen, klicken Sie auf das entsprechende Prüfelement oder Detaileintrag.

### 2. Detaillierte Prüfergebnisse anzeigen

Detaillierte Ergebnisse umfassen typischerweise:

- SSH Konnektivität und Benutzerberechtigungen für die Ausführung.
- Betriebssystem, CPU Architektur und Anzahl der Kerne.
- Speicherkapazität, Festplattenspeicher und Verzeichnisberechtigungen.
- Zeitzone und Status der Zeitsynchronisation.
- Netzwerk, Bildressourcen und Verbindung zu externen Diensten.
- Umweltbedingte Rückstände auf dem Server, die die Bereitstellung beeinträchtigen können.

### 3. Status der Prüfung verstehen

| Status | Bedeutung | Nächste Aktion |
| --- | --- | --- |
| Erfolg | Das aktuelle Prüfelement erfüllt die Bereitstellungsanforderungen | Warten, bis andere Elemente abgeschlossen sind |
| Warnung | Blockiert die Bereitstellung nicht direkt, muss jedoch bestätigt werden, ob sie mit dem aktuellen Plan übereinstimmt | Details öffnen und nach Bestätigung der Auswirkungen fortfahren |
| Fehler | Das aktuelle Problem kann die Installation oder den Betrieb des Produkts beeinträchtigen | Problem zuerst beheben, dann erneut prüfen |
| In Bearbeitung | Der Installer führt die Prüfung durch | Warten, bis die Prüfung abgeschlossen ist, keine wiederholten Aktionen durchführen |

Wenn ein Element lange in 'In Bearbeitung' bleibt, können Sie zunächst warten, bis die aktuelle Festplatten- oder Remote-Prüfung abgeschlossen ist, bevor Sie entscheiden, ob eine erneute Prüfung nötig ist.

### 4. Umgang mit Warnungen und Fehlern

Wenn die Seite eine Warnung anzeigt:

1. Öffnen Sie die detaillierte Beschreibung des entsprechenden Prüfelements.
2. Bestätigen Sie, ob die Warnung mit dem aktuellen Bereitstellungsplan übereinstimmt.
3. Wenn unsicher, speichern Sie die Seite und die Installer-Protokolle, und kontaktieren Sie dann Personal für Implementierung oder Betrieb zur Bestätigung.

Wenn die Seite nicht angezeigt werden kann: 

1. Befolgen Sie die Anweisungen, um Probleme mit SSHBerechtigungen, Ressourcen, Festplatte, Netzwerk oder Middleware zu beheben. 
2. Klicken Sie **Erneut scannen**. 
3. Bestätigen Sie, dass die fehlgeschlagenen Elemente verschwunden sind. 

Nachdem sichergestellt wurde, dass keine Fehler die Bereitstellung blockieren und alle Warnungen bestätigt wurden, klicken Sie auf **Fortfahren**. 

## 8. Installation starten 

### 1. Installationsplan bestätigen 

Die Seite zeigt den Installationsplan und die auszuführenden Aufgaben an. Nachdem Sie bestätigt haben, dass sie korrekt sind, klicken Sie auf **Bereitstellung starten**. 

Auf der Seite kann eine Eingabeaufforderung „Bestätigen, um die Installation zu starten“ erscheinen. Sobald gestartet, wird die Installation wie geplant durchgeführt; wenn Sie die Konfiguration anpassen müssen, klicken Sie bitte auf **Abbrechen** um zum vorherigen Schritt zurückzukehren.

### 2. Bereitstellungsfortschritt prüfen

Nach dem Start der Bereitstellung zeigt die Seite den aktuellen Aufgabenstatus, Echtzeitprotokolle und die Ausführungszeit an. Die Bereitstellung auf einem einzelnen Knoten dauert normalerweise etwa 10 Minuten, die tatsächliche Dauer hängt jedoch von der Serverleistung und der Netzwerkbandbreite ab.

Bitte beachten Sie während des Installationsvorgangs: 

- Schließen Sie den Installationsprozess nicht. 
- Starten Sie die Installationsknoten nicht neu. 
- Aktualisieren Sie die Seite nicht, gehen Sie nicht zurück und senden Sie die Installationsaufgabe nicht erneut. 
- Wenn die Aufgabe fehlschlägt, prüfen Sie zuerst den ersten Fehler im entsprechenden Aufgabenprotokoll und gehen Sie dann gemäß der Anweisung vor. 

Wenn die Seite anzeigt **Installation abgeschlossen** oder die Seite **Bereitstellungszustellung** aufgerufen wird, bedeutet dies, dass die Installation abgeschlossen ist. 

## 9. Zustellungsinformationen speichern 

Die Seite zur Abschluss der Installation zeigt die Zugriffs­informationen und die Prüf­einträge für diese Bereitstellung an. Bitte führen Sie sofort die folgenden Aktionen aus: 

1. Führen Sie die Nachinstallations­serviceprüfung durch und bestätigen Sie die Prüfergebnisse. 
2. Öffnen Sie mit den Zugriffs­informationen auf der Bereitstellungs­lieferseite die ShimoDocs Suite Geschäfts­seite und führen Sie die Anmelde­prüfung durch. 
3. Notieren Sie die ShimoDocs Suite Geschäftszugangsadresse und die MDP Adresse der Operations-Plattform. 
4. Speichern Sie das Anfangs­konto und das temporäre PASSWORDund ändern Sie sofort das Anfangs­passwort PASSWORD nach der ersten Anmeldung. 
5. Überprüfen Sie die Cluster-Knoten und den Anwendungs­status in der MDP Operations-Plattform. 

> Die Lieferinformationen enthalten Zugriffsadressen und Anfangs­zugangsdaten. Machen Sie keine Screenshots und verbreiten Sie diese nicht, laden Sie sie nicht in öffentliche Wissensdatenbanken hoch und senden Sie sie nicht über unkontrollierte Kanäle. 

## 10. Überprüfen Sie die Bereitstellungsergebnisse 

Nach Abschluss der Installation wird empfohlen, die Annahme in folgender Reihenfolge durchzuführen: 

### 1. Überprüfen Sie die Nachinstallationsdienste 

Führen Sie Nachinstallationsprüfungen auf der Seite zum Abschluss der Installation durch, um zu bestätigen, dass die Servicetestfälle bestanden wurden oder die Ergebnisse den Erwartungen der aktuellen Umgebung entsprechen. 

Wenn die Überprüfung fehlschlägt oder nur teilweise bestanden wird, können Sie eine Inspektionsaufgabe erneut einreichen in MDP Operations-Plattform. 

### 2. Überprüfen MDP Operations-Plattform

Melden Sie sich bei der MDP Operations-Plattform an, gehen Sie zu **Systemdienste → Clusterverwaltung**, und bestätigen Sie, dass die Clusterknoten und der Anwendungsstatus normal sind.

### 3. Überprüfen Sie ShimoDocs Suite Funktionen

Melden Sie sich bei der ShimoDocs Suite Frontend-Seite und überprüfen Sie mindestens die folgenden Funktionen:

- Erstellen Sie eine Testdatei oder Suite.
- Inhalt bearbeiten und speichern.
- Dateien exportieren.
- Dateien importieren.

Nachdem alle oben genannten Prüfungen erfolgreich abgeschlossen wurden, zeigt dies an, dass diese Schnellbereitstellung abgeschlossen ist. Wenn langfristiger Betrieb, Skalierung oder Hochverfügbarkeit in der Zukunft erforderlich ist, wechseln Sie bitte entsprechend zum entsprechenden Bereitstellungsplan gemäß dem tatsächlichen Umfang und schließen Sie die Lizenz- und Geschäftskonfiguration ab.

## 11. Häufige Fragen

### 1. Der Browser kann die Installationsseite nicht öffnen

Überprüfen Sie in folgender Reihenfolge:

- Ob der Installateurprozess noch läuft.
- Ob die Zugriffsadresse die tatsächliche IP des Installationsknotens oder einen auflösbaren Domainnamen verwendet.
- Ob Port `18080/TCP` wurde geöffnet.
- Ob das Netzwerk zwischen dem Computer mit dem Browser und dem Installationsknoten verbunden ist.

### 2. Verteilungspaket-Verifizierung fehlgeschlagen

Überprüfen:

- Ob die hochgeladene Datei ein vollständiges `.tar.gz` Release-Paket ist.
- Ob der Dateiname und der Produkttyp mit dieser Lieferung übereinstimmen.
- Ob das Release-Paket mit dem Server übereinstimmt CPU 
- Ob die Datei beim Hochladen oder Übertragen beschädigt wurde.

### 3. SSH Authentifizierung fehlgeschlagen

Überprüfen:

- Ob die NODE_IP und SSH Port sind korrekt.
- Ob die SSH Benutzer, PASSWORDoder privater Schlüssel ist korrekt.
- Hat der SSH Benutzer die für die Bereitstellung erforderlichen Berechtigungen?
- Ob die Firewall oder Sicherheitsgruppe SSH Verbindungen zulässt.

### 4. Warnungen in der Umgebungsprüfung

Warnungen verhindern nicht direkt die Bereitstellung, aber Sie müssen die Details öffnen, um die Auswirkungen zu bestätigen. Wenn es die Plattenleistung, die Zeitsynchronisation, Konfigurationsreste oder externe Dienste betrifft, bestätigen Sie zuerst, ob dies mit dem aktuellen Bereitstellungsplan übereinstimmt, bevor Sie entscheiden, ob Sie fortfahren.

### 5. Fehler bei der Überprüfung der Umgebung

Fehlerhafte Punkte müssen zuerst behoben werden. Überspringen Sie die Überprüfung nicht und starten Sie die Installation nicht direkt. Nach der Behebung klicken Sie **Erneut scannen** um zu bestätigen, dass die fehlerhaften Punkte bestanden haben.

### 6. Fehler bei der Installation

1. Öffnen Sie das Ausführungsprotokoll der fehlgeschlagenen Aufgabe.
2. Suchen Sie die erste Fehlermeldung, die aufgetreten ist.
3. Speichern Sie das Installationsprotokoll, den Namen der fehlgeschlagenen Aufgabe und die Auftretenszeit.
4. Nach Behebung der entsprechenden Netzwerk-, Festplatten-, Image-, Middleware- oder Kubernetes Probleme, fahren Sie entsprechend der tatsächlichen Wiederherstellungsmethode fort.
