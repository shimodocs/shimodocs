# Redis Werkzeuge

[← ShimoDocs Suite Bereitstellungsdokumentation](../../../README.md)

> [!TIP]
>
> Redis wird in der Operations-Plattform verwendet, um Redis Instanzen, DBs, eine Liste von Schlüsseln (Keys) und Schlüssel-Details. Es wird häufig verwendet, um Caches, Sessions, verteilte Sperren, Rate-Limiting-Zähler und Kurzzeit-Zustände zu diagnostizieren.
>
> Die Seite unterstützt die Suche nach Schlüssel oder Schlüsselpräfixen und zeigt den Schlüsseltyp, TTL, sowie den aktuellen Wert an.

## 1. Zugriff auf Redis

1. Melden Sie sich bei **MDP Operations-Plattform**.
2. Auswählen **Systemdienste** oben.
3. Erweitern **Middleware-Tools** in der linken Navigationsleiste.
4. Auswählen **Redis**.

Die linke Seite der Seite ist der Abfragebereich für Schlüssel, die rechte Seite ist der Bereich für Schlüsseldetails.

## 2. Auswahl Redis Instanz und DB

1. Wählen Sie im ersten Dropdown oben links die Redis Instanz aus.
2. Wählen Sie im zweiten Dropdown die DB aus, zum Beispiel `db0`.
3. Die Seite lädt die Schlüsselliste basierend auf der aktuellen Instanz und DB.

Wenn die DB-Liste leer ist oder die Seite einen Fehler anzeigt, prüfen Sie bitte zunächst, ob die Redis Instanzkonfiguration normal ist.

## 3. Schlüssel suchen

1. Geben Sie den Schlüsselnamen oder Schlüsselpräfix in das Suchfeld ein.
2. Klicken Sie auf die Suchschaltfläche oder drücken Sie Enter, um die Abfrage auszuführen.
3. Die Schlüsselliste auf der linken Seite anzeigen.
4. Wenn Sie die Liste unter den aktuellen Bedingungen neu laden müssen, klicken Sie auf das Aktualisierungssymbol.

Die Eingabeaufforderung des Suchfelds lautet: "Bitte geben Sie den Schlüsselnamen ein, unscharfe Suche unterstützt." Die Seite zeigt den passenden Schlüsseltyp und TTL.

## 4. Schlüsselliste anzeigen

Die Schlüsselliste enthält die folgenden Informationen:

| Informationen | Beschreibung |
| --- | --- |
| Typ | Die Redis Art des Schlüssels, wie z. B. `string`, `hash`, `list`, `set`, `zset`. |
| Schlüsselname | Der vollständig übereinstimmende Schlüssel. |
| TTL | Die verbleibende Ablaufzeit des Schlüssels; die Seite zeigt „permanent“ an, wenn der aktuelle Schlüssel kein Ablaufdatum hat. |

## 5. Schlüssel-Details anzeigen

1. Klicken Sie auf den Zielschlüssel in der Schlüsselliste links.
2. Der Detailbereich rechts zeigt den Schlüsselname, den Typ TTLund den spezifischen Wert an.
3. Um die aktuellen Schlüssel-Details zu aktualisieren, klicken Sie auf die Aktualisierungsschaltfläche im Detailtitelbereich.

Die verschiedenen Anzeigemethoden sind wie folgt:

| Typ | Anzeigemethode |
| --- | --- |
| `string` | Den vollständigen Wert in einem Textfeld anzeigen. |
| `hash` | Feld Key und Wert in einer Tabelle anzeigen. |
| `list` / `set` | Die Liste der Elemente in einer Tabelle anzeigen. |
| `zset` | Score und Member in einer Tabelle anzeigen. |

## 6. Feldwerte kopieren

1. Suchen Sie das Feld oder den Wert, den Sie kopieren möchten, in der Schlüsseldetailliste.
2. Klicken Sie auf den entsprechenden Inhalt.
3. Die Seite kopiert diesen Inhalt in die Zwischenablage.

> `string` Der Typ wird in einem Textfeld angezeigt und kann direkt durch Auswählen des Textes kopiert werden; Tabellentypen unterstützen das Klicken auf den Wert zum Kopieren.

## 7. Häufige Problemlösungsfälle

| Szenario | Vorgeschlagene Aktion |
| --- | --- |
| Überprüfen, ob der Cache existiert | Nach Auswahl der Instanz und DB, suchen Sie nach vollständigem Schlüssel oder Präfix. |
| Überprüfen, ob der Cache abgelaufen ist | Überprüfen Sie die TTL in der Schlüsselliste oder in den Details. |
| Hash-Felder anzeigen | Klicken Sie auf den Schlüssel, um die Felder und Werte in der Tabelle rechts anzuzeigen. |
| ZSet sortierte Daten anzeigen | Klicken Sie auf den `zset` Schlüssel, um Score und Member anzuzeigen. |
| Den neuesten Status desselben Schlüssels überprüfen | Klicken Sie auf die Aktualisierungsschaltfläche im Detailbereich. |
