# Bereitstellen mit Redis

[← ShimoDocs Suite Bereitstellungsdokumentation](../../README.md)

Dieser Artikel erklärt, wie man die integrierte Redis im ShimoDocs Installer deaktiviert und der eigene Redis als eine Drittanbieter-Cache-Datenbank. Nach der Konfiguration überprüft der Installer die Redis Netzwerkverbindung, Verbindung, Authentifizierung, Befehlsausführung, Publish/Subscribe-Berechtigungen usw. Sobald die Überprüfungen bestanden sind, kann die Bereitstellung fortgesetzt werden. 

# 1. Vorbereitung vor der Konfiguration 
Bitte bestätigen Sie vor Beginn: 
- Der Redis Der Server ist installiert und läuft normal. 
- Die Bereitstellungsknoten können auf den Redis Host und Port des Servers zugreifen. 
- Authentifizierungsbenutzerinformationen und PASSWORD zum Verbinden mit dem Redis Server sind vorbereitet. 

> [!TIP] 
> 
> Die IP, der Port und das Konto in diesem Artikel sind Beispiele. Bitte verwenden Sie die tatsächlichen Umgebungsinformationen zur Konfiguration und speichern Sie keine echten PASSWÖRTER in öffentlichen Dokumenten oder Screenshots. 
> 

# 2. Erweiterte Konfiguration eingeben 
Im Schritt „Konfiguration“ des Installers, nach Abschluss der Einrichtung von Netzwerk-, Zielumgebung- und Knoteninformationen, erweitern Sie den Abschnitt „Erweiterte Konfiguration“ am unteren Rand der Seite. 

# 3. Installation der eingebauten Komponenten abbrechen Redis
Deaktivieren Sie im Abschnitt „Middleware-Dienste“ Redis

Nach dem Abwählen installiert der Installer den eingebauten Redisund verwenden stattdessen die vorbereitete externe Redis Ob andere Middleware eingebaute Dienste verwendet, sollte entsprechend dem tatsächlichen Bereitstellungsplan gewählt werden.

# 4. Konfiguration von Drittanbieter-Middleware öffnen
Klicken Sie im Bereich 'Drittanbieter-Middleware' auf 'Konfigurieren'.

# 5. Konfigurieren Redis Cache-Middleware
## Redis Server Einzelknoten
1. Wählen Sie 'Redis Cache auf der linken Seite.
2. Aktivieren Sie "Drittanbieter-Nachrichtenwarteschlange verwenden". Redis".
3. Klicken Sie auf "Einzelknoten"
4. Host, Port eingeben, PASSWORD
5. Überprüfen und speichern

## Redis Server Sentinel-Cluster
1. Wählen Sie auf der linken Seite 'Cache'.Redis Cache auswählen
2. "Verwendung von Drittanbieter" aktivieren Redis'.
3. Klicken Sie auf 'Sentinel-Cluster'.
4. Geben Sie 'Master-Name, Knoten' ein. SENTINEL PASSWORD, SENTINEL Knoten
5. Überprüfen und speichern

# 6. Bestätigen Sie die Überprüfungsergebnisse
Der Installer wird Folgendes überprüfen:
- Login: Das Konto kann normal authentifiziert werden
- Konnektivität: Die Bereitstellungsumgebung kann darauf zugreifen Redis
- Berechtigung: Das Konto hat Berechtigungen für Verbindung, Authentifizierung, Befehlsausführung, Veröffentlichung/Abonnement usw.

Nachdem alle Überprüfungspunkte ‚Erfolg‘ anzeigen, schließen Sie das Konfigurationsfenster und kehren zur ‚Konfiguration‘-Seite des Installers zurück.

Wenn es Fehler gibt, überprüfen Sie bitte nach den Anweisungen auf der Seite:
- Ob Host und Port korrekt ausgefüllt sind.
- Ob das Netzwerk zwischen dem Bereitstellungsknoten und dem Redis Server verbunden ist.
- Ob USERNAME und PASSWORD sind korrekt.
- Ob das Konto die erforderlichen Berechtigungen hat (Verbindung und Authentifizierung, Befehlsberechtigungen, Veröffentlichungs-/Abonnementberechtigungen usw.).

# 7. Fortfahren mit der Initialisierung der Bereitstellung
Nach der Rückkehr zur 'Konfiguration'-Seite sicherstellen Redis bleibt unbeaufsichtigt, klicken Sie dann auf 'Bereitstellung initialisieren', um mit der Übersicht, Überprüfung und den Ausführungsschritten der Bereitstellung fortzufahren.

> [!TIP]
>
> Bitte bestätigen Sie vor der Initialisierung der Bereitstellung noch einmal, dass die Redis Die Konfiguration wurde gespeichert und alle Überprüfungspunkte wurden bestanden.
