# Allgemeine Werkzeuge

[← ShimoDocs Suite Bereitstellungsdokumentation](../../../README.md)

## 1. Seitenübersicht

Die Seite Allgemeine Werkzeuge enthält 7 häufig verwendete Funktionen: JSON Formatierung, Formatkonvertierung, JWT Decodierung, Zeitstempelkonvertierung, Maschinenzeitüberprüfung, QR-Code-Analyse und Base64-Codierung/Decodierung.

1. Klicken Sie auf eine Funktionskarte, um zur Bedienungsseite zu gelangen.
2. Nach dem Eintritt können Sie direkt von der Funktionsliste auf der linken Seite zu anderen Werkzeugen wechseln.
3. Klicken Sie auf 'Zurück zum Funktionsmenü', um zur Karten-Startseite zurückzukehren.

## 2. JSON Formatierung

Diese Funktion wird verwendet, um Inhalte zu formatieren, zu komprimieren und zu validieren. JSON Inhalt.

1. Klicken Sie aufJSON Formatierung.
2. Geben Sie den zu verarbeitenden Inhalt in das „Eingabefeld“ ein JSON“-Bereich, zum Beispiel:

   ```json
   {"name":"MDP","enabled":true,"items":[1,2]}
   ```

3. Klicken Sie auf 'Format', und eingerückt JSON wird rechts erzeugt werden.
4. Klicken Sie auf "Komprimieren", und ein kompaktes JSON Ohne Leerzeichen und Zeilenumbrüche wird auf der rechten Seite erstellt.
5. Klicken Sie auf 'Kopieren', um das verarbeitete Ergebnis zu kopieren.
6. Klicken Sie auf 'Löschen', um den Eingabe- und Ausgabeinhalt zu entfernen.

Testergebnisse: Strings, boolesche Werte und Arrays werden alle korrekt erhalten, und die Formatierungs- und Komprimierungsfunktionen funktionieren ordnungsgemäß.

## 3. Formatkonvertierung

Diese Funktion unterstützt die Konvertierung und Formatierung zwischen JSON, YAMLund TOML Formate.

1. Klicken Sie auf "Formatkonvertierung."
2. Wählen Sie das Eingabeinhaltsformat in "Quellformat" aus.
3. Wählen Sie das gewünschte Ausgabeformat in "Zielformat" aus.
4. Geben Sie den zu konvertierenden Inhalt links ein.
5. Klicken Sie auf "Konvertieren" und das Ergebnis wird rechts angezeigt.
6. Klicken Sie auf "Format tauschen", um die Quell- und Zielformate zu wechseln.
7. Klicken Sie auf "Format", um die Einrückung und das Layout des aktuellen Inhalts anzupassen.
8. Klicken Sie auf "Kopieren", um das Ausgaberesultat zu kopieren.

Dieses Mal verwenden wir JSON um zu konvertieren nach YAML, eingeben:

```json
{"name":"MDP","ports":[80,443],"enabled":true}
```

Konvertierungsergebnis: 

```yaml
name: MDP
ports:
  - 80
  - 443
enabled: true
```

Gemessene Ergebnisse: Felder, Arrays und boolesche Werte wurden korrekt konvertiert.

## 4. JWT Dekodieren

Diese Funktion wird verwendet, um den Header, Payload und die Signatur eines JWT Tokens zu analysieren.

1. Klicken Sie aufJWT Dekodieren.
2. Fügen Sie das JWT Token in das Eingabefeld ein.
3. Klicken Sie auf "Dekodieren".
4. Siehe den Signaturalgorithmus und den Token-Typ im Header.
5. Siehe Informationen wie Benutzer, Rolle und Ablaufdatum im Payload.
6. Siehe den Rohinhalt der Signatur.
7. Klicken Sie in jedem Abschnitt auf die Kopieren-Schaltfläche, um die Parsing-Ergebnisse zu kopieren.
8. Klicken Sie auf "Löschen", um das aktuelle Token und die Parsing-Ergebnisse zu löschen.

Testergebnisse: Das Test-Token hat Felder wie `HS256`, `JWT`, Benutzer, Rolle und Ablaufzeit erfolgreich geparst.

> JWT Decode dient nur zur Ansicht der Token-Struktur und kann die serverseitige Überprüfung der Signaturgültigkeit nicht ersetzen.

## 5. Zeitstempel-Konvertierung 

Diese Funktion unterstützt die bidirektionale Konvertierung zwischen Unix-Zeitstempel und Datum/Uhrzeit. 

### 5.1 Zeitstempel-Konvertierung zu Datum/Uhrzeit 

1. Klicken Sie auf "Zeitstempel-Konvertierung". 
2. Geben Sie einen 10-stelligen Sekunden- oder 13-stelligen Millisekunden-Zeitstempel in "Zeitstempel (Sekunden oder Millisekunden)" ein. 
3. Klicken Sie oben auf "Konvertieren". 
4. Sehen Sie das Datum und die Uhrzeit unter "Konvertierungsergebnisse". 
5. Klicken Sie auf die Kopieren-Schaltfläche neben dem Ergebnis, um den Inhalt zu kopieren. 

### 5.2 Datum/Uhrzeit zu Zeitstempel 

1. Geben Sie 'YYYY-MM-TT HH:mm:ss' ein oder das ISO Zeitformat im Feld "Datum Uhrzeit". 
2. Klicken Sie unten auf "Konvertieren". 
3. Sehen Sie den Unix-Zeitstempel unter "Konvertierungsergebnis (Sekunden)". 
4. Klicken Sie auf "Aktuelle Zeit", um schnell den aktuellen Zeitstempel und das Datum einzufügen. 
5. Klicken Sie auf "Löschen", um alle Inhalte zu entfernen. 

Testergebnis: '1704067200' erfolgreich in Datum-Uhrzeit konvertiert, und die Datum-Uhrzeit kann auch korrekt in Sekunden-Zeitstempel konvertiert werden. 

> Bei der Überprüfung von Daten über Zeitzonen hinweg klären Sie zunächst, ob die Geschäftszeit UTC oder lokale Zeitzonen verwendet. 

## 6. Maschinenzeitprüfung

Diese Funktion dient zur Überprüfung der Zeit aller Pods mit `app=ws-gateway` in der aktuellen NAMESPACE und hebt Instanzen mit einer Zeitabweichung von mehr als 30 Sekunden hervor.

1. Klicken Sie auf "Maschinenzeitprüfung".
2. Klicken Sie oben rechts auf "Aktualisieren".
3. Überprüfen Sie die aktuellen NAMESPACE und Abfrage-Labels.
4. Sehen Sie sich die vom System berechnete Referenzzeit an, die die Medianzeit aller Pods ist.
5. Sehen Sie sich den Knoten an, auf dem sich jeder Pod befindet, den Unix-Zeitstempel und die lesbare Zeit.
6. Überprüfen Sie "Abweichung von der Referenz" und "Status".
7. Wenn die Abweichung mehr als 30 Sekunden beträgt, überprüfen Sie die NTP/Chrony-, virtuelle Maschinenzeit- und Zeitzoneneinstellungen des Knotens.

Testergebnis: 1 `ws-gateway` Pod zurückgegeben, mit einer Abweichung von der Referenzzeit von `0s` und Status "Normal".

## 7. QR-Code-Analyse

Diese Funktion dient zum Hochladen von QR-Code-Bildern und zum Extrahieren des enthaltenen Texts, Links oder anderer Inhalte.

1. Klicken Sie auf "QR-Code-Analyse".
2. Klicken Sie auf "Datei auswählen".
3. Wählen Sie ein klares QR-Code-Bild von Ihrem lokalen Gerät aus.
4. Nachdem die Seite die Bildvorschau anzeigt, überprüfen Sie das "Analyseergebnis" unten.
5. Vergleichen Sie das Ergebnis mit dem erwarteten Inhalt des QR-Codes, um die Übereinstimmung zu bestätigen.
6. Klicken Sie auf "Kopieren", um den analysierten Inhalt zu kopieren.
7. Klicken Sie auf "Löschen", um das Bild und das Analyseergebnis zu entfernen.

Testergebnis: Der Test-QR-Code kann erfolgreich hochgeladen, in der Vorschau angezeigt und korrekt analysiert werden.

## 8. Base64-Codierung und -Decodierung

Diese Funktion dient der bidirektionalen Umwandlung zwischen Klartext und Base64-Inhalten.

### 8.1 Base64-Codierung

1. Klicken Sie auf "Base64-Codierung und -Decodierung".
2. Geben Sie den Klartext links ein.
3. Klicken Sie auf "Codieren".
4. Sehen Sie sich das Base64-codierte Ergebnis rechts an.

### 8.2 Base64-Decodierung

1. Geben Sie den Base64-Inhalt links ein.
2. Klicken Sie auf "Dekodieren".
3. Sehen Sie sich den wiederhergestellten Text rechts an.
4. Klicken Sie auf "Kopieren", um das Ergebnis zu kopieren.
5. Klicken Sie auf "Löschen", um die Eingabe und Ausgabe zu löschen.

Testergebnis:

```text
MDPTEST → TURQ5rWL6K+V
TURQ5rWL6K+V → MDPTEST
```

Chinesisch UTF-8-Inhalte können normal hin- und herkonvertiert werden.

