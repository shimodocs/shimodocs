# Container-Paket-Erfassung

[← ShimoDocs Suite Bereitstellungsdokumentation](../../../README.md)

## Funktionsübersicht 

Die Container-Paketaufzeichnung wird verwendet, um Netzwerkdaten von laufenden Pods in einer Kubernetes Umgebung zu sammeln und hilft Ihnen, Probleme wie Verbindungsfehler, Anforderungs-Timeouts, TCP Neusendungen und Netzwerküberlastung zu analysieren. 

Nach Abschluss der Aufzeichnung können Sie die PCAP Datei herunterladen und sie mit Netzwerk-Analysetools wie Wireshark weiter untersuchen. 

## Zugriff auf die Seite 

Nach dem Einloggen in die Managementkonsole wählen Sie **Container-Paket-Erfassung** im linken Navigationsbereich, um die Seite zu betreten. 

Die Container-Paketaufzeichnung ist nur in Kubernetes Bereitstellungsumgebungen anwendbar und nur für Administratoren verfügbar. 

## Starten einer Paketaufzeichnung 

Es wird empfohlen, diese Schritte zu befolgen: 

1. Suchen und lokalisieren Sie den Ziel-Pod in der **Pods-Liste**. 
2. Stellen Sie sicher, dass der Pod-Status auf Running steht, und klicken Sie dann auf **Start Capture**. 
3. Wählen Sie die Erfassungsdauer: 1 Minute, 5 Minuten oder 30 Minuten. 
4. Wählen Sie die Größe der Erfassungsdatei: 100 MB, 500 MB oder 1 GB. 
5. Wählen Sie bei Bedarf Filterbedingungen aus oder geben Sie manuell einen tcpdump-Filterausdruck ein. 
6. Überprüfen Sie den vollständigen Erfassungsbefehl, der auf der Seite angezeigt wird. 
7. Klicken Sie **Start Capture** um die Aufgabe zu erstellen. 

Es kann nur eine Paketaufgabe gleichzeitig auf demselben Pod ausgeführt werden. Die Aufgabe endet automatisch, wenn die eingestellte Dauer erreicht ist, oder sie kann manuell gestoppt werden. 

## Filterbedingungen 

Das Festlegen von Filterbedingungen kann nicht verwandten Datenverkehr und die Dateigröße reduzieren. Die Seite bietet einige häufig verwendete Voreinstellungen, wie zum Beispiel: 

- Datenverkehr auf bestimmten Ports. 
- gRPC Datenverkehr. 
- Bestimmter Host und Port. 
- HTTP POST Anfragen. 
- TCP Verbindungsaufbau, Wiederholungen oder Pakete mit kleinem Fenster. 

Sie können auch manuell mit tcpdump-Syntax eingeben, zum Beispiel: 

```text
host 10.0.0.1 and port 80
```

Wenn keine Filterbedingungen angegeben sind, kann die Aufgabe eine große Menge an Netzwerkverkehr vom Pod erfassen.

## Paketerfassung Aufgaben verwalten

Auf der **Paketerfassung Aufgaben** Auf dieser Seite können Sie die Aufgaben-ID, den Pod, den Status, die Erstellungszeit und die Laufzeit einsehen.

- **Läuft**: Die Aufgabe kann manuell gestoppt werden.
- **Abgeschlossen**: Der PCAP Die Datei kann heruntergeladen werden.
- **Fehlgeschlagen**: Sie können die Aufgabenprotokolle einsehen, um den Grund für das Scheitern zu verstehen.

Die Aufgabenliste wird automatisch aktualisiert, oder Sie können klicken **Aktualisieren** , um den neuesten Status manuell abzurufen.

## Download und Analyse

Nachdem die Aufgabe abgeschlossen ist, klicken Sie **Herunterladen** , um die PCAP Datei zu erhalten. Die Download-Funktion hängt davon ab, dass das System ordnungsgemäß mit Objektspeicher konfiguriert ist.

PCAP Dateien können Anforderungsadressen, Protokolldaten oder andere sensible Informationen enthalten. Bitte stellen Sie sie nur autorisiertem Personal zur Verfügung und speichern oder löschen Sie sie nach der Verwendung ordnungsgemäß.

## Häufige Situationen

- **Pod nicht gefunden**: Die Seite zeigt nur Pods an, die sich im Running-Zustand in der aktuellen Umgebung befinden. Bitte überprüfen Sie den Pod-Status und die Bereitstellungsumgebung.
- **Nicht in der Lage, die Paket-Erfassung zu starten**: Bitte stellen Sie sicher, dass der Pod keine laufende Paket-Erfassungsaufgabe hat, und überprüfen Sie Kubernetes Berechtigungen und Unterstützung für ephemere Container.
- **Aufgabenausführung fehlgeschlagen**: Überprüfen Sie die Aufgabenprotokolle, um den Filterausdruck, den Pod-Status und die Funktionsfähigkeit der Paketaufzeichnungs-Komponenten zu prüfen.
- **Datei konnte nicht heruntergeladen werden**: Bitte überprüfen Sie die Objekt-Speicher-Konfiguration und die Netzwerkverbindung.
- **Paketaufzeichnungsdatei zu groß**: Verringern Sie die Dauer der Aufzeichnung und verwenden Sie einen präziseren Filterausdruck.

> Packet-Erfassung verbraucht bestimmte Netzwerk-, CPUund Speicherressourcen. Bitte vermeiden Sie es, während Spitzenzeiten langandauernde, hochvolumige Erfassungen ohne Filter durchzuführen.
