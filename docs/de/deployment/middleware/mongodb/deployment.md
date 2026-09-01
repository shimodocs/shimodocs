# Bereitstellen mit MongoDB

[← ShimoDocs Suite Bereitstellungsdokumentation](../../README.md)

Dieser Artikel erklärt, wie man die eingebaute MongoDB im ShimoDocs Installer deaktiviert und der eigene MongoDB als Drittanbieter MongoDB Dokumenten-Datenbank deaktiviert. Nach der Konfiguration überprüft der Installer MongoDB Netzwerkverbindung und Berechtigungen wie Verbindung und Authentifizierung. Sobald die Überprüfungen bestanden sind, kann die Bereitstellung fortgesetzt werden. 

# 1. Vorbereitungen vor der Konfiguration 
Bitte bestätigen Sie vor Beginn: 
- MongoDB Der Server ist installiert und läuft normal. 
- Der Bereitstellungsknoten kann auf Host und Port der MongoDB Server. 
- Verbindungsinformationen und PASSWORD zum Authentifizieren mit dem MongoDB Server sind vorbereitet. 

> [!TIP] 
> 
> Die IP, der Port und das Konto in diesem Artikel sind Beispiele. Bitte verwenden Sie für die Konfiguration die tatsächlichen Umgebungsinformationen und speichern Sie die echten PASSWORD nicht in öffentlichen Dokumenten oder Screenshots. 
> 

# 2. Erweiterte Konfiguration eingeben 
Im Schritt „Konfiguration“ des Installationsprogramms, nachdem die Netzwerk-, Zielumgebungs- und Knoteninformationen konfiguriert wurden, erweitern Sie am unteren Rand der Seite die „Erweiterte Konfiguration“. 

# 3. Abbrechen der Installation von eingebauten MongoDB
im Bereich 'Middleware-Services' abwählen MongoDB

Nach dem Abwählen installiert der Installer den eingebauten MongoDB, und stattdessen eine extern vorbereitete MongoDB verwenden. Für andere Middleware sollte die Entscheidung, ob eingebaute Dienste verwendet werden, nach dem tatsächlichen Bereitstellungsplan getroffen werden.

# 4. Konfiguration von Drittanbieter-Middleware öffnen
Klicken Sie im Bereich "Drittanbieter-Middleware" auf "Konfigurieren".

# 5. Konfigurieren MongoDB Dokumenten-Datenbank
1. Wählen Sie 'MongoDB Dokumenten-Datenbank“ auf der linken Seite.
2. Aktivieren Sie "Drittanbieter-Nachrichtenwarteschlange verwenden". MongoDB Dokumenten-Datenbank.
3. Host, Port eingeben, USERNAME, PASSWORD, Verbindungszeichenfolgenüberschreibung
4. Überprüfen und speichern

> [!WARNING]
>
> Achtung: Wenn ein Drittanbieter MongoDB ein spezielles Konto für die Anwendung erstellt und dem 'Prinzip der minimalen Rechte' folgt, bei dem ein Konto nur Berechtigungen für den Zugriff auf eine bestimmte Datenbank hat, ist es notwendig, einen Benutzer und PASSWORD für jede Geschäftsdatenbank zuzuweisen

# 6. Bestätigen Sie die Überprüfungsergebnisse
Der Installer wird Folgendes überprüfen:
- Login: Das Konto kann normal authentifiziert werden
- Konnektivität: Die Bereitstellungsumgebung kann darauf zugreifen MongoDB
- Berechtigung: Das Konto hat Berechtigungen für Verbindung, Authentifizierung, Befehlsausführung usw.

Nachdem alle Überprüfungspunkte ‚Erfolg‘ anzeigen, schließen Sie das Konfigurationsfenster und kehren zur ‚Konfiguration‘-Seite des Installers zurück.

Wenn es Fehler gibt, überprüfen Sie bitte nach den Anweisungen auf der Seite:
- Ob Host und Port korrekt ausgefüllt sind.
- Ob das Netzwerk zwischen dem Bereitstellungsknoten und dem MongoDB Server ist verbunden.
- Ob USERNAME und PASSWORD sind korrekt.
- Ob das Konto die erforderlichen Berechtigungen hat (Verbindung und Authentifizierung, Befehlsberechtigungen usw.).

# 7. Fortfahren mit der Initialisierung der Bereitstellung
Nach der Rückkehr zur 'Konfiguration'-Seite sicherstellen MongoDB bleibt ungeprüft, dann klicken Sie auf 'Bereitstellung initialisieren', um die Übersicht der Bereitstellung, Überprüfungen und Ausführungsschritte weiter abzuschließen.

> [!TIP]
>
> Bevor Sie die Bereitstellung initialisieren, bestätigen Sie bitte noch einmal, dass die MongoDB Konfiguration wurde gespeichert und alle Validierungspunkte wurden bestanden.
