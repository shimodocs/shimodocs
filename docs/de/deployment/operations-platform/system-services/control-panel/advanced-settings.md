# Erweiterte Einstellungen

[← ShimoDocs Suite Bereitstellungsdokumentation](../../../README.md)

## Funktionsübersicht

Erweiterte Einstellungen werden verwendet, um die benutzerdefinierten Systemeinstellungen zu verwalten `pd-config` direkt durch YAML. Dies eignet sich für die Handhabung fortschrittlicher Parameter, die auf der Standard-Einstellungsseite nicht bereitgestellt werden, oder für Massenkonfigurationen.

Das System wird benutzerdefinierte Konfigurationen mit den werkseitigen Standardkonfigurationen zusammenführen. Benutzerdefinierte Werte am gleichen Pfad überschreiben die Standardwerte, während nicht ausgefüllte Konfigurationen weiterhin die werkseitigen Standardwerte verwenden.

## Aufrufen der Seite

Nach dem Einloggen in das Verwaltungs-Backend wählen Sie **Erweiterte Einstellungen** über die Navigation auf der linken Seite, um auf die Seite zuzugreifen.

Erweiterte Einstellungen sind nur für Administratoren verfügbar. Diese Seite kann das gesamte System beeinflussen, daher sollte sie nur von Personal bedient werden, das mit der MDP Konfigurationsstruktur vertraut ist.

## Seitenbeschreibung

Die Seite ist in zwei Teile unterteilt:

- **Werkseitige Standard-pd-config**: Die vom Installationspaket bereitgestellte Standardkonfiguration, nur lesbar.
- **Benutzerdefinierte pd-config**: Die derzeit gespeicherte kundenspezifische Konfiguration, die bearbeitet werden kann.

Die benutzerdefinierte Konfiguration muss nicht den gesamten Standardinhalt kopieren; in der Regel werden nur die Konfigurationselemente beibehalten, die überschrieben oder hinzugefügt werden müssen.

## Konfiguration bearbeiten und veröffentlichen

Es wird empfohlen, folgende Schritte zu befolgen:

1. Klicken Sie **Aktualisieren** um sicherzustellen, dass die neueste benutzerdefinierte Konfiguration geladen wird.
2. Vergleichen Sie mit der werkseitigen Standardkonfiguration auf der linken Seite und bearbeiten Sie den YAML Inhalt auf der rechten Seite.
3. Klicken Sie **Veröffentlichen**.
4. Überprüfen Sie den hinzugefügten, gelöschten und geänderten Inhalt im Fenster zur Differenzbestätigung.
5. Verwenden Sie die Tasten „Vorherige/Nächste Differenz“, um die Änderungen Punkt für Punkt zu überprüfen.
6. Nach Bestätigung, dass keine Fehler vorliegen, klicken Sie **Bestätigen Sie die Veröffentlichung**.

Nach einer erfolgreichen Veröffentlichung erstellt das System eine Konfigurationsanwendungsaufgabe und öffnet das Aufgabenprotokoll in einem neuen Fenster. Abhängig von den Änderungen und den Systemeinstellungen können verwandte Dienste automatisch neu gestartet werden.

## Konfigurationshistorie

Klicken Sie **Historie** um zuvor veröffentlichte benutzerdefinierte Konfigurationen anzuzeigen, einschließlich Datensatz-ID, Erstellungzeit und MD5.

- Klicken Sie **Anzeigen** um die vollständige YAML einer historischen Version zu sehen.
- Nach der Auswahl von zwei Datensätzen können Sie einen Unterschiedsvergleich durchführen.

Die aktuelle Seite bietet keine Ein-Klick-Wiederherstellungs-Schaltfläche. Um historische Konfigurationen wiederherzustellen, prüfen Sie bitte die entsprechende Version, verifizieren den Inhalt, kopieren ihn manuell in den Bearbeitungsbereich und veröffentlichen erneut.

## Hinweise

- YAML Die Syntax muss korrekt bleiben; achten Sie auf Einrückungen, Doppelpunkte und Datentypen.
- Löschen Sie Konfigurationselemente, die Sie nicht verstehen, nicht willkürlich.
- Überprüfen Sie vor der Veröffentlichung die Unterschiede vollständig, um zu vermeiden, dass kürzlich von anderen Administratoren eingereichte Änderungen überschrieben werden.
- Wichtige Änderungen sollten nach Möglichkeit außerhalb der Geschäftszeiten durchgeführt und die ursprüngliche Konfiguration im Voraus aufgezeichnet werden.
- Nach der Veröffentlichung überprüfen Sie das Aufgabenprotokoll, um sicherzustellen, dass die Konfigurationsanwendung und die Überprüfung des Dienststatus abgeschlossen sind.

## Häufige Situationen

- **Veröffentlichung fehlgeschlagen**: Bitte überprüfen Sie YAML Format, Feldnamen und Konfigurationswerttypen.
- **Dienstneustart nach Veröffentlichung**: Konfigurationsänderungen können einen Neustart der zugehörigen Dienste erfordern, dies ist normal.
- **Seite vorübergehend nach Veröffentlichung nicht erreichbar**: MDP oder verwandte Dienste werden möglicherweise neu gestartet; bitte nach kurzer Wartezeit aktualisieren.
- **Die Konfiguration erreicht nicht die erwartete Wirkung**: Bitte bestätigen Sie, dass der Konfigurationspfad korrekt ist, und überprüfen Sie das endgültige zusammengeführte Ergebnis sowie das Aufgabenprotokoll.
- **Falsche Konfigurationsänderung**: Finden Sie die korrekte Version in der Historie, kopieren Sie den Inhalt und veröffentlichen Sie ihn erneut.

> Erweiterte Einstellungen wirken sich auf Systemkonfigurationen und Dienstantreibungen aus. Veröffentlichen Sie unüberprüfte Konfigurationen nicht direkt in der Produktionsumgebung.

## Beispiel für die Bedienoberfläche

Die Abbildung unten zeigt die Vergleichsoberfläche zwischen Werksstandardkonfiguration und benutzerdefinierter Konfiguration.

