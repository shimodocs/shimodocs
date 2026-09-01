# Kompatibilitätstest

[← ShimoDocs Suite Bereitstellungsdokumentation](../../../README.md)

## 1. Seitenübersicht

Die Kompatibilitätstestseite wird verwendet, um die Objekt-Storage-Konfiguration, Konnektivität, Upload-Kompatibilität und Upload-Leistung zu überprüfen. Die Seite ist unterteilt in:

1. Speicherkonfiguration;
2. Kompatibilitätstest;
3. Leistungstest.

## 2. Speicherkonfiguration 

### 2.1 Beschreibung der Konfigurationselemente 

| Konfigurationselement | Funktion |
| --- | --- |
| Zugriffsschlüssel | Identifikator der Objekt-Storage-Zugriffsidentität, nämlich AK |
| Geheimer Schlüssel | Der Zugriffsschlüssel, der mit AK einhergeht, also SK |
| Endpunkt | Adresse des Objekt-Storage-Dienstes |
| Bucket-Name | Ziel-Bucket, der überprüft werden soll |
| Region | Speicher-Bucket-Standortbereich |
| Öffentlicher Endpunkt | Die von Browsern verwendete öffentliche Domain zum Zugriff auf den Objekt-Storage, optional |
| Pfad-Stil | Verwendung des Formats 'endpoint/bucket/object' zum Zugriff auf Objekte, Dienste wie MinIO müssen dies normalerweise aktivieren |

### 2.2 Füllen der Konfiguration 
1. Klicken Sie nach Bedarf auf „Attachments-Bucket füllen“ oder „Contents-Bucket füllen“. 
2. Das System füllt automatisch Konfigurationen wie AK, SK, Endpoint, Bucket, Region usw. entsprechend der aktuellen Umgebung aus. 
3. Wenn Sie die automatische Ausfüllung nicht verwenden, können Sie auch alle Konfigurationen manuell eingeben. 
4. Überprüfen Sie, ob der Endpunkt das richtige Protokoll und den richtigen Port enthält. 
5. Überprüfen Sie, ob der Bucket-Name der Bucket ist, der dieses Mal erkannt werden muss. 
6. Überprüfen Sie, ob die Region mit der tatsächlichen Region des Objektspeichers übereinstimmt.
7. Wenn Sie möchten, dass der Browser direkt auf den Objektspeicher zugreift, füllen Sie den öffentlichen Endpunkt aus.
8. Entscheiden Sie je nach Art des Objektspeichers, ob der Pfadstil aktiviert werden soll.

Nach dem automatischen Ausfüllen des Attachments-Buckets dieses Mal:

- Bucket: `shimo-attachments`;
- Endpunkt: wird automatisch vom System ausgefüllt;
- Region: `cn-north-1`;
- Öffentlicher Endpunkt: wird automatisch vom System ausgefüllt;
- Pfadstil: aktiviert.

Nach dem Klicken auf "Inhalte-Bucket ausfüllen" kann der Bucket automatisch wechseln zu `file-contents`.

> Secret Key ist sensible Information; zeigen Sie ihn nicht im Klartext in Screenshots, Chats oder Tickets an.

## 3. Kompatibilitätstest

Der Kompatibilitätstest überprüft nacheinander das Backend-Upload, den direkten Browser-Upload und den Multipart-Upload.

### 3.1 Test starten

1. Schließen Sie die Speicher-Konfiguration ab.
2. Klicken Sie auf den Reiter "Kompatibilitätstest".
3. Bestätigen Sie erneut, dass die Konfiguration von Bucket, Endpunkt, Region, öffentlichem Endpunkt und Pfadstil korrekt ist.
4. Klicken Sie auf "Kompatibilitätstest starten".
5. Warten Sie, bis die Seite "Kompatibilitätstest abgeschlossen" anzeigt.
6. Prüfen Sie, ob der Zusammenfassungsstatus auf der Seite "Alle bestanden" ist.
7. Überprüfen Sie den Status, die Dauer und die Ergebnisinformationen der drei Tests separat.

### 3.2 Backend-Upload-Test

Dieser Test wird verwendet, um die Netzwerkverbindung und Schreibberechtigungen vom Backend-Service zum Objektspeicher zu überprüfen.

1. Das Backend erzeugt eine Testtextdatei.
2. Das Backend schreibt die Datei in den Ziel-Bucket.
3. Die Seite zeigt die Upload-Dauer und den Pfad des Testobjekts an.
4. Ein grüner Erfolgsstatus zeigt an, dass das Backend-Netzwerk und die Schreibberechtigungen normal sind.

Aktuelles Ergebnis: Upload erfolgreich, Dauer `157 ms`.

### 3.3 Browser-Direktupload-Test

Dieser Test wird verwendet, um die Verbindung für den direkten Upload vom Browser zum Objektspeicher über PostPolicy zu überprüfen.

1. Die Seite fordert die für den Direktupload erforderliche PostPolicy vom Backend an.
2. Der Browser verwendet den öffentlichen Endpunkt, um Dateien direkt in den Objektspeicher hochzuladen.
3. Die Seite überprüft den HTTP Statuscode, der vom Objektspeicher zurückgegeben wird.
4. HTTP 204 zeigt an, dass der direkte Browser-Upload erfolgreich war.

Ergebnis dieses Mal: Direkter Browser-Upload erfolgreich, dauerte `61 ms`, Statuscode `204`.

### 3.4 Multipart-Upload-Test

Dieser Test wird verwendet, um den vollständigen Ablauf des Multipart-Uploads von großen Dateien zu überprüfen.

1. Initialisiere die Multipart-Upload-Aufgabe.
2. Teile die Testdatei in mehrere Teile.
3. Lade jeden Teil nacheinander hoch.
4. Rufe die Merge-Schnittstelle auf, um die Objekterstellung abzuschließen.
5. Die Seite zeigt `multipart upload succeeded`, was darauf hinweist, dass der gesamte Prozess erfolgreich war.

Ergebnis dieses Mal: Multipart-Upload erfolgreich, dauerte `1746 ms`.

### 3.5 Beschreibung der Testobjekte

Kompatibilitätstests führen tatsächliche Schreibvorgänge im Ziel-Bucket durch, und die Pfade der Backend-Testobjekte sind üblicherweise ähnlich zu:

```text
compatibility-tests/<RANDOM UUID>.txt
```

1. Überprüfen Sie, dass der Ziel-Bucket korrekt ist, bevor Sie Tests durchführen.
2. Führen Sie keine Tests wahllos in einem falschen Produktions-Bucket durch.
3. Nach den Tests können Sie Testobjekte gemäß der Bereinigungspolitik der Umgebung überprüfen und entfernen.

## 4. Leistungstest

Leistungstests werden verwendet, um die Upload-Zeit und den Durchsatz für verschiedene Dateigrößen zu messen.

### 4.1 Konfiguration von Leistungstests

1. Klicken Sie auf die Registerkarte "Leistungstests".
2. Wählen Sie den Testmodus; die Seite ist standardmäßig auf "Browser Direkt" eingestellt.
3. Wählen Sie den Inhaltstyp; Sie können `application/octet-stream` standardmäßig.
4. Wählen Sie die Dateigröße, die Sie testen möchten.
5. Die Seite unterstützt 1, 5, 8, 10, 12, 15, 20 und 25 MB.
6. In der Testumgebung können Sie zunächst 1 MB für eine schnelle Validierung auswählen.
7. Für einen formalen Leistungsvergleich wählen Sie mehrere Dateigrößen für den Test aus.

### 4.2 Starten des Leistungstests

1. Stellen Sie sicher, dass die Speicher-Konfiguration korrekt ist.
2. Stellen Sie sicher, dass mindestens eine Dateigröße ausgewählt wurde.
3. Klicken Sie auf 'Leistungstest starten'.
4. Warten Sie, bis alle Dateien hochgeladen sind.
5. Überprüfen Sie den durchschnittlichen Durchsatz, die kürzeste Zeit und die längste Zeit.
6. Überprüfen Sie den Status, die verstrichene Zeit, den Durchsatz und Fehlermeldungen für jede Dateigröße.
7. Wenn Fehler auftreten, überprüfen Sie zuerst das Browser-Netzwerk, den öffentlichen Endpunkt, die Cross-Origin-Konfiguration und den Status des Objektspeichers.

### 4.3 Ergebnisse dieses Tests

Dieser Test verwendet nur 1 MB Dateien für einen leichten Frontend-Direkt-Upload-Test:

| Metrik | Ergebnis |
| --- | ---: |
| Dateigröße | 1,0 MB |
| Status | Erfolg |
| Zeit | 874 ms |
| Durchsatz | 1,14 MB/s |
| Fehlermeldung | Keine |

Tatsächliche Ergebnisse: Upload erfolgreich, und die Seite kann verstrichene Zeit und Durchsatzmetriken korrekt erzeugen.

> Leistungsergebnisse können durch das Netzwerk des Browsers, die Cluster-Auslastung, Proxy-Verbindungen und die Last des Objektspeichers beeinflusst werden. Ein einzelner Test kann nur die funktionale Verfügbarkeit überprüfen; die formale Leistungsabnahme sollte wiederholt in derselben Umgebung getestet und Statistik für P50, P95sowie Fehlerraten sollten aufgezeichnet werden.

## 5. Vorsichtsmaßnahmen
1. Bestätigen Sie den Ziel-Bucket, bevor Sie Tests durchführen, um zu vermeiden, dass Testdateien in den falschen Speicher-Bucket geschrieben werden.
2. Zeigen Sie den Secret Key nicht im Klartext in Dokumenten oder Screenshots an.
3. Direktes Hochladen über den Browser hängt vom Public Endpoint und der Cross-Origin-Konfiguration ab.
4. S3-kompatible Dienste wie MinIO erfordern normalerweise die Aktivierung des Path Style.
5. Leistungstests erzeugen echten Netzwerkverkehr und Speicherzugriffe; bewerten Sie die Auswirkungen auf die Umgebung, bevor Sie große Dateien testen.
6. Die formale Leistungsabnahme sollte in mehreren Runden durchgeführt werden; ein einzelnes Browser-Testergebnis ist nicht ausreichend.
