# System-Upgrade

[← ShimoDocs Suite Bereitstellungsdokumentation](../../../README.md)

## Funktionsübersicht

Das System-Upgrade wird verwendet, um eine neue hochzuladen und anzuwenden MDP Installationspaket. Vor dem offiziellen Upgrade prüft das System automatisch das Upgrade-Paket und die aktuelle Umgebung und zeigt die Konfigurations- und Dienständerungen an, die in diesem Upgrade enthalten sind, um Ihnen zu helfen, das Versionsupgrade oder die routinemäßige Wartung abzuschließen.

Die Seite behält auch den Upgrade-Verlauf bei, sodass es einfach ist, frühere Upgrade-Aufzeichnungen, Ausführungsstatus und zugehörige Protokolle einzusehen.

**Hinweis**: Hauptversions-Upgrades können das Datenbankschema aktualisieren. Das Upgrade umfasst Konfigurationsänderungen, Neustarts von Diensten und Änderungen der funktionalen Schnittstellen, die die Benutzererfahrung beeinträchtigen können. Es sollte außerhalb der Geschäftszeiten durchgeführt werden.

## Seite aufrufen

Nach dem Einloggen in das Verwaltungs-Backend wählen Sie **System-Upgrade** in der linken Navigation, um die Seite zu betreten.

Systemaktualisierungen sind nur für Administratoren verfügbar. Wenn Sie dieses Menü nicht sehen, wenden Sie sich bitte an Ihren Systemadministrator, um die aktuellen Kontoberechtigungen zu bestätigen.

## Vorbereitungen vor der Aktualisierung

Bevor Sie mit der Aktualisierung beginnen, wird empfohlen, die folgenden Punkte zu überprüfen:

- Verwenden Sie das offiziell bereitgestellte Upgrade-Paket, das zum aktuellen Produkttyp und Bereitstellungsmethode passt.
- Das Upgrade-Paket befindet sich in `.tar.gz` Format. Bitte extrahieren oder ändern Sie die Dateien innerhalb des Pakets nicht.
- Es wird empfohlen, das Upgrade während der Geschäfts-Nebenzeiten oder Wartungsfenster durchzuführen.
- Stellen Sie sicher, dass der aktuelle Service normal läuft, und benachrichtigen Sie die relevanten Benutzer im Voraus.
- Wenn das Upgrade eine Lizenzänderung beinhaltet, bereiten Sie bitte den neuen Lizenzinhalt im Voraus vor.

## Upgrade-Schritte

### 1. Upgrade-Paket hochladen

Klicken Sie auf den Upload-Bereich auf der System-Upgrade-Seite oder ziehen Sie das Upgrade-Paket in den Upload-Bereich. Nach Abschluss des Uploads wird das System das Upgrade-Paket automatisch analysieren und überprüfen.

Die Überprüfung umfasst hauptsächlich:

- Das Format und die Inhaltsintegrität des Installationspakets.
- Ob die Signatur des Installationspakets gültig ist.
- Die Version des Upgrade-Pakets und den Upgrade-Plan.
- Ob der Produkttyp und die Bereitstellungsarchitektur übereinstimmen.
- Ob die aktuelle Lizenz dieses Upgrade unterstützt.

Die Überprüfungsergebnisse werden in die folgenden Status unterteilt:

- **Bestanden**: Überprüfung ist normal und Sie können fortfahren.
- **Änderung**: In diesem Upgrade gibt es erwartete Änderungen; bitte bestätigen Sie den Inhalt, bevor Sie fortfahren.
- **Nicht übereinstimmend**: Es gibt Probleme, die das Upgrade verhindern; Sie müssen das Upgrade-Paket ersetzen oder die entsprechende Konfiguration bearbeiten, bevor Sie erneut hochladen.

### 2. Lizenz eingeben

Wenn das System feststellt, dass dieses Upgrade eine aktualisierte Lizenz benötigt, wird die Seite die Produkte anzeigen, die aktualisiert werden müssen, sowie Informationen wie den aktuellen Server-Maschinen-Code.

Nachdem Sie den neuen Lizenzinhalt eingefügt haben, klicken Sie auf **Überprüfen und vorläufig speichern**. Das Upgrade kann nur fortgesetzt werden, wenn die Lizenz erfolgreich überprüft wurde. Die vorübergehend gespeicherte Lizenz wird automatisch wirksam, nachdem das Update erfolgreich angewendet wurde. Für Lizenzreferenzen siehe [Lizenzverwaltung]

Wenn die Seite anzeigt, dass die Lizenz nicht aktualisiert werden muss, können Sie direkt zum nächsten Schritt übergehen.

### 3. Bestätigen Sie den Inhalt des Upgrade-Pakets

Die Seite zeigt die Konfigurationsdateien und Dienstressourcen im Upgrade-Paket an. Sie können bestimmte Dateien auswählen, um deren Inhalt zu prüfen, und bestätigen, dass das Upgrade-Paket mit dem aktuellen Upgradestandziel übereinstimmt.

### 4. Änderungen bestätigen

Das System wird die aktuelle Umgebung mit dem Upgrade-Paket vergleichen und die Ressourcen anzeigen, die in diesem Upgrade hinzugefügt, geändert, gelöscht oder neu gestartet werden.

Bitte achten Sie besonders darauf zu bestätigen: 

- Ob unerwartete Ressourcendeletierungen vorliegen. 
- Ob wichtige Dienste neugestartet werden müssen. 
- Ob die Änderungen in den Konfigurationsdateien wie erwartet sind. 

### 5. Update anwenden 

Nachdem Sie die oben genannten Informationen bestätigt haben, klicken Sie auf **Bestätigen, um das Update zu starten**. Das System erstellt einen Snapshot vor dem Upgrade und beginnt mit der Anwendung des Update-Pakets. 

Während des Upgrades zeigt die Seite kontinuierlich die Ausführungsprotokolle an, einschließlich Informationen zu Ressourcenaktualisierungen, Neustarts von Diensten und Statusprüfungen. Wenn einige Komponenten neu starten, kann die Verwaltungsseite vorübergehend nicht zugänglich sein. Bitte warten Sie einen Moment und öffnen Sie die Seite erneut, um den Fortschritt zu überprüfen. 

Wenn die Ausführung fehlschlägt, können Sie die Probleme anhand der Protokolle beheben und dann auf **Erneut anwenden**. 

### 6. Upgrade abschließen 

Nachdem die Upgrade-Aufgabe erfolgreich ausgeführt wurde, klicken Sie auf **Fertigstellen** um diesen Upgrade-Vorgang abzuschließen. 

Die Seite zum Abschluss des Upgrades zeigt den Namen und die Version des Upgrade-Pakets an und bietet die folgenden Optionen: 

- **Ausführungsprotokoll anzeigen**: Zeigt den vollständigen Ablauf dieses Upgrades an. 
- **Zurück zur Vor-Upgrade-Version**: Geben Sie den Snapshot vor dem Upgrade ein und folgen Sie den Seitenanweisungen, um ein Zurücksetzen durchzuführen. 
- **Zurück zu Anwendung aktualisieren**: Zur Startseite des Systemupgrades zurückkehren. 

## Upgrade-Verlauf 

Am unteren Rand der Startseite des Systemupgrades wird die Upgrade-Historie angezeigt, einschließlich des Namens des Upgrade-Pakets, der Version, der Erstellungszeit und des Ausführungsstatus. 

Klicken Sie auf den Upgrade-Eintrag, um den entsprechenden Vorgang erneut aufzurufen und den Upgrade-Fortschritt oder historische Ausführungsergebnisse anzuzeigen.

## Häufige Situationen

- **Verifizierung des Upgrade-Pakets fehlgeschlagen**: Bitte bestätigen Sie die Quelle des Upgrade-Pakets, die Dateiintegrität, den Produkttyp und die Bereitstellungsarchitektur.
- **Versionsabweichung**: Bitte überprüfen Sie die aktuelle Systemversion und die Version des Upgrade-Pakets, um sicherzustellen, dass der richtige Upgrade-Pfad verwendet wird.
- **Lizenz-Update erforderlich**: Beschaffen Sie eine neue Lizenz, die mit der Zielversion und der aktuellen Laufzeitumgebung kompatibel ist, validieren Sie sie und speichern Sie sie vorübergehend, bevor Sie fortfahren.
- **Seite des Upgrade-Vorgangs vorübergehend nicht verfügbar**: Der MDP Dienst wird möglicherweise aktualisiert oder neu gestartet. Bitte warten Sie einen Moment und aktualisieren Sie die Seite.
- **Upgrade-Aufgabe fehlgeschlagen**: Überprüfen Sie die Ausführungsprotokolle, um die Ursache zu identifizieren; nachdem das Problem behoben ist, verwenden Sie **Erneut anwenden**.
- **Dienstanomalie nach Upgrade**: Überprüfen Sie zunächst die Ausführungsprotokolle und den Status des Dienstes; wenn eine Wiederherstellung erforderlich ist, können Sie mithilfe eines Snapshots vor dem Upgrade zurückrollen.

> System-Upgrades ändern die Dienstkonfigurationen und können Dienstneustarts auslösen. Bitte fahren Sie nur fort, nachdem Sie das Upgrade-Paket und die Änderungen überprüft haben.
