# Bereitstellen mit Kafka

[← ShimoDocs Suite Bereitstellungsdokumentation](../../README.md)

Dieser Artikel erklärt, wie der eingebaute Kafka im ShimoDocs Installer deaktiviert und der eigene Kafka des Kunden als Drittanbieter-Nachrichtenwarteschlange konfiguriert wird. Nach der Konfiguration überprüft der Installer die KafkaNetzwerkkonnektivität und die Berechtigung zur Erstellung von Themen. Nach der Überprüfung kann die Bereitstellung fortgesetzt werden. 

# 1. Vorbereitungen vor der Konfiguration 
Bitte bestätigen Sie vor Beginn: 
- Kafka Der Server ist installiert und läuft normal. 
- Der Bereitstellungsknoten kann auf den Kafka Server-Host und -Port zugreifen. 
- Vorbereitete Authentifizierungsbenutzerinformationen und PASSWORD für die Verbindung zum Kafka Server-Thema (falls der externe Kafka Cluster die sichere Authentifizierung aktiviert hat). 
- Das authentifizierte Konto muss einen Administratorbenutzer verwenden und Berechtigungen zum Erstellen, Löschen, Autorisieren sowie zum Lesen und Schreiben von Themen haben (falls der externe Kafka Cluster die sichere Authentifizierung aktiviert hat). 

> [!TIP]
>
> Die IP, der Port und das Konto in diesem Artikel sind Beispiele. Bitte konfigurieren Sie anhand Ihrer tatsächlichen Umgebungsinformationen; speichern Sie keine echten PASSWORD Informationen in externen Dokumenten oder Screenshots. 
>

# 2. Erweiterte Konfiguration eingeben 
Im „Konfiguration“-Schritt des Installers, nach Abschluss der Netzwerkkonfiguration, Zielumgebung und Knoteninformationen, erweitern Sie den Abschnitt „Erweiterte Konfiguration“ am unteren Rand der Seite. 

# 3. Installation des eingebauten Kafka
im Bereich 'Middleware-Services' abwählen Kafka.

Nach dem Abwählen installiert der Installer den eingebauten Kafkanicht mehr, und ein externer Kafka Das vorbereitete wird später verwendet. Für andere Middleware sollte die Entscheidung, ob integrierte Dienste verwendet werden, nach dem tatsächlichen Bereitstellungsplan getroffen werden.

# 4. Konfiguration von Drittanbieter-Middleware öffnen
Klicken Sie im Bereich "Drittanbieter-Middleware" auf "Konfigurieren".

# 5. Konfigurieren Kafka Messaging-Middleware
## Kafka Server SASL Authentifizierung nicht aktiviert
1. Wählen Sie 'Kafka Nachrichtenwarteschlange" auf der linken Seite.
2. Aktivieren Sie "Drittanbieter-Nachrichtenwarteschlange verwenden". Kafka Nachrichtenwarteschlange verwenden".
3. Füllen Sie die Kafka Server-Verbindungsinformationen aus.
5. Überprüfen und speichern

## Aktivierung SASL der Authentifizierung auf Kafka Server 
Wenn Kafka dem Server hat SASL Authentifizierung aktiviert, muss gleichzeitig in der Web-Oberfläche aktiviert werden: Nur aktivieren, wenn der Broker authentifizierten Zugriff erfordert Schaltfläche 
1. Aktivieren SASL Authentifizierung 
2. Überprüfungsmechanismus 
3. Eingeben USERNAME und PASSWORD 
4. Überprüfen und speichern 

# 6. Bestätigen Sie die Überprüfungsergebnisse
Der Installer wird Folgendes überprüfen:
- Login: Das Konto kann normal authentifiziert werden (falls SASL aktiviert).
- Konnektivität: Die Bereitstellungsumgebung kann darauf zugreifen Kafka.
- Berechtigung zum Erstellen von Topics: Das Konto hat Berechtigungen zum Erstellen von Topics, Autorisieren sowie Lesen/Schreiben.

Nachdem alle Überprüfungspunkte ‚Erfolg‘ anzeigen, schließen Sie das Konfigurationsfenster und kehren zur ‚Konfiguration‘-Seite des Installers zurück.

Wenn es Fehler gibt, überprüfen Sie bitte nach den Anweisungen auf der Seite:
- Ob Host und Port korrekt ausgefüllt sind.
- Ob das Netzwerk zwischen dem Bereitstellungsknoten und dem Kafka Server verbunden ist.
- Ob USERNAME und PASSWORD sind korrekt (Kafka Server hat aktiviert SASL Authentifizierung).
- Ob das Konto die erforderlichen Berechtigungen hat (Kafka dem Server hat SASL Authentifizierung aktiviert).

# 7. Fortfahren mit der Initialisierung der Bereitstellung
Nach der Rückkehr zur 'Konfiguration'-Seite sicherstellen Kafka bleibt unbeaufsichtigt, klicken Sie dann auf 'Bereitstellung initialisieren', um mit der Übersicht, Überprüfung und den Ausführungsschritten der Bereitstellung fortzufahren.

> [!TIP]
>
> Bevor Sie die Bereitstellung initialisieren, bestätigen Sie bitte noch einmal, dass die Kafka Konfiguration gespeichert wurde und alle Prüfposten bestanden sind.
