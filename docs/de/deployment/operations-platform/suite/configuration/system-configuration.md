# Systemkonfiguration

[← ShimoDocs Suite Bereitstellungsdokumentation](../../../README.md)

## 1. Anweisungen

Dieses Handbuch stellt die Funktion "Systemkonfiguration" von ShimoDocs Suitevor, geeignet für Systemadministratoren und Implementierer, die diese Funktion zum ersten Mal verwenden. Sie können die Schritte in diesem Dokument befolgen, um Konfigurationselemente zu finden, Konfigurationen zu ändern, zu prüfen, ob die Änderungen wirksam sind, und bei Bedarf die ursprünglichen Einstellungen wiederherstellen.

> [!TIP]
>
> Wenn Sie unsicher über die Bedeutung oder Auswirkung eines Konfigurationselements sind, wenden Sie sich bitte an ShimoDocs Technischer Support zur Bestätigung einholen, bevor Änderungen vorgenommen werden.

**Wichtigste Bereichsregel** Wenn die Enterprise-ID leer gelassen wird, beziehen sich die Abfrage und Änderung auf die globale Konfiguration; wenn eine Enterprise-ID ausgewählt wird, beziehen sich die Abfrage und Änderung auf die Konfiguration des ausgewählten Unternehmens. Das Ändern der globalen Konfiguration kann mehrere Unternehmen betreffen, daher bitte den Konfigurationsbereich vor dem Speichern erneut bestätigen.

### 1.1 Zugangsweg

Admin-Backend > ShimoDocs Suite > Konfigurationsmanagement > Systemkonfiguration

### 1.2 Vorbereitung vor der Verwendung

- Bestätigen Sie, dass das Login-Konto über Ansichts- und Bearbeitungsberechtigungen für die ShimoDocs Suite Systemkonfiguration verfügt.
- Bestätigen Sie zunächst, ob der Zielbereich global oder ein bestimmtes Unternehmen ist, und ermitteln Sie die genaue Unternehmens-ID.
- Bestätigen Sie die Namen der Konfigurationsschlüssel anhand der Konfigurationsanforderungen oder Anhang A. Der Name des Konfigurationsschlüssels ist ein eindeutiger Bezeichner; bitte raten Sie nicht ausschließlich auf Basis des chinesischen Namens.
- Notieren Sie die Quelle, den Status und den effektiven Wert vor der Änderung; für wichtige Konfigurationen bereiten Sie auch Rücksetzwerte vor.
- Globale Konfigurationen mit großer Auswirkung sollten während Geschäftszeiten mit geringem Aufkommen geändert werden, und relevantes Personal sollte im Voraus informiert werden.

## 2. Konfigurationsumfang und Priorität

Die Systemkonfiguration unterstützt sowohl globale als auch unternehmensbezogene Bereiche. Vor der Änderung müssen das Feld Unternehmens-ID und die Bereichsaufforderung am unteren Rand der Seite überprüft werden.

| **Funktionsbereich** | **Globaler Bereich** | **Unternehmensbereich** | **Seitenerkennungszeichen** |
| --- | --- | --- | --- |
| Systemkonfiguration | Unternehmens-ID leer lassen | Unternehmens-ID auswählen | Unten stehender Hinweis „Globale Version überschreiben“ oder „Endgültiges Ergebnis des Unternehmens“ |

*Abbildung 1  Ort zur Auswahl der Enterprise-ID in der Systemkonfiguration*

### 2.1 Globale Konfiguration

- Die Enterprise-ID nicht auswählen.
- Die Seite gibt an, dass die aktuelle Abfrage und Änderung für die 'Globale Versionsüberschreibung' gilt.
- Globale Werte sind die Basiswerte, die verwendet werden, wenn keine unternehmensspezifische Überschreibung festgelegt ist; deren Änderung kann mehrere Unternehmen betreffen.
- Vor dem Speichern mindestens einmal überprüfen, ob das Feld Enterprise-ID tatsächlich leer ist.

### 2.2 Unternehmensspezifische Konfiguration

- Das Zielunternehmen aus dem Enterprise-ID-Dropdown auswählen.
- Die Seite zeigt das endgültige effektive Ergebnis nach der Zusammenführung der Standardwerte mit der individuellen Konfiguration des aktuellen Unternehmens.
- Die unternehmensspezifische Konfiguration betrifft nur das ausgewählte Unternehmen und ändert die Konfiguration anderer Unternehmen nicht direkt.
- Wenn eine unternehmensspezifische Konfiguration für dasselbe Element existiert, hat der endgültige effektive Wert des Unternehmens Vorrang vor dem globalen Wert.

### 2.3 Überschreibung, Vererbung und Wiederherstellung

- Wenn das aktuelle Unternehmen keine Überschreibung hat, werden die globale Konfiguration oder die Standardwerte des Systems verwendet.
- Operationen wie 'Systemstandard wiederherstellen' oder das Löschen der aktuellen Überschreibung bedeuten im Allgemeinen, die Überschreibung des aktuellen Bereichs zu entfernen und den Wert der übergeordneten Ebene erneut zu übernehmen.
- Hinweise auf der Seite wie 'Systemstandard', 'Globale Versionsüberschreibung' und 'Endgültiges effektives Ergebnis des Unternehmens' können verwendet werden, um zu bestimmen, aus welcher Ebene der aktuelle Wert stammt.
- Vor der Wiederherstellung oder dem Löschen den aktuellen Wert aufzeichnen und bestätigen, dass das geerbte Ergebnis den Erwartungen entspricht.

**Risikohinweis** Speichern Sie nicht direkt, ohne den Unternehmensbereich zu bestätigen. Wenn die Unternehmens-ID leer ist, kann der Vorgang eine globale Überschreibung schreiben und mehrere Unternehmen betreffen.

## 3. Systemkonfiguration

Die Systemkonfiguration dient dazu, die allgemeinen Funktionen, Kontingente und Betriebsparameter zu betrachten und anzupassen. ShimoDocs Suite.

### 3.1 Suchmethode Eins: Exakte Suche

- Unternehmens-ID: Leer lassen für global; eine Unternehmens-ID für eine unternehmensspezifische Konfiguration auswählen.
- Suchkriterien: wählen Sie Typ, Werttyp und Ablaufdatum nach Bedarf; lassen Sie "Alle" stehen, wenn Sie unsicher sind.
- Schlüsselname: Geben Sie den Konfigurationsschlüssel ein; ein Schlüssel pro Zeile oder verwenden Sie Kommas zur Trennung mehrerer Schlüssel.
- Klicken Sie auf "Suchen", um Name, Schlüssel, Quelle, Status und aktuellen Wert in den Ergebnissen zu bestätigen.
- Klicken Sie auf „Bearbeiten“ ganz rechts in der Zielliste, um das Änderungs-Popup zu öffnen.

*Abbildung 2  Präziser Suchbereich der Systemkonfiguration*

*Abbildung 3  Einzelergebnis nach präziser Suche nach Konfigurations-Schlüsselname*

**Bereichshinweis** Wenn „Wenn kein Unternehmen ausgewählt ist, bezieht sich die aktuelle Abfrage und Änderung auf die globale Version-Überschreibung“ am unteren Rand der Seite angezeigt wird, bedeutet dies, dass der aktuelle Bereich global ist. Nach der Auswahl eines Unternehmens zeigt die Seite das endgültig wirksame Ergebnis, das Standardwerte mit der benutzerdefinierten Konfiguration des aktuellen Unternehmens kombiniert.

### 3.2 Zweite Suchmethode: Direkt in der Liste finden

- Behalten Sie den Unternehmensbereich und die Filterbedingungen korrekt bei und verwenden Sie das Scrollen der Seite, um die Liste zu durchsuchen.
- Bestätigen Sie die Zielkonfiguration anhand des „Namens“ oder „Schlüsselnamens“, beurteilen Sie nicht nur anhand des aktuellen Werts.
- Sehen Sie den Typ, das wirksame Ende, den aktuellen Wert, die Quelle und den Status in derselben Zeile.
- Klicken Sie ganz rechts auf „Bearbeiten“. Wenn Sie die Spalte für Operationen nicht sehen können, scrollen Sie die Tabelle horizontal nach rechts oder vergrößern Sie das Browser-Fenster.

### 3.3 Bearbeiten verschiedener Arten von Systemkonfigurationen

#### 3.3.1 Schlüssel-Wert-Typ

Der obere Teil des Bearbeitungspopups zeigt schreibgeschützte Metadaten an, einschließlich Schlüsselname, Name, Beschreibung, Typ und wirksames Ende. Wenn aktiviert, füllen Sie Werte in Eingabefeldern wie „Zeichenkettenwert“ aus und speichern Sie. Wenn die Zeichenkette JSON, URL, Pfad oder Liste enthält, sollte das ursprüngliche Format beibehalten werden.

*Abbildung 4  Systemkonfiguration Schlüssel-Wert-Typ Bearbeitungspopup*

#### 3.3.2 Kontingenttyp

Das Kontingent-Popup enthält normalerweise Status, Minimalwert, Maximalwert und einen „Keine Validierung“-Schalter. Nach der Aktivierung der Konfiguration füllen Sie den Bereich entsprechend den Geschäftsanforderungen aus; das Einschalten von „Keine Validierung“ bedeutet, dass das System keine Einschränkungsprüfungen basierend auf dem eingegebenen Bereich durchführt. Werte müssen mit den Einheiten des Popups übereinstimmen, wie „Stücke“, „MB“ usw.

*Abbildung 5 Systemkonfiguration Kontingenttyp Bearbeitungspopup*

#### 3.3.3 Funktionstypen

Der Funktionstyp basiert hauptsächlich auf einem Statusumschalter. Das Einschalten zeigt an, dass das Konfigurationselement im aktuellen Bereich aktiviert ist; das Ausschalten zeigt an, dass es deaktiviert oder nicht aktiviert ist. Einige Schlüssel haben umgekehrte Semantik und sollten gemäß dem Namen und der Beschreibung des Konfigurationselements bestimmt werden. Zum Beispiel können Schlüssel mit Namen, die 'unsupport' oder 'disable' enthalten, beim Einschalten 'nicht unterstützt' oder 'deaktiviert' darstellen.

### 3.4 Speichern, Löschen und Wiederherstellen

- Vor dem Speichern den Unternehmensbereich, den Schlüsselnamen, den Typ, die Einheit und die geänderten Werte erneut bestätigen.
- Nach dem Speichern dasselbe Konfigurationselement erneut suchen, um zu bestätigen, dass Quelle, Status und wirksamer Wert geändert wurden.
- Wenn es eine Überschreibung im aktuellen Bereich gibt, ist die 'Löschen'-Operation möglicherweise verfügbar; nach dem Löschen der Überschreibung wird der geerbte Wert der vorherigen Ebene wiederhergestellt.
- Wenn ein Rollback erforderlich ist, zuerst den ursprünglich aufgezeichneten Wert zurückschreiben oder die aktuelle Überschreibung nach Bestätigung der Vererbungsbeziehung löschen.
- 'Löschen' nicht als Löschen des Konfigurationselements selbst verstehen; das Löschen auf der Seite bezieht sich normalerweise nur auf den Überschreibungsdatensatz im aktuellen Bereich.

## 4. Wirksamkeitsprüfung und Rollback

### 4.1 Überprüfung nach dem Speichern

- Auf der Systemkonfigurationsseite denselben Unternehmensbereich und dasselbe Konfigurationselement erneut abfragen, um Quelle, Status und wirksamen Wert zu bestätigen.
- Zur funktionalen Seite gehen, die diese Konfiguration tatsächlich verwendet, um die Leistung der Funktion zu überprüfen, anstatt nur das Konfigurations-Backend anzusehen.
- Für die globale Konfiguration sollte mindestens ein Unternehmen ohne Konfiguration auf Unternehmensebene stichprobenartig überprüft werden; die Konfiguration auf Unternehmensebene wird nur für das Zielunternehmen überprüft.
- Bei kontobezogenen, Berechtigungs- oder Cache-Einstellungen die Seite aktualisieren, erneut einloggen oder bei Bedarf auf die Aktualisierung des Caches warten.
- Die Änderungszeit, den Betreiber, den Unternehmensbereich, den Schlüsselname, die Werte vor und nach der Änderung sowie die Prüfergebnisse aufzeichnen.

### 4.2 Rollback

- Es gibt einen definierten Originalwert: erneut bearbeiten und den Originalwert zurückschreiben.
- Es muss nur die aktuelle Bereichsüberschreibung entfernt werden: 'Systemstandards wiederherstellen' verwenden oder die aktuelle Überschreibung löschen.
- Nach dem Zurücksetzen die Quell- und Wirksamkeitswerte erneut abfragen und die Geschäftsseite zur Überprüfung wieder aufrufen.
- Wenn globale Änderungen weitreichende Unregelmäßigkeiten verursachen, sollte vorrangig die globale Abdeckung wiederhergestellt werden, und dann die Unterschiede in der Abdeckung einzelner Unternehmen untersucht werden.

**Wichtiger Hinweis** Löschungen auf der Seite beziehen sich normalerweise auf die Überschreibungsdatensätze innerhalb des aktuellen Bereichs; das Konfigurationselement selbst bleibt bestehen. Es ist notwendig sicherzustellen, dass der vererbte Wert den Erwartungen entspricht, bevor gelöscht wird.

## 5. Häufig gestellte Fragen

| **Frage** | **Vorgehensweise** |
| --- | --- |
| Bearbeitungs- oder Aktionsbutton kann nicht gefunden werden | Wenn die Tabelle breit ist, horizontal nach ganz rechts scrollen; man kann auch den sichtbaren Bereich des Browsers vergrößern. |
| Keine Ergebnisse bei exakter Suche | Überprüfen Sie Groß-/Kleinschreibung und Unterstriche des Schlüsselnamens; bestätigen Sie den Unternehmens-ID-Bereich; übermäßig strenge Typ-, Werttyp- oder Wirksamkeitsende-Filter löschen. |
| Nach dem Speichern hat die Geschäftsseite keine Änderungen | Überprüfen Sie, ob der falsche Unternehmensbereich ausgewählt wurde, ob die Quelle vom Unternehmen überschrieben wird, ob eine Aktualisierung oder erneute Anmeldung erforderlich ist, und bestätigen Sie, ob die Konfigurationselemente für die aktuelle Funktion anwendbar sind. |
| Systemstandard-Wiederherstellungsbutton nicht verfügbar | Der aktuelle Bereich hat keine Überschreibungen, verwendet derzeit geerbte Werte oder Systemstandardwerte. |
| JSON oder URL Konfigurationsfehler | Gültig beibehalten JSON oder URL Format, keine Anführungszeichen, Kommas oder Protokolle auslassen; zuerst im Testunternehmen überprüfen. |
| Endgültiger effektiver Unternehmenswert unterscheidet sich vom globalen Wert | Das aktuelle Unternehmen kann Überschreibungen haben. Überprüfen Sie Quelle und Überschreibungsaufzeichnungen, um zu bestätigen, ob Unternehmensunterschiede beibehalten oder die Vererbung wiederhergestellt werden soll. |

## Anhang A: System-Konfigurationselement-Index

Der folgende Index listet nur Systemkonfigurationselemente auf, die auf der aktuellen Seite abgefragt und geändert werden können; der spezifische sichtbare Bereich hängt von der aktuellen Bereitstellungsversion und der tatsächlichen Seitenanzeige ab.

| **Konfigurationsschlüssel** | **Konfigurationselementname** | **Typ/Regel** | **Seitenunterstützung** |
| --- | --- | --- | --- |
| erlauben_Team_Admin_holen_eingeladen_Benutzer_Passwort | Unternehmensadmin erhält die Initialwerte PASSWORD der eingeladenen Benutzer | Leere Zeichenkette | Auf der Seite konfigurierbar |
| auto_Anmeldung_ein_nein_Berechtigung_Seite | Anonymer Zugriff ohne Berechtigung wird zur Anmeldeseite umgeleitet | Leere Zeichenkette | Auf der Seite konfigurierbar |
| Batch_löschen_Datei_Anzahl_Limit | Maximale Anzahl von Dateien für die Batch-Löschung | 0–500 | Auf der Seite konfigurierbar |
| Batch_herunterladen_Dateien | Maximale Anzahl von Dateien für den einzelnen Batch-Download | 0–500 | Auf der Seite konfigurierbar |
| Batch_herunterladen_Größe | Maximale Gesamtgröße für den einzelnen Batch-Download | 0–21474836480 | Auf der Seite konfigurierbar |
| Batch_verschieben_Datei_Anzahl_Limit | Maximale Anzahl von Dateien für das Batch-Verschieben | 0–500 | Auf der Seite konfigurierbar |
| Marke | Front-End-Markenname | Leere Zeichenkette | Auf der Seite konfigurierbar |
| ändern_Ordner_Mitarbeiter | Ordner-Zusammenarbeit | Leere Zeichenkette | Auf der Seite konfigurierbar |
| Klassifizierung_Markierung_Konfiguration_Limit | Maximale Anzahl von Downgrade-Genehmigungsrichtlinien | 0–30 | Auf der Seite konfigurierbar |
| Klassifizierung_Markierung_Limit | Maximale Anzahl von Klassifizierungskennzeichen | 0–20 | Auf der Seite konfigurierbar |
| Klassifizierung_Markierung_Regel_Limit | Maximale Anzahl von Klassifizierungskennzeichen-Regeln | 0–30 | Auf der Seite konfigurierbar |
| Cloud_Team_Speicherplatz_herunterladen_Datei_Größe | Maximale Größe einer einzelnen heruntergeladenen Datei (MB) | 0–3072 | Auf der Seite konfigurierbar |
| Cloud_Team_Speicherplatz_hochladen_Datei_Größe | Datei-Upload-Limit für Team-Speicherplatz | 0–300 | Auf der Seite konfigurierbar |
| Tag_entpacken_Datei_Anzahl_Limit | Maximale Anzahl von Dateien, die pro Tag entpackt werden können | 0–2000 | Auf der Seite konfigurierbar |
| Standard_Avatar | Standard-Avatar URL | Pfad | Auf der Seite konfigurierbar |
| Standard_Unternehmen_Papierkorb_Kontingent | Standard-Kontingent des Unternehmens-Papierkorbs | 0–0 | Auf der Seite konfigurierbar |
| Standard_Speicherplatz_Kontingent | Standard-Kontingent für Team-Speicherplatz | 0–107374182400 | Auf der Seite konfigurierbar |
| Standard_Team_Benutzer_Kontingent | Standard-Kapazitätslimit für Unternehmensmitglieder | 0–0 | Auf der Seite konfigurierbar |
| Standard_Benutzer_Datei_Tags | Standard-Tags für Benutzerdateien | JSON Array | Auf der Seite konfigurierbar |
| Standard_Benutzer_Kontingent | Standardkontingent des persönlichen Speichers im Team (Mein Desktop) | 0–107374182400 | Auf der Seite konfigurierbar |
| Abteilung_Anzahl_Limit | Maximale Anzahl von Abteilungen, die im Unternehmen erstellt werden können | 0–500 | Auf der Seite konfigurierbar |
| Abteilung_Tiefe_Limit | Maximale Anzahl der verschachtelten Abteilungsebenen | 0–20 | Auf der Seite konfigurierbar |
| deaktivieren_Batch_herunterladen | Batch-Download deaktivieren | Leere Zeichenkette | Auf der Seite konfigurierbar |
| deaktivieren_Unternehmen_Papierkorb | Unternehmens-Papierkorb ausblenden | Leere Zeichenkette | Auf der Seite konfigurierbar |
| anzeigen_IP_Standort | IP-Standort anzeigen | Leere Zeichenkette | Auf der Seite konfigurierbar |
| Laufwerk_Editor_Über_Marke_Sichtbar | Markeninformationen anzeigen auf ShimoDocs Suite Editor-Über-Seite | Leere Zeichenkette | Auf der Seite konfigurierbar |
| Laufwerk_Editor_Über_Eintrag_Sichtbar | „Über“-Eintrag im Editor anzeigen ShimoDocs Suite Editor | Leere Zeichenkette | Auf der Seite konfigurierbar |
| Laufwerk_Editor_Offiziell_Webseite_Eintrag_Sichtbar | ShimoDocs Suite Editor Offizielle Website Eintrag anzeigen | Leerer String | Seite konfigurierbar |
| aktivieren_Link_Bericht | Externer Link-Bericht | Leerer String | Seite konfigurierbar |
| aktivieren_Außenseiter | Externe Mitarbeiter | Leerer String | Seite konfigurierbar |
| aktivieren_PC_System_Thema | aktivieren_PC_System_Thema | Leerer String | Seite konfigurierbar |
| aktivieren_rdoc_MD_Image_Export_Optionen | aktivieren_rdoc_MD_Image_Export_Optionen | Leerer String | Seite konfigurierbar |
| aktivieren_Risiken | Risikobewertung | Leerer String | Seite konfigurierbar |
| aktivieren_teilen_ablaufen_Zeit | Ablaufzeit des Freigabelinks | Leerer String | Seite konfigurierbar |
| aktivieren_teilen_Passwort | Freigabepasswort | Leerer String | Seite konfigurierbar |
| Datei_Mitarbeiter_Limit | Maximale Anzahl von Mitarbeitern pro Datei | 0–100 | Seite konfigurierbar |
| Ordner_Unterordner_Anzahl_Limit | Maximale Anzahl von Dateien auf derselben Ebene | 0–2000 | Seite konfigurierbar |
| kostenlos_Benutzer_erstellen_Limit | Begrenzung der Anzahl von Vorlagen, die kostenlose Benutzer erstellen können | 0–5 | Seite konfigurierbar |
| Frontend_Laufzeit_Funktionen | Liste der Frontend-Laufzeitkonfigurationsobjekte | JSON Array | Auf der Seite konfigurierbar |
| importieren_Benutzer_Zeilen_Limit | Maximale Anzahl von Benutzern, die gleichzeitig importiert werden können | 0–500 | Auf der Seite konfigurierbar |
| einladen_mobil_limit_abgelaufen | Ablauffenster für die Anzahl der über das Mobiltelefon gesendeten Datei-Kollaborationseinladungen | 0–3600 | Auf der Seite konfigurierbar |
| einladen_mobil_limit_max | Begrenzung der Anzahl der Datei-Kollaborationseinladungen über das Mobiltelefon | 0–20 | Auf der Seite konfigurierbar |
| ist_offen_Rolle_anwenden | Dateiberechtigungsanwendung | Leere Zeichenkette | Auf der Seite konfigurierbar |
| Anmeldung_Gerät_Limit | Maximale Anzahl gleichzeitig angemeldeter Geräte pro Konto | 0–0 | Auf der Seite konfigurierbar |
| max_Ersteller_Teams_pro_Konto | Maximale Anzahl von Unternehmen, die pro Konto erstellt werden können | 0–3 | Auf der Seite konfigurierbar |
| max_Ordner_Tiefe | Maximale Ordner-Verschachtelungstiefe | 0–50 | Auf der Seite konfigurierbar |
| max_beigetreten_Teams_pro_Konto | Maximale Anzahl von Unternehmen, denen ein Konto beitreten kann | 0–100 | Auf der Seite konfigurierbar |
| max_Papierkörbe_Liste_Größe | Anzahl der von der Papierkorb-Listen-Schnittstelle zurückgegebenen Datensätze | 0–500 | Auf der Seite konfigurierbar |
| Mehrteiliger Upload_hochladen_aktivieren | Multipart-Upload | Numerische Zeichenfolge | Auf der Seite konfigurierbar |
| einmal_entpacken_Datei_Anzahl_Limit | Maximale Anzahl von Dateien pro Extraktion | 0–500 | Auf der Seite konfigurierbar |
| nur_Eigentümer_kann_löschen | Nur Eigentümer kann löschen | Leere Zeichenkette | Auf der Seite konfigurierbar |
| Premium_Benutzer_erstellen_Limit | Maximale Anzahl der Vorlagen, die ein Benutzer erstellen kann | 0–50 | Auf der Seite konfigurierbar |
| privat_bereitstellen_Seite_Symbol | Seitensymbolkonfiguration | Leere Zeichenkette | Auf der Seite konfigurierbar |
| öffentlich_teilen | Öffentliches Teilen | Leere Zeichenkette | Auf der Seite konfigurierbar |
| rag_suche_Regel | RAG Suchregeln | JSON Objekt | Auf der Seite konfigurierbar |
| sdkCheckpointCacheTTL | Dauer des Editor-Konfigurationscaches | 0–600 | Auf der Seite konfigurierbar |
| SDK_Checkpoint_Whitelist | Whitelist der Editor-Konfiguration | JSON Objekt | Auf der Seite konfigurierbar |
| suche_KI_aktivieren | suche_KI_aktivieren | Leere Zeichenkette | Auf der Seite konfigurierbar |
| Teilen_Passwort_Länge | Länge des Freigabepassworts | 0–6 | Auf der Seite konfigurierbar |
| einzeln_Datei_hochladen_Größe_Limit | Maximale Größe einer einzelnen hochgeladenen Datei (GB) | 0–1 | Auf der Seite konfigurierbar |
| einzeln_hochladen_Datei_Anzahl_Limit | Batch-Upload | Leerer String | Auf Seite konfigurierbar |
| Team_Ändern | Teamänderung | Leerer String | Auf Seite konfigurierbar |
| Team_Rolle_Verwalten | Rollenverwaltung | Leerer String | Auf Seite konfigurierbar |
| Thema_Farbe | Frontend-Themenfarbe | Leerer String | Auf Seite konfigurierbar |
| Thema_Farbe_Button | Button-Themenfarbe | HEX Farbwert | Auf Seite konfigurierbar |
| UI_Radius_Konfiguration | Frontend-Randradius-Konfiguration | Leerer String | Auf Seite konfigurierbar |
| Hochladen_Batch_max | Maximale Anzahl von Dateien pro Upload | 0–500 | Auf Seite konfigurierbar |

## Anhang B: Terminologie und Seitenfeldzuordnung

| **Begriff** | **Bedeutung** |
| --- | --- |
| Konfigurationsschlüssel / Schlüsselname | Der eindeutige Schlüssel des Konfigurationselements, zum Beispiel batch_herunterladen_Dateien. |
| Unternehmens-ID | Unternehmenskennung. Wenn ausgewählt, tritt die Konfigurationsbereich auf Unternehmensebene in Kraft. |
| Globale Konfiguration | Standardbereich, der abgefragt und geändert wird, wenn die Unternehmens-ID leer gelassen wird. |
| Konfiguration auf Unternehmensebene | Überschreibungen gelten nur für das ausgewählte Unternehmen. |
| Systemstandard | Wenn im aktuellen Bereich keine benutzerdefinierte Überschreibung existiert, wird der integrierte Standardwert verwendet. |
| Globale Versionsüberschreibung | Das aktuelle Konfigurationselement hat benutzerdefinierte Einstellungen auf globaler Ebene. |
| Ergebnis auf Unternehmensebene | Das tatsächlich wirksame Ergebnis nach Zusammenführung des Unternehmensstandardwerts mit der Unternehmensüberschreibung. |
| Schlüssel-Wert | Ein Einzelwertparameter, der in Form einer Zeichenkette gespeichert ist und Text, URL, Pfad oder JSON. |
| Quote | Ein numerischer Bereich, der Mindest-, Höchstwerte oder einen Grenzschalter umfasst. |
| Funktion | Ein Schalter- oder Status-Parameter. |
| Keine Validierung | Führt keine Validierungsprüfungen basierend auf dem angegebenen oberen Limit durch. |
