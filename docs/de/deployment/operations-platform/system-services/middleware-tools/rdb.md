# RDB Werkzeuge

[← ShimoDocs Suite Bereitstellungsdokumentation](../../../README.md)

> [!TIP]
>
> RDB wird verwendet, um relationale Datenbankdaten auf der Operationsplattform anzusehen und zu überprüfen, und wird häufig genutzt, um Geschäftsdatensätze, Konfigurationsdatensätze, Aufgabenstatus, Betriebsprotokolle und andere strukturierte Daten zu bestätigen.
>
> Stellen Sie vor der Verwendung sicher, dass Ihr aktuelles Konto Berechtigungen für Middleware-Tools besitzt und dass die richtige Bereitstellungsumgebung ausgewählt ist.

> Der RDB Das Tool greift direkt auf Datenbankdaten zu. Bitte bestätigen Sie vor der Abfrage die Tabellen und Filterbedingungen, um das Ausführen kostenintensiver Abfragen oder eine Fehlbedienung von Produktionsdaten zu vermeiden.

## 1. Zugriff auf RDB

1. Melden Sie sich bei **MDP Operations-Plattform**.
2. Auswählen **Systemdienste** oben.
3. Erweitern **Middleware-Tools** im linken Navigationsbereich.
4. Auswählen **RDB**.

Die Seite enthält normalerweise Bereiche für Datenbankverbindung, Tabellenauswahl, SQL Eingabe und Abfrageergebnisse.

## 2. Auswahl einer Datenbankverbindung

1. Auf der RDB Seite wählen Sie die Datenbankinstanz aus, auf die Sie zugreifen müssen.
2. Stellen Sie sicher, dass der Instanzname, die Datenbankadresse oder die Umgebungskennung mit dem aktuellen Ziel der Fehlerbehebung übereinstimmt.
3. Wählen Sie die Ziel-Datenbank.
4. Erweitern Sie die Tabellenliste und bestätigen Sie, dass die Zieltabelle existiert.

> Wenn die Datenbankinstanz leer ist oder die Verbindung fehlschlägt, überprüfen Sie bitte zuerst die Middleware-Konfiguration, die Kontoberechtigungen und die Netzwerkverbindung.

## 3. Anzeigen der Tabellenstruktur

1. Wählen Sie die Zieltabelle aus der Tabellenliste auf der linken Seite aus.
2. Sehen Sie sich die Feldnamen, Feldtypen, Primärschlüssel und Indexinformationen an.
3. Bestätigen Sie die nachfolgenden Abfragebedingungen basierend auf den Feldbedeutungen.

Es wird empfohlen, sich auf die folgenden Informationen zu konzentrieren:

| Informationen | Beschreibung |
| --- | --- |
| Primärschlüssel | Wird verwendet, um einen einzelnen Datensatz genau abzufragen. |
| Geschäfts-ID | Zum Beispiel Tenant-ID, Benutzer-ID, Aufgaben-ID, Datei-ID. |
| Statusfeld | Wird verwendet, um den aktuellen Ablaufstatus des Geschäfts zu bestätigen. |
| Zeitfeld | Wird verwendet, um den Abfragezeitraum einzuschränken. |
| Indiziertes Feld | Sollte vorzugsweise als Abfragefilter verwendet werden, um Volltabellenscans zu reduzieren. |

## 4. Verwendung von Common SQL

'Common SQL' wird verwendet, um voreingestellte Abfragen schnell auszuführen, geeignet für Szenarien mit hoher Prüfungsfrequenz wie Zertifikate, Anträge, Dateien und Benutzer.

1. Klicken Sie **Common SQL** über dem SQL Editor.
2. Wählen Sie das SQL Sie müssen es aus der Dropdown-Liste verwenden.
3. Wenn Sie zuerst den Inhalt der Anweisung überprüfen müssen, klicken Sie **Vorschau** neben der entsprechenden SQL.
4. Überprüfen Sie die Beschreibung, Datenbank, Tabellenname und SQL Inhalt im Vorschaufenster.
5. Nachdem Sie bestätigt haben, dass alles korrekt ist, klicken Sie **Ausführen**.

Häufig verwendete SQL können Platzhalter enthalten:

| Platzhalter | Parametertyp | Beispiel |
| --- | --- | --- |
| `%s` | String | App-ID, Anbieter-Datei-ID, Verlauf-GUID, Anbieter-Benutzer-ID |
| `%d` | Nummer | Interne Benutzer-ID |

Wenn das SQL Platzhalter enthält, ein **Ausfüllen SQL Parameter** Ein Fenster wird beim Ausführen angezeigt.

1. Füllen Sie jeden Parameter entsprechend der Aufforderung aus.
2. Bei Zeichenkettenparametern geben Sie die vollständige ID ein, ohne zusätzliche Anführungszeichen hinzuzufügen.
3. Füllen Sie numerische Parameter mit reinen Zahlen aus.
4. Klicken Sie **Abfrage ausführen**.

Die derzeit häufig verwendeten SQL umfassen hauptsächlich die folgenden Szenarien:

| Szenario | Beschreibung |
| --- | --- |
| Zertifikatsabfrage | Anwendungszertifikate und Anwendungs-IDs abfragen. |
| Abfrage nach angegebener App-ID | Abfrage von Anwendungsdetails nach Anwendungs-ID. |
| Abfrage nach angegebener Kunden-Datei-GUID | Dateidetails abfragen nach `provider_file_id`. |
| Abfrage nach angegebener interner Datei-GUID | Dateidetails abfragen nach `history_guid`. |
| Abfrage nach angegebener interner Benutzer-ID | Benutzerdetails nach interner Benutzer-ID abfragen. |
| Abfrage nach angegebener Kunden-Benutzer-ID | Benutzerdetails abfragen nach `provider_user_id`. |

> Selbst bevor häufig verwendete SQLverwendet werden, ist es notwendig, die Zielumgebung und Parameterwerte zu bestätigen. Häufig verwendet SQL dient nur dazu, den manuellen Schreibaufwand zu reduzieren, garantiert jedoch nicht, dass die Abfrageergebnisse die Ziele dieser Untersuchung erfüllen.

## 5. Abfragen ausführen

1. Füllen Sie die Abfrageanweisung im SQL Eingabebereich aus.
2. Es ist vorzuziehen, `SELECT` Abfragen zu verwenden und keine Insert-, Update- oder Delete-Anweisungen auszuführen.
3. Die Standardabfrage LIMIT ist 10 und kann manuell angepasst werden.
4. Klicken Sie **Abfrage ausführen**.
5. Führen Sie nach Möglichkeit zuerst EXPLAIN aus und **bestätigen Sie die Ausführung**, bevor Sie die Abfrage starten.

Beispiel:

```sql
SELECT *
FROM example_table
WHERE id = 'example-id';
```

## 6. Anzeigen von Abfrageergebnissen

1. Sehen Sie sich die zurückgegebenen Datensätze im Ergebnisbereich an.
2. Überprüfen Sie, ob die Schlüsselfelder den Erwartungen entsprechen.
3. Wenn das Ergebnis leer ist, überprüfen Sie die Datenbank, den Tabellennamen, die Abfragebedingungen und den Zeitraum.
4. Wenn es zu viele Ergebnisse gibt, fügen Sie genauere Filterbedingungen hinzu und führen Sie die Abfrage erneut aus.

## 7. Verwendung der Abfragehistorie

"Abfragehistorie" wird verwendet, um die SQL Anweisungen anzuzeigen, die auf der aktuellen Seite ausgeführt wurden, was es praktisch macht, Fehlerbehebungsanweisungen erneut zu verwenden, Ausführungsergebnisse zu überprüfen und zu kopieren. SQL."

> [!NOTE]
>
> Die Abfragehistorie wird lokal im aktuellen Browser gespeichert und nicht dauerhaft gespeichert. Jede Datenbank-/Tabellendimension behält bis zu den letzten 100 Datensätzen bei, und aktuell gibt es keinen automatischen zeitbasierten Ablauf; das Löschen der Browser-Sitedaten, der Browserwechsel, der Gerätwechsel oder das Wechseln zu einer anderen Datenbank/Tabelle führt dazu, dass andere historische Datensätze angezeigt werden.

1. Wechseln zu **Abfragehistorie** im Ergebnisbereich.
2. Sehen Sie den Ausführungsstatus, Zeit, Datenbank, Tabelle, SQL, Anzahl der zurückgegebenen Zeilen und verstrichene Zeit in den Verlaufseinträgen.
3. Um eine SQL Anweisung erneut auszuführen, klicken Sie **In Editor einfügen und Ausführen** in der Spalte Aktion dieses Eintrags.
4. Wenn Sie die Anweisung nur wiederverwenden müssen, klicken Sie **Kopieren SQL**.

Abfrageverlaufsfeld Beschreibung:

| Feld | Beschreibung |
| --- | --- |
| Status | Ob SQL erfolgreich ausgeführt; Wenn es fehlschlägt, Fehler in Verbindung mit den Fehlermeldungen beheben. |
| Zeit | Die Ausführungszeit der aktuellen Abfrage. |
| Datenbank | Die währenddessen ausgewählte Datenbank SQL Ausführung. |
| Tabelle | Die währenddessen verbundene Tabelle SQL Ausführung. |
| SQL | Die tatsächlich ausgeführte Abfrageanweisung. |
| Gibt die Anzahl der Zeilen zurück | Anzahl der durch diese Abfrage zurückgegebenen Datenzeilen. |
| Verbrauchte Zeit | SQL Die Ausführung benötigt Zeit und kann verwendet werden, um zu bestimmen, ob ein Risiko langsamer Abfragen besteht. |
| Operation | Unterstützt das erneute Einfügen und Ausführen oder Kopieren SQL. |

Beim Beheben von Problemen mit der Abfragehistorie wird empfohlen, sich auf Folgendes zu konzentrieren: 

| Situation | Vorschläge zur Handhabung |
| --- | --- |
| Status fehlgeschlagen | Prüfen Sie zunächst, ob die SQL Syntax, die Datenbanktabelle existiert und ob die Felder korrekt sind. |
| Dauert lange | Filterbedingungen hinzufügen oder zuerst die Tabellenstruktur und Indexfelder überprüfen. |
| Zu viele Zeilen zurückgegeben | Bedingung hinzufügen 'WHERE' und 'LIMIT'. |
| Mehrere Abfrageergebnisse sind inkonsistent | Bestätigen, ob die Datenbank, Tabelle oder Umgebung gewechselt wurde. |

> Die Abfragehistorie dient dazu, den aktuellen Fehlerbehebungsprozess zu unterstützen. Vor der erneuten Ausführung der historischen SQL, müssen Sie weiterhin den SQL Inhalt, die Zieldatenbank und die aktuelle Umgebung bestätigen. 

## 8. Häufige Fehlerszenarien

| Szenario | Vorschläge zur Vorgehensweise |
| --- | --- |
| Überprüfen, ob Geschäftseinträge vorhanden sind | Verwenden Sie die Geschäft-ID für eine präzise Abfrage. |
| Aufgabenstatus prüfen | Abfrage des Statusfeldes und der Aktualisierungszeit anhand der Aufgaben-ID. |
| Fehlerhafte Konfigurationen beheben | Abfrage des aktuellen Wertes und der Aktualisierungszeit in der Konfigurationstabelle. |
| Überprüfen Sie kürzliche Änderungen | Abfrage in absteigender Reihenfolge nach Zeitfeld und Begrenzung der zurückgegebenen Einträge. |
| Abfrage von Anwendungs- oder Zertifikatsinformationen | Bevorzugen Sie die Verwendung von Zertifikatsabfragen oder AppID-Abfragen in „Common“ SQL”. |
| Fehlerbehebungsanweisungen wiederverwenden | Kopieren SQL aus der „Abfragehistorie“, Parameter überprüfen und erneut ausführen. |

## 9. Vorsichtsmaßnahmen

1. Unbedingte Abfragen auf großen Tabellen sind in der Produktionsumgebung verboten.
2. Wenn Sie sich über die SQL Auswirkungen unsicher sind, überprüfen Sie dies zuerst in einer risikofreien Umgebung.
3. Geschäftsdaten nicht direkt über RDB Tools nur verwenden, wenn ein klarer Änderungsplan und eine Genehmigung vorliegen.
4. Parameter gemeinsam SQL müssen mit den tatsächlichen Werten der aktuellen Umgebung ausgefüllt werden, um falsche Abfragen über verschiedene Umgebungen hinweg zu vermeiden.
5. SQL In der Abfragehistorie können sensible IDs enthalten sein. Den Umfang prüfen, bevor Kopien erstellt oder weitergeleitet werden.
6. Wenn Abfrageergebnisse sensible Informationen enthalten, keine vollständigen Screenshots oder Klartextdaten extern teilen.
