# gRPC Werkzeuge

[← ShimoDocs Suite Bereitstellungsdokumentation](../../../README.md)

> [!TIP]
>
> Der gRPC Tool wird verwendet, um eine Verbindung zu internen gRPC Diensten herzustellen, Dienste und Methoden anzuzeigen und Unary-Methoden-Debug-Aufrufe zu starten.
>
> Die Seite unterstützt drei Möglichkeiten, ein Ziel auszuwählen: manuelle Eingabe der Adresse, Auswahl nach Kubernetes Dienst oder Auswahl nach Pod.

## 1. Zugriff auf gRPC

1. Melden Sie sich bei der **MDP Operations-Plattform**.
2. Auswählen **Systemdienste** oben.
3. Erweitern **Middleware-Tools** in der linken Navigationsleiste.
4. Auswählen **gRPC**.

Die Seite zeigt zunächst den Bereich "Ziel" zur Auswahl eines gRPC Dienst zum Verbinden.

## 2. Methoden zur Zielauswahl

Die Seite bietet drei Zielmodi:

| Modus | Beschreibung |
| --- | --- |
| Manuell | Geben Sie manuell die gRPC Adresse, z. B., `svc-user:50051`. |
| Dienst | Wählen Sie das Ziel nach Cluster, Namespace, Dienst und Port aus. |
| Pod | Wählen Sie Ziele nach Cluster, Namespace, Pod und Port aus. |

## 3. Manuelle Verbindung 

1. Auswählen **Manuell**. 
2. Geben Sie die gRPC Dienstadresse in der **Adresse** Eingabebox. 
3. Klicken Sie **Verbinden**. 
4. Nach einer erfolgreichen Verbindung gelangt die Seite in den Service- / Methoden-Debugging-Arbeitsbereich. 

## 4. Verbindung über Service

1. Auswählen **Service**.
2. Wählen Sie den Ziel-Cluster und Namespace aus.
3. In der **Service- / Port** Dropdown-Liste wählen Sie den Zielservice und Port aus.
4. Wenn die Serviceliste nicht aktualisiert wird, klicken Sie **Aktualisieren**.
5. Klicken Sie **Verbinden**.

## 5. Verbindung über Pod

1. Auswählen **Pod**.
2. Wählen Sie den Ziel-Cluster und Namespace aus.
3. In der **Pod / Port** Dropdown-Liste wählen Sie den Zielpod und Port aus.
4. Wenn die Pod-Liste nicht aktualisiert wird, klicken Sie **Aktualisieren**.
5. Klicken Sie **Verbinden**.

## 6. Wählen Sie Dienst und Methode

Nach einer erfolgreichen Verbindung ist die Seite in eine Dienstliste, eine Methodenliste, einen Anforderungsbereich und einen Antwortbereich unterteilt.

1. Wählen Sie den Ziel-Dienst aus der Dienstliste links aus.
2. Sie können das Dienstsuchfeld verwenden, um Dienste zu filtern.
3. Wählen Sie die Zielmethode aus der Methodenliste aus.
4. Umschaltbare Methodenfilteroptionen: `Unary`, `Streaming`, `All`.
5. Sie können das Methodensuchfeld verwenden, um Methoden zu filtern.

> Die aktuelle Seite unterstützt nur das Aufrufen von Unary-Methoden. Streaming-Methoden werden als nicht verfügbar angezeigt.

## 7. Anforderungsparameter ausfüllen

Der Anforderungsbereich unterstützt zwei Möglichkeiten des Ausfüllens:

| Methode | Beschreibung |
| --- | --- |
| Formularmodus | Die Seite erzeugt ein Formular basierend auf den Eingabefeldern der Methode. |
| JSON Modus | Wenn **JSON Modus** aktiviert ist, bearbeiten Sie direkt den vollständigen JSON Anforderungskörper. |

Schritte zur Verwendung des Formularmodus:

1. Wählen Sie die Zielmethode aus.
2. Füllen Sie die Anforderungsparameter Feld für Feld aus.
3. Verwenden Sie das Dropdown-Menü, um aufgezählte Felder auszuwählen.
4. Auswählen `true` oder `false` für boolesche Felder.
5. Verwenden Sie Kommas wie auf der Seite angegeben für wiederholte Felder.

Schritte zur Verwendung des JSON Modus:

1. Schalten Sie den **JSON Modus** Schalter ein.
2. Füllen Sie den vollständigen JSON im Textfeld aus.
3. Stellen Sie sicher, dass das JSON Format gültig ist.

## 8. Metadaten ausfüllen

1. Erweitern **Metadaten** im Anforderungsbereich.
2. Füllen Sie Schlüssel und Wert aus.
3. Um mehrere Metadaten-Einträge hinzuzufügen, klicken Sie auf **Hinzufügen**.
4. Um eine Zeile zu löschen, klicken Sie auf das Löschsymbol.

Metadaten werden üblicherweise verwendet, um Authentifizierungsinformationen, Anforderungs-ID oder Geschäftskontext zu übermitteln.

## 9. Den Anruf starten und die Antwort anzeigen

1. Bestätigen Sie das Ziel, den Service, die Methode, den Anforderungstext und die Metadaten.
2. Klicken Sie **Aufrufen** oben rechts auf der Seite.
3. Sehen Sie den Status, die vergangene Zeit, die Antwortmetadaten und die Antwort JSON im Antwortbereich.
4. Wenn der Anruf fehlschlägt, zeigt der Antwortbereich den Fehlerstatus und den Fehlerinhalt an.

## 10. Ziel wechseln oder erneut verbinden

1. Klicken Sie **Verbinden** oben, um die Servicedefinition des aktuellen Ziels neu zu laden.
2. Klicken Sie **Ziel ändern** um zur Zielauswahlseite zurückzukehren.
3. Nach dem Wechseln des Ziels müssen Sie erneut verbinden und Service / Methode erneut auswählen.

## 11. Häufige Problemlösungsszenarien

| Szenario | Vorschlag für die Vorgehensweise |
| --- | --- |
| Überprüfen, ob der Service erreichbar ist | Wählen Sie das Ziel und klicken Sie **Verbinden** um zu sehen, ob die Serviceliste geladen werden kann. |
| Schnittstellenmethoden finden | Verwenden Sie die Service- und Methodensuche zur Filterung. |
| Abfrage-Schnittstelle debuggen | Wählen Sie eine Unary-Methode, füllen Sie die Anforderungsparameter aus und klicken Sie **Aufrufen**. |
| Muss Kontext übergeben werden | Erweitern Sie Metadaten und geben Sie den entsprechenden Schlüssel und Wert ein. |
| Antwort ist leer oder fehlgeschlagen | Überprüfen Sie den Antwortstatus, Fehlerinhalt und Metadaten. |
