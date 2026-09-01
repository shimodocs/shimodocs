# Bereitstellen mit MySQL 8

[← ShimoDocs Suite Bereitstellungsdokumentation](../../README.md)

Dieser Artikel erklärt, wie der eingebaute MySQL im ShimoDocs Installer und konfigurieren Sie Ihre eigene MySQL als eine relationale Datenbank eines Drittanbieters. Nach der Konfiguration überprüft der Installer die Datenbankanmeldung, die Netzwerkverbindung und die Berechtigungen zur Tabellenerstellung. Nach der Überprüfung kann die Bereitstellung fortgesetzt werden. 

# 1. Vorbereitung vor der Konfiguration 
Bitte bestätigen Sie vor Beginn: 
- MySQL 8.0 ist installiert und läuft normal. 
- Der Bereitstellungsknoten kann auf den MySQL Datenbankhost und Port. 
- Datenbankhost, Port, USERNAMEund PASSWORD sind vorbereitet. 
- Das Datenbankkonto hat die Berechtigung, sich anzumelden, eine Verbindung herzustellen, Tabellen zu erstellen und Tabellen zu löschen. 

> [!TIP]
>
> Die IP, der Port und das Konto in diesem Artikel sind Beispiele. Bitte konfigurieren Sie anhand Ihrer tatsächlichen Umgebungsinformationen; speichern Sie keine echten PASSWORD Informationen in externen Dokumenten oder Screenshots. 
>

# 2. Erweiterte Konfiguration eingeben 
Im „Konfiguration“-Schritt des Installers, nach Abschluss der Netzwerkkonfiguration, Zielumgebung und Knoteninformationen, erweitern Sie den Abschnitt „Erweiterte Konfiguration“ am unteren Rand der Seite. 

# 3. Abbrechen der Installation von eingebauten MySQL
im Bereich 'Middleware-Services' abwählen MySQL.

Nach dem Abwählen installiert der Installer den eingebauten MySQLnicht mehr, und ein externer MySQL Die bereits vorbereitete 8.0 wird später verwendet. Ob andere Middleware integrierte Dienste nutzt, sollte entsprechend dem tatsächlichen Bereitstellungsplan gewählt werden.

# 4. Konfiguration von Drittanbieter-Middleware öffnen
Im Abschnitt „Drittanbieter-Middleware“ auf „Konfigurieren“ klicken.

# 5. Konfigurieren MySQL Datenbank
1. Wählen Sie 'RDB Relationale Datenbank' links aus.
2. Aktivieren Sie „Drittanbieter-relationale Datenbank verwenden“.
3. Standard auswählen MySQL unter „Dialect“.
4. Geben Sie die Datenbank-Verbindungsinformationen ein.
5. Überprüfen und speichern.

# 6. Bestätigen Sie die Überprüfungsergebnisse
Der Installer wird Folgendes überprüfen:

- Anmeldung: Ob das Datenbankkonto sich normal anmelden kann.
- Konnektivität: Ob die Bereitstellungsumgebung auf die Datenbank zugreifen kann.
- Berechtigung zur Tabellenerstellung: Ob das Datenbankkonto die Berechtigung hat, Tabellen zu erstellen und zu löschen.

Nachdem alle Überprüfungspunkte ‚Erfolg‘ anzeigen, schließen Sie das Konfigurationsfenster und kehren zur ‚Konfiguration‘-Seite des Installers zurück.

Wenn es Fehler gibt, überprüfen Sie bitte nach den Anweisungen auf der Seite:
- Ob Host und Port korrekt ausgefüllt sind.
- Ob das Netzwerk zwischen den Bereitstellungsknoten und der Datenbank verbunden ist.
- Ob USERNAME und PASSWORD sind korrekt.
- Ob das Datenbankkonto die erforderlichen Berechtigungen hat.

# 7. Fortfahren mit der Initialisierung der Bereitstellung
Nach der Rückkehr zur 'Konfiguration'-Seite sicherstellen MySQL bleibt ungeprüft, dann klicken Sie auf 'Bereitstellung initialisieren', um die Übersicht, Prüfung und Ausführung der Bereitstellungsschritte weiter abzuschließen.

> [!TIP]
>
> Bitte bestätigen Sie vor der Initialisierung der Bereitstellung noch einmal, dass die MySQL 8.0-Konfiguration gespeichert wurde und dass alle Validierungspunkte bestanden wurden.
