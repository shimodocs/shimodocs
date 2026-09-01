# Kafka Werkzeuge

[← ShimoDocs Suite Bereitstellungsdokumentation](../../../README.md)

> [!TIP]
>
> Der Kafka Tool ermöglicht Ihnen: Kafka Cluster-Status, Themen, Nachrichten, Consumer-Gruppen und Partitionsinformationen über die Redpanda-Konsole anzuzeigen, häufig verwendet zur Fehlerbehebung bei Nachrichtenschreibungen, Consumer-Rückständen und asynchronen Verbindungsproblemen.
>
> Sobald die Seite erfolgreich geladen ist, wird die Redpanda-Konsole eingebettet sein MDP.

## 1. Zugriff auf Kafka

1. Melden Sie sich bei der **MDP Operations-Plattform**.
2. Auswählen **Systemdienste** oben.
3. Erweitern **Middleware-Tools** in der linken Navigationsleiste.
4. Auswählen **Kafka**.
5. Warten Sie, bis die Redpanda-Konsole das Laden abgeschlossen hat.

Wenn die Konsole nicht bereit ist, zeigt die Seite an, dass sie gestartet wird oder dass der Start fehlgeschlagen ist, und zeigt Fehlermeldungen an.

## 2. Cluster Übersicht anzeigen

Nach dem Betreten Kafka, **Übersicht** wird standardmäßig angezeigt.

Sie können die folgenden Informationen anzeigen:

| Informationen | Beschreibung |
| --- | --- |
| Cluster-Status | Betriebszustand des Clusters. |
| Cluster-Speichergröße | Aktuelle Cluster-Speichergröße. |
| Cluster-Version | Cluster-Versionsinformationen. |
| Online-Broker | Anzahl der Online-Broker. |
| Themen | Anzahl der Themen. |
| Replikate | Anzahl der Replikate. |
| Broker-Details | Broker-ID, Status und Größe. |

## 3. Themen anzeigen

1. Wählen Sie in der linken Navigation der Redpanda-Konsole aus **Themen**.
2. Finden Sie das Zielthema in der Themenliste.
3. Klicken Sie auf das Thema, um die Detailseite aufzurufen.
4. Sehen Sie Informationen wie die Partitionen des Themas, Nachrichten und Konfiguration ein.

Die Fehlersuche von Themen konzentriert sich normalerweise auf:

| Informationen | Beschreibung |
| --- | --- |
| Partitionen | Den Partitionstatus des Themas. |
| Nachrichten | Die Liste der Nachrichten im Thema. |
| Konfiguration | Themenkonfiguration, wie z. B. Aufbewahrungsrichtlinie. |

## 4. Nachrichten anzeigen

1. Rufen Sie das Zielthema auf.
2. Öffnen Sie den Nachrichtenanzeigebereich.
3. Wählen Sie Partition, Position oder Zeitbereich mithilfe der auf der Seite bereitgestellten Filter aus.
4. Sehen Sie Nachrichtenschlüssel, -wert, Header, Partition, Offset und Zeitstempel ein.

> Der Nachrichteninhalt kann Geschäftsfelder enthalten. Bei der Fehlersuche hat die Suche nach Geschäfts-ID, Schlüssel, Offset und Zeitstempel Vorrang.

## 5. Verbrauchergruppen anzeigen

1. Wählen Sie in der Redpanda-Konsole aus **Verbrauchergruppen** aus der linken Navigation.
2. Suchen Sie die Ziel-Consumer-Gruppe oder wählen Sie sie aus.
3. Geben Sie die Details der Consumer-Gruppe ein.
4. Sehen Sie sich die zugehörigen Topics, Partitionen, den aktuellen Offset, den Log-End-Offset und den Lag der Consumer-Gruppe an.

## 6. Bestimmung des Consumer-Rückstands

| Status | Beschreibung |
| --- | --- |
| Lag ist 0 | Die aktuelle Consumer-Gruppe hat keinen Rückstand. |
| Lag nimmt kontinuierlich zu | Die Verbrauchsgeschwindigkeit ist niedriger als die Produktionsgeschwindigkeit. |
| Lag ändert sich nicht, ist aber nicht 0 | Es könnte ein gestoppter Consumer, eine Partitionsblockade oder ein Verbrauchsfehler vorliegen. |
| Lag einer einzelnen Partition ist deutlich hoch | Dies kann auf einen Hot Key oder einen abnormalen Verbrauch in dieser Partition zurückzuführen sein. |

## 7. Broker anzeigen

1. Gehen Sie zur Übersichtsseite und finden Sie **Broker-Details**.
2. Überprüfen Sie die Broker-ID, den Betriebsstatus und die Speicherkapazität.
3. Klicken Sie **Anzeigen** um die Broker-Details zu sehen.

## 8. Häufige Fehlerszenarien

| Szenario | Betriebliche Vorschläge |
| --- | --- |
| Bestätigen Sie, ob Kafka normal funktioniert | Überprüfen Sie den Cluster-Status und die online befindlichen Broker in der Übersicht. |
| Bestätigen Sie, ob Nachrichten geschrieben werden | Gehen Sie zum Topic und überprüfen Sie die Nachrichten. |
| Verzögerungen beim Consumer beheben | Gehen Sie zu den Consumer-Gruppen und überprüfen Sie den Lag. |
| Eine einzelne Nachricht finden | Suchen Sie nach Topic, Partition, Offset, Key oder Zeitstempel. |
| Bestätigen Sie die Topic-Konfiguration | Gehen Sie zu den Topic-Details und überprüfen Sie die Konfiguration. |
