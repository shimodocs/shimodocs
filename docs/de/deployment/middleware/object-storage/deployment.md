# Bereitstellen mit Objektspeicher

[← ShimoDocs Suite Bereitstellungsdokumentation](../../README.md)

Dieser Artikel erklärt, wie man die integrierte MinIO im ShimoDocs Installer deaktiviert und der eigene S3 Objektspeicher als Drittanbieter S3 Objektspeicher. Nach Abschluss der Konfiguration prüft der Installer die S3 Objektspeicher-Netzwerkkonnektivität, Authentifizierungsinformationen und Lese-/Schreibberechtigungen für Buckets. Sobald die Prüfungen bestanden sind, kann die Bereitstellung fortgesetzt werden. 

# 1. Vorbereitung vor der Konfiguration 
Bitte bestätigen Sie vor Beginn: 
- S3 Objektspeicher ist installiert und läuft normal. 
- K8s Cluster-Knoten können auf Host und Port des S3 Objektspeichers zugreifen. 
- Authentifizierung AK/SK für die Verbindung zum S3 Objektspeicher ist bereit. 
- S3 Speicher muss den Zugriff über Client-Browser unterstützen. 
- Es wird empfohlen, eine separate S3 Instanz zu verwenden. 
- Standard S3 Protokollzugriffsadressen für interne und externe Netzwerke müssen bereitgestellt werden. 
- ShimoDocs geschäftsstandard S3 Buckets sollten im Voraus erstellt werden. 
- S3 Speicher muss SSD Festplatten verwenden. 

## ShimoDocs Business Standard S3 Bucket-Liste 

| Bucket-Name | Zugriffsberechtigung | Erlaubte Ursprünge | Erlaubte Methoden | Zugriffsmodus | Expose_Header | 
| --- | --- | --- | --- | --- | --- | 
| automatisch erwähnen | Privat Lese-/Schreibzugriff | - | - | Internes Netzwerk |  | 
| compose-payloads | Privat Lese-/Schreibzugriff | - | - | Internes Netzwerk |  | 
| fc-task | Privat Lese-/Schreibzugriff | - | - | Intranet |   |
| file-changesets | Privat Lese-/Schreibzugriff | - | - | Intranet |
| file-calced | Privat Lese-/Schreibzugriff | * | GET/HEAD | Intranet Extranet | 
| file-contents | Privat Lese-/Schreibzugriff | * | GET/HEAD | Intranet Extranet | x-amz-meta-head x-amz-meta-length x-amz-meta-bytes x-amz-meta-delta-version x-amz-meta-eek x-amz-meta-formula-cache x-amz-meta-compressor |
| file-templates | Privat Lese-/Schreibzugriff | - | - | Intranet Extranet |
| sheet-histories | Privat Lese-/Schreibzugriff | - | GET/HEAD | Intranet Extranet | Access-Control-Allow-Origin x-amz-meta-compressor |
| svc-doc-history | Privat Lese-/Schreibzugriff | * | GET/HEAD | Intranet | Access-Control-Allow-Origin x-amz-meta-compressor | 
| shimo-assets | Öffentlich Lesen Privat Schreiben | * | GET/HEAD | Intranet Extranet | 
| shimo-attachments | Privat Lese-/Schreibzugriff | * | GET/POST/PUT/HEAD | Intranet Extranet |  |  |  |
| shimo-images | Privater Lese-/Schreibzugriff | * | GET/POST/PUT/HEAD | Internes Netzwerk Externes Netzwerk |  |  |  |
| shimo-users | Privater Lese-/Schreibzugriff | - | - | Internes Netzwerk Externes Netzwerk |  |  |  |
| shimo-avatar | Öffentlich Lesen Privat Schreiben | * | GET | Internes Netzwerk Externes Netzwerk  |  |  |  |
| Vorschau | Privater Lese-/Schreibzugriff | * | GET/HEAD | Internes Netzwerk Externes Netzwerk |Akzeptanz-Bereiche x-amz-meta-head x-amz-meta-length x-amz-meta-bytes x-amz-meta-delta-version x-amz-meta-eek x-amz-meta-formel-cache x-amz-meta-kompressor  |  |  |
| svc-drive | Privater Lese-/Schreibzugriff | * | GET/POST/PUT | Internes Netzwerk Externes Netzwerk |Akzeptanz-Bereiche|  |  |
| svc-table | Privater Lese-/Schreibzugriff | * | GET/HEAD |  Internes Netzwerk Externes Netzwerk |  |  |  |
| Datei-Snapshots |  Privat Lesen/Schreiben|  - | - |  Internes Netzwerk Externes Netzwerk |  |  |  |

## Bucket-Konfigurationsanweisungen
- Offenlegen_Es wird empfohlen, die Header-Namen anzugeben und nicht das * Symbol zu verwenden, da einige Anbieter das * Symbol möglicherweise nicht unterstützen, zum Beispiel Huawei Cloud OBS, Alibaba OSS
- Der Bucket-Name kann nach den Bedürfnissen des Unternehmens mit einem Präfix konfiguriert werden, um Duplikate zu vermeiden

# 2. Erweiterte Einstellungen eingeben
Im Schritt 'Konfiguration' des Installationsprogramms, nach Abschluss der Netzwerk-, Zielumgebungs- und Knoteninformationen-Konfiguration, erweitern Sie die 'Erweiterte Konfiguration' am Seitenende.

# 3. Abbrechen der Installation von eingebauten MinIO
im Bereich 'Middleware-Services' abwählen MinIO

Nach dem Abwählen installiert der Installer den eingebauten MinIOnicht mehr, und ein externer S3 Das vorbereitete Object Storage wird später verwendet. Ob andere Middleware eingebaute Dienste verwendet, sollte gemäß dem tatsächlichen Bereitstellungsplan gewählt werden.

# 4. Konfiguration von Drittanbieter-Middleware öffnen
Klicken Sie im Bereich 'Drittanbieter-Middleware' auf 'Konfigurieren'.

# 5. Konfigurieren S3 Object Storage
1. Wählen Sie 'S3 Object Storage auf der linken Seite.
2. AktivierenMinIO Object Storage.
3. Für Hauptdienst/Editor-Interaktion jeweils eingeben: AK/SK, interner Endpunkt, öffentlicher Endpunkt, Host, Port, SSLund andere Informationen
4. Überprüfen und speichern

> [!TIP]
>
> Hauptdienst: Die für Dienste außer der kollaborativen Bearbeitung verwendete Object-Storage-Instanz
> Editor-Interaktion: Die von dem kollaborativen Bearbeitungsdienst verwendete Object-Storage-Instanz
>
> Hinweis: Der Hauptdienst und die Editor-Interaktion können dieselbe Object-Storage-Instanz verwenden, aber die Bereitstellung einer separaten Instanz für die Editor-Interaktion kann eine bessere Leistung bei der kollaborativen Bearbeitung bieten

## Bucket-Benennung

> [!NOTE]
>
> Wenn mehrere Geschäftsapplikationen dieselbe S3 Instanz gemeinsam nutzen, können Kunden Präfixe gemäß ShimoDocsden Bucket-Benennungsregeln hinzufügen, um zwischen verschiedenen Geschäftsbereichen zu unterscheiden und die Buckets zu verwalten

# 6. Bestätigen Sie die Überprüfungsergebnisse
Der Installer wird Folgendes überprüfen:
- login: Das Konto kann sich normal authentifizieren
- connectivity: Die Bereitstellungsumgebung kann auf den S3 Objektspeicher zugreifen
- permission: Das Konto hat die Berechtigungen für Verbindung, Authentifizierung, Bucket-Lese-/Schreibrechte usw.

Nachdem alle Prüfpunkte 'Erfolg' anzeigen, schließen Sie das Konfigurationsfenster und kehren Sie zur 'Konfiguration'-Seite des Installers zurück.

Wenn Prüfpunkte fehlgeschlagen sind, überprüfen Sie bitte gemäß den Seitenhinweisen:
- Ob Host und Port korrekt ausgefüllt sind.
- Ob das Netzwerk zwischen dem Bereitstellungsknoten und dem S3 Objektspeicher ist verbunden.
- Ob USERNAME und PASSWORD sind korrekt. 
- Ob das Konto die erforderlichen Berechtigungen hat (Verbindung und Authentifizierung, Bucket-Lese-/Schreibberechtigungen usw.). 

# 7. Fortsetzen der Bereitstellungsinitialisierung 
Nachdem Sie zur 'Konfiguration'-Seite zurückgekehrt sind, stellen Sie sicher, dass S3 Objektspeicher weiterhin nicht ausgewählt ist, und klicken Sie auf 'Bereitstellung initialisieren', um die Übersicht, Prüfungen und Ausführungsschritte der Bereitstellung fortzusetzen. 

> [!TIP] 
> 
> Bevor die Bereitstellung initialisiert wird, bestätigen Sie bitte erneut, dass die S3 Objektspeicherkonfiguration wurde gespeichert und alle Validierungspunkte wurden bestanden.
