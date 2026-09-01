# Bereitstellen mit Dameng V8

[← ShimoDocs Suite Bereitstellungsdokumentation](../../README.md)

Dieser Artikel erklärt, wie man die integrierte MySQL im ShimoDocs Installer deaktiviert und Dameng DM8 als relationale Datenbank eines Drittanbieters konfiguriert. Nach Abschluss der Konfiguration überprüft der Installer die Datenbankanmeldung, die Netzwerkkonnektivität und die Berechtigungen zur Tabellenerstellung. Sobald die Überprüfungen bestanden sind, kann die Bereitstellung fortgesetzt werden.

## 1. Vorbereitungen vor der Konfiguration

Bitte bestätigen Sie vor dem Start:

- Dameng DM8 ist installiert und läuft normal.
- Der Bereitstellungsknoten kann auf Host und Port der Dameng Datenbank zugreifen.
- Der Datenbank-Host, Port, USERNAMEund PASSWORD sind bereit.
- Das Datenbankkonto hat die Berechtigungen zum Anmelden, Verbinden, Tabellen erstellen und Tabellen löschen.
- Dameng Die Datenbank hat die MySQL Kompatibilitätsmodus-Konfiguration wie erforderlich abgeschlossen. Für detaillierte Anweisungen befolgen Sie bitte ["Dameng Datenbank-Integrationskonfigurationshandbuch.](requirements.md).

> [!TIP]
>
> Die in diesem Artikel angegebenen IP, Port und Konten sind alles Beispiele. Bitte verwenden Sie für die Konfiguration tatsächliche Umgebungsinformationen und nehmen Sie keine echten PASSWORD Angaben in externen Dokumenten oder Screenshots auf.

## 2. Erweiterte Einstellungen eingeben

Im Schritt 'Konfiguration' des Installationsprogramms, nach Abschluss der Netzwerk-, Zielumgebungs- und Knoteninformationen-Konfiguration, erweitern Sie die 'Erweiterte Konfiguration' am Seitenende.

## 3. Installation der integrierten Komponenten abwählen MySQL

Im Bereich 'Middleware-Dienste' deaktivieren Sie **MySQL**.

Nach der Deaktivierung wird das Installationsprogramm die integrierten MySQLnicht mehr installieren und verwendet künftig die vorbereitete Dameng Datenbank. Bei anderer Middleware sollte die Nutzung von integrierten Diensten je nach tatsächlichem Bereitstellungsplan ausgewählt werden.

## 4. Konfiguration von Drittanbieter-Middleware öffnen

Klicken Sie im Bereich 'Drittanbieter-Middleware' auf 'Konfigurieren'.

## 5. Konfigurieren Dameng Datenbank

1. Wählen Sie 'RDB Relationale Datenbank' auf der linken Seite.
2. Aktivieren Sie 'Drittanbieter-relationale Datenbank verwenden.'
3. Wählen Sie im Abschnitt 'Dialekt' **DM (Dameng)**.
4. Geben Sie die Datenbank-Verbindungsinformationen ein.

| Konfigurationselement | Beschreibung |
| --- | --- |
| Host | Die IP-Adresse oder der erreichbare Hostname der Dameng Datenbank |
| Port | Der abgehörte Port der Dameng Datenbank, typischerweise standardmäßig 5236, abhängig von der tatsächlichen Konfiguration |
| USERNAME | Das Konto, das für die Verbindung mit der Datenbank verwendet wird |
| PASSWORD | Die PASSWORD entsprechend dem Datenbankkonto |
| DSN | Automatisch vom Installer basierend auf den obigen Informationen generiert, keine manuelle Eingabe erforderlich |

5. Nachdem Sie bestätigt haben, dass die Informationen korrekt sind, klicken Sie auf "Überprüfen und Speichern."

## 6. Bestätigen Sie die Überprüfungsergebnisse

Der Installer überprüft die folgenden Punkte:

- **Anmeldung**: Das Datenbankkonto kann sich normal anmelden.
- **Konnektivität**: Die Bereitstellungsumgebung kann auf die Datenbank zugreifen.
- **Berechtigung zum Erstellen von Tabellen**: Das Datenbankkonto hat die Berechtigung, Tabellen zu erstellen und zu löschen.

Nachdem alle Prüfungen "Erfolg" anzeigen, schließen Sie das Konfigurationsfenster und kehren Sie zur "Konfiguration"-Seite des Installers zurück.

Wenn Prüfpunkte fehlgeschlagen sind, überprüfen Sie bitte gemäß den Seitenhinweisen:

- Ob Host und Port korrekt ausgefüllt sind.
- Ob das Netzwerk zwischen dem Bereitstellungsknoten und der Datenbank verbunden ist.
- Ob die USERNAME und PASSWORD sind korrekt.
- Ob das Datenbankkonto die erforderlichen Berechtigungen hat.
- Ob die Dameng Datenbankdienst und MySQL Kompatibilitätskonfigurationen haben normal gegriffen.

## 7. Initialisierung der Bereitstellung fortsetzen

Nach der Rückkehr zur "Konfiguration"-Seite bestätigen Sie, dass MySQL nicht markiert bleibt, und klicken Sie auf "Bereitstellung initialisieren", um die Übersicht der Bereitstellung, Prüfungen und Ausführungsschritte weiter abzuschließen.

> [!TIP]
>
> Bevor die Bereitstellung initialisiert wird, bestätigen Sie bitte erneut, dass die Dameng Konfiguration gespeichert wurde und alle Überprüfungspunkte bestanden sind.
