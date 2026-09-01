# MongoDB Werkzeuge

[← ShimoDocs Suite Bereitstellungsdokumentation](../../../README.md)

> [!TIP]
>
> MongoDB wird in der Operations-Plattform verwendet, um MongoDB Datenbanken, Sammlungen und Dokumentinhalte anzuzeigen. Es eignet sich zur Fehlersuche bei dokumentenbasierten Daten, Zwischenzuständen, Aufgabenaufzeichnungen und Geschäftsdaten mit flexiblen Strukturen.
>
> Die Seite unterstützt die Suche nach Datenbank oder Sammlung, und nach Auswahl einer Sammlung, MongoDB JSON Bedingte Abfragen können verwendet werden.

## 1. Zugriff auf MongoDB

1. Melden Sie sich bei **MDP Operations-Plattform**.
2. Auswählen **Systemdienste** oben.
3. Erweitern **Middleware-Tools** in der linken Navigationsleiste.
4. Auswählen **MongoDB**.

Die linke Seite der Seite zeigt die Datenbank- und Sammlungshierarchie, während die rechte Seite die Abfragebedingungen und Abfrageergebnisse zeigt.

## 2. Durchsuchen von Datenbanken oder Sammlungen

1. Eingeben DATABASE_NAME Oder Sammlungsnamen-Stichwörter in das Suchfeld oben links eingeben.
2. Gefilterte Baumliste anzeigen.
3. Suchfeld löschen, um die Anzeige aller Datenbanken wiederherzustellen.

## 3. Erweitern Sie die Datenbank und wählen Sie eine Sammlung aus

1. Finden Sie die Ziel-Datenbank im Baum auf der linken Seite.
2. Klicken Sie auf das Erweiterungssymbol links neben der Datenbank, um die Liste der Sammlungen zu laden.
3. Wählen Sie die Ziel-Sammlung aus.
4. Die Seite auf der rechten Seite führt automatisch einmal eine Abfrage mit der Standardbedingung aus. `{}`.

> Wenn nur die Datenbank ausgewählt wird, wird keine Abfrage für die Sammlung ausgeführt; Sie müssen zuerst eine bestimmte Sammlung auswählen, und dann wird der Abfragebereich auf der rechten Seite angezeigt.

## 4. Geben Sie die Abfragebedingungen ein

1. Füllen Sie die MongoDB JSON Abfragebedingungen in das Abfrageeingabefeld rechts eingeben.
2. Wählen Sie die Anzahl der zurückzugebenden Ergebnisse, unterstützt `limit: 10`, `limit: 20`, `limit: 50`.
3. Klicken Sie **Abfrage**.

Abfragebeispiel: 

```json
{
  "_id": "task-123"
}
```

Beispiel für die Abfrage nach Feld:

```json
{
  "status": "running"
}
```

## 5. Anzeige der Abfrageergebnisse

1. Nach Abschluss der Abfrage überprüfen Sie die zurückgegebenen Dokumente im Ergebnisbereich rechts.
2. Standardmäßig werden die Ergebnisse in JSON Format angezeigt.
3. Klicken Sie **Erweitern** um das aktuelle Dokument zu erweitern.
4. Klicken Sie **Zusammenklappen** um das aktuelle Dokument zu reduzieren.
5. Klicken Sie **Kopieren** um das aktuelle Dokument zu kopieren JSON.

## 6. Häufige Fehlerbehebungsszenarien

| Szenario | Betriebsempfehlung |
| --- | --- |
| Bestätigen Sie, ob das Dokument existiert | Nach der Auswahl der Sammlung abfragen mit `_id` oder der Geschäfts-ID. |
| Aufgabenstatus prüfen | Nach der Aufgaben-ID abfragen und das Statusfeld sowie das Aktualisierungszeitfeld prüfen. |
| Einen Typ von Datensatz finden | Verwenden Sie eine Kombination von Feldern wie Status, Typ und Erstellungszeit, um abzufragen. |
| Ergebnis ist leer | Prüfen Sie, ob die richtigen Datenbank-, Sammlungs-, Feldnamen und Feldtypen ausgewählt wurden. |
| Ergebnisse der Fehlerbehebung müssen entfernt werden | Klicken Sie **Kopieren** auf ein einzelnes Ergebnis. |
