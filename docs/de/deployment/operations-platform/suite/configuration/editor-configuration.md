# Editor-Konfiguration

[← ShimoDocs Suite Bereitstellungsdokumentation](../../../README.md)

## 1. Handbuchanweisungen

Dieses Handbuch führt in die Funktion „Editor-Konfiguration“ von ShimoDocs Suiteein und eignet sich für Systemadministratoren und Implementierer, die diese Funktion zum ersten Mal verwenden. Sie können den Schritten in diesem Dokument folgen, um Konfigurationselemente zu finden, Funktionsschalter oder Kontingente zu ändern, zu überprüfen, ob sie wirksam sind, und bei Bedarf die ursprünglichen Einstellungen wiederherzustellen.

**Wichtigste Bereichsregel** Wenn die Team-ID leer gelassen wird, wird die Standardanwendungskonfiguration abgefragt und geändert; wenn eine Team-ID eingegeben wird, wird die Konfiguration für das entsprechende Team abgefragt und geändert. Das Ändern der Standardanwendungskonfiguration kann mehrere Teams beeinflussen, daher bestätigen Sie bitte den Konfigurationsbereich erneut, bevor Sie speichern.

### 1.1 Zugangsweg

Admin-Backend > ShimoDocs Suite > Konfigurationsverwaltung > Editor-Konfiguration

### 1.2 Vorbereitungen vor der Nutzung

- Bestätigen Sie, dass Ihr Login-Konto die Berechtigung hat, Editor-Konfigurationen anzusehen und zu ändern. ShimoDocs Suite 
- Bestätigen Sie zunächst, ob der Zielbereich die Standardanwendung oder ein bestimmtes Team ist, und beschaffen Sie die genaue Team-ID.
- Bestätigen Sie die Namen der Konfigurationselemente aus den Konfigurationsanforderungen oder Anhang A. Die Namen der Konfigurationselemente sind eindeutige Bezeichner; raten Sie nicht nur anhand der chinesischen Funktionsnamen.
- Notieren Sie die Quelle, die effektiven Werte und den Einschränkungsstatus vor der Änderung; für wichtige Konfigurationen bereiten Sie auch die Rücksetzwerte vor.
- Standardanwendungskonfigurationen haben eine breite Auswirkung; es wird empfohlen, sie in Zeiten mit geringem Geschäftsbetrieb zu ändern und die zuständigen Personen im Voraus zu benachrichtigen.

## 2. Konfigurationsumfang und Priorität

Die Editor-Konfiguration unterstützt zwei Bereiche: App-Standard und Team. Vor Änderungen müssen Sie sowohl die Team-ID als auch die 'Aktuelle Dimension' oben auf der Seite überprüfen.

| **Funktionsbereich** | **App-Standardbereich** | **Team-Bereich** | **Seiten-Erkennungsanzeige** |
| --- | --- | --- | --- |
| Editor-Konfiguration | Team-ID leer lassen | Geben Sie eine positive Ganzzahl als Team-ID ein | Aktuelle Dimension zeigt 'App-Standard' oder 'Team' |

*Abbildung 1 Korrespondenz zwischen Team-ID und aktueller Dimension*

### 2.1 Anwendungsstandardbereich

- Lassen Sie die Team-ID leer.
- Oben auf der Seite wird „Aktuelle Dimension: Anwendungsstandard“ angezeigt.
- Der Anwendungsstandardwert ist der Basiswert, wenn keine Teamüberschreibung gesetzt ist; eine Änderung kann mehrere Teams beeinflussen.
- Vor dem Speichern bestätigen Sie erneut, dass die Team-ID tatsächlich leer ist, um zu vermeiden, dass Team-Anforderungen versehentlich als Standardanwendungseinstellungen geschrieben werden.

### 2.2 Teamumfang

- Geben Sie die positive Ganzzahl-ID des Zielteams in das Feld Team-ID ein und klicken Sie dann auf „Abfragen“.
- Oben auf der Seite wird „Aktuelle Dimension: Team“ angezeigt.
- Die Konfiguration auf Teamebene betrifft nur das Team, das der eingegebenen Team-ID entspricht, und ändert die Konfiguration anderer Teams nicht direkt.
- Wenn ein Konfigurationselement eine Einstellung auf Teamebene hat, hat der teamwirksame Wert Vorrang vor dem Standardwert der Anwendung.

### 2.3 Überschreiben, Vererbung und Wiederherstellung

- Wenn das aktuelle Team keine benutzerdefinierte Überschreibung hat, wird die Standardkonfiguration der Anwendung oder der Systemstandardwert verwendet.
- Die „Quelle“ in der Liste kann helfen festzustellen, ob der aktuelle Wert vom Systemstandard, von der Anwendungsüberschreibung oder von der Teamüberschreibung stammt.
- Nach dem Löschen der Überschreibung der aktuellen Ebene wird die Konfiguration normalerweise wieder vom oberen Wert übernommen; bestätigen Sie das Ergebnis nach der Vererbung vor dem Löschen.
- Nach dem Ändern oder Wiederherstellen führen Sie eine erneute Abfrage derselben Team-ID und des Konfigurationselements durch, um zu bestätigen, dass Quelle und wirksamer Wert den Erwartungen entsprechen.

**Risikohinweis** Speichern Sie nicht direkt, ohne die aktuelle Dimension zu bestätigen. Wenn die Team-ID leer ist, werden Operationen im Standardscope der Anwendung geschrieben, was mehrere Teams beeinflussen kann. 

## 3. Editor-Konfiguration 

Die Editor-Konfiguration wird verwendet, um die Funktionsschalter, Nutzungskontingente und die strukturierte Konfiguration der ShimoDocs Suite Editor. Sie können nach Typ filtern oder den "Erweiterten Filter" erweitern und den Namen des Konfigurationselements in die "Namens-Whitelist" eingeben, um eine präzise Suche durchzuführen. 

### 3.1 Seitenfelder 

| **Feld** | **Beschreibung** | 
| --- | --- | 
| App-ID | Aktuell ShimoDocs Suite Anwendungskennung, wird nur verwendet, um den Kontext zu bestätigen. | 
| Aktuelle Dimension | "Standardeinstellung der Anwendung", wenn Team-ID leer ist; "Team", nachdem die Team-ID eingetragen wurde. | 
| Team-ID | Team-Kennung; akzeptiert nur positive Ganzzahlen. | 
| Typ | Optionen umfassen Alle, Feature, Einzelwert-Quota, Bereichs-Quota oder JSON Konfiguration. | 
| Erweiterter Filter | Erweitern Sie das Eingabefeld für Konfigurationselement-Namen. | 
| Namens-Whitelist | Geben Sie Konfigurationselement-Namen ein; unterstützt einen pro Zeile oder durch englische Kommas getrennt. | 
| Quelle | Gibt an, ob der aktuelle Wert aus den Systemstandards, der Anwendungsüberschreibung oder der Teamüberschreibung stammt. | 
| Effektiver Wert | Der tatsächlich verwendete Schalter, die Quota oder die strukturierte Konfiguration im aktuellen Bereich. | 
| Aktion | Stiftsymbol zum Bearbeiten; Löschsymbol zum Entfernen der aktuellen Ebenenüberschreibung. | 

*Abbildung 2 Editor-Konfigurationsabfragebereich, Ergebnisliste und Aktionsspalte*

### 3.2 Präzise Suche

1. Entscheiden Sie, ob die Team-ID basierend auf dem Konfigurationsbereich ausgefüllt wird: leer lassen für den Standardbereich der Anwendung oder für das entsprechende Team ausfüllen.
2. Wählen Sie den "Typ" nach Bedarf; wenn Sie sich über den Typ unsicher sind, behalten Sie "Alle" bei.
3. Klicken Sie auf "Erweiterter Filter", um die "Namens-Whitelist" zu erweitern.
4. Geben Sie den vollständigen Namen des Konfigurationselements ein. Für mehrere Namen geben Sie einen pro Zeile ein oder trennen Sie sie mit Kommas.
5. Klicken Sie auf "Abfrage", um die Namen, Typen, Quellen und effektiven Werte in den Ergebnissen zu überprüfen.
6. Klicken Sie auf das Stiftsymbol ganz rechts in der Zielfeldzeile, um das Bearbeitungs-Popup zu öffnen.

*Abbildung 3 Füllen Sie die Namens-Whitelist aus, nachdem Sie auf 'Erweiterter Filter' geklickt haben*

*Abbildung 4 Einzelnes Ergebnis nach genauer Suche zur Bearbeitung_limit_mosheet_Größe*

**Bedienungstipps** Wenn Sie die Aktionssymbole nicht sehen, scrollen Sie bitte die Liste horizontal nach ganz rechts oder vergrößern Sie den sichtbaren Bereich des Browsers.

### 3.3 Direktes Suchen in der Liste

- Überprüfen Sie zuerst, ob die Team-ID- und Typfilter korrekt sind, und scrollen Sie dann durch die Abfrageergebnisse.
- Verwenden Sie sowohl "Name" als auch "Typ", um die Zielkonfiguration zu bestätigen; verlassen Sie sich nicht nur auf den aktuellen Wert.
- Die Anzahl der auf der Seite angezeigten Datensätze kann je nach Bereitstellungsversion und den von der aktuellen Anwendung unterstützten Konfigurationselementen variieren.
- Nachdem Sie die Zielfeldzeile gefunden haben, klicken Sie auf das Stiftsymbol ganz rechts, um in den Bearbeitungsmodus zu wechseln.

### 3.4 Konfiguration bearbeiten

#### 3.4.1 Funktion

Der Funktionstyp wird verwendet, um zu steuern, ob eine Funktion verfügbar ist. Nach dem Öffnen des Bearbeitungs-Pop-ups wählen Sie den auf der Seite bereitgestellten Status wie "Unterstützt" oder "Verbergen" aus dem Dropdown-Menü "Effektiver Wert" und klicken dann auf "Speichern". Einige Konfigurationselementnamen enthalten gegensätzliche Bedeutungen wie unsupport oder disable, daher beurteilen Sie bitte die tatsächliche Bedeutung anhand des Elementnamens und der Beschreibung.

*Abbildung 5  Effektive Wert-Einstellungen von Funktionstyp-Konfigurationselementen*

#### 3.4.2 Einzelwert-Quote

Eine Einzelwert-Quote beinhaltet in der Regel einen „Limit-Prüfung“-Schalter und einen „Maximalwert“. Wenn die Limit-Prüfung aktiviert ist, wird das System gemäß dem Maximalwert validieren; ist sie deaktiviert, wird in der Regel „unbegrenzt“ angezeigt. Der Maximalwert muss innerhalb des erlaubten Bereichs des Parameters liegen und mit der Einheit im Parameternamen übereinstimmen, wie MB, GB, Seiten, Elemente oder Zeichen.

*Abbildung 6 Validierung der Einzelwert-Quote und Festlegung des Maximalwerts*

#### 3.4.3 Bereichs-Quote

- Bereichs-Quoten bieten normalerweise sowohl einen Mindest- als auch einen Maximalwert.
- Der Mindestwert darf nicht größer als der Maximalwert sein, und der Eingabewert sollte innerhalb des auf der Seite oder im Anhang angegebenen erlaubten Bereichs liegen.
- Wenn die Seite eine Option „Keine Validierung“ oder „Keine Begrenzung“ bietet, bestätigen Sie zuerst, ob die aktuelle Funktion diese Einstellung unterstützt.
- Nach dem Speichern die Grenzwerte in der tatsächlichen Geschäftsfunktionsüberprüfung verifizieren, um zu vermeiden, dass nur die Anzeige im Konfigurations-Backend überprüft wird.

#### 3.4.4 JSON Konfiguration

- JSON Die Konfiguration muss eine gültige Struktur beibehalten, einschließlich gepaarter Anführungszeichen, Kommas, Klammern und korrekter Datentypen.
- Speichern Sie den vollständigen Originalwert, bevor Sie Änderungen vornehmen; nicht nur ein Feld aufzeichnen.
- Wenn die Bedeutung eines Feldes unklar ist, fügen Sie keine Felder hinzu, löschen Sie keine oder benennen Sie Felder nicht willkürlich um.

### 3.5 Speichern und Löschen

- Vor dem Speichern die aktuelle Dimension, Team-ID, den Konfigurationselementnamen, Typ, Einheit und neuen Wert erneut bestätigen.
- Nach dem Speichern die gleiche Bereichsabfrage und denselben Konfigurationseintrag erneut durchführen, um zu bestätigen, dass die Quell- und Effektivwerte aktualisiert wurden.
- Das Löschsymbol wird normalerweise verwendet, um den Überschreibungsdatensatz des aktuellen Bereichs zu entfernen, nicht um den Konfigurationseintrag selbst zu löschen.
- Dieses Handbuch listet nur die Konfigurationseinträge auf, die auf der aktuellen Seite abgefragt und geändert werden können; die tatsächlich angezeigten Elemente können je nach Bereitstellungsversion und den aktuellen Anwendungssupportfunktionen variieren.

### 3.6 Beschreibung des Konfigurationseintrags

Anhang A umfasst nur die Editor-Konfigurationseinträge, die auf der aktuellen Seite abgefragt und geändert werden können; der tatsächlich sichtbare Bereich richtet sich nach der aktuellen Bereitstellungsversion und der tatsächlichen Anzeige der Seite.

## 4. Effektprüfung und Zurücksetzung

### 4.1 Überprüfung nach dem Speichern

- Auf der Konfigurationsseite des Editors die gleiche Team-ID und denselben Konfigurationseintrag erneut abfragen, um die Quelle, den effektiven Wert und den Einschränkungsstatus zu bestätigen.
- Die Seite des Editors oder der Funktion aufrufen, die diese Konfiguration tatsächlich verwendet, um zu überprüfen, ob die Funktion sichtbar ist, das Kontingent wirksam ist oder die Einschränkung aufgehoben wurde.
- Bei der Anwendung der Standardkonfiguration mindestens ein Team überprüfen, das keine Teamkonfiguration gesetzt hat; bei Teamkonfigurationen nur die Ziel-Team-ID überprüfen.
- Seite aktualisieren, den Editor erneut aufrufen, sich erneut anmelden oder bei Bedarf auf Cache-Updates warten.
- Zeitpunkt der Änderung, Bearbeiter, Konfigurationsumfang, Team-ID, Name des Konfigurationseintrags, Werte vor und nach der Änderung sowie Prüfergebnisse protokollieren.

### 4.2 Rollback

- Wenn der Ursprungswert aufgezeichnet wurde, erneut bearbeiten und den ursprünglichen Wert zurückschreiben.
- Wenn nur die aktuelle Bereichsüberschreibung entfernt werden muss, verwenden Sie das Löschsymbol und bestätigen Sie den geerbten Wert vom oberen Niveau nach der Löschung.
- Nach dem Rollback die Quelle und den effektiven Wert erneut abfragen und die Geschäftsseite erneut zur Überprüfung betreten.
- Wenn die Anwendung der Standardkonfigurationsänderung Anomalien verursacht, stellen Sie zuerst den Standardwert wieder her und prüfen Sie dann, ob ein Team unabhängige Überschreibungen hat.

**Wichtiger Hinweis** Nach dem Löschen der aktuellen Überschreibung kann der geerbte Wert sofort auf der Seite erscheinen. Vor der Löschung muss die Konfiguration auf höherer Ebene bestätigt werden, um die Erwartungen zu erfüllen, und ein Protokoll des Voränderungszustands sollte aufbewahrt werden.

## 5. Häufig gestellte Fragen

| **Problem** | **Lösung** |
| --- | --- |
| Konfigurations-Element-Name-Eingabefeld nicht gefunden | Klicken Sie auf „Erweiterter Filter“, um die „Namens-Whitelist“ zu erweitern. |
| Bearbeitungs- oder Löschsymbol nicht gefunden | Horizontal die Liste ganz nach rechts scrollen oder den Browser-Ansichtsbereich erweitern. |
| Exakte Suche liefert keine Ergebnisse | Überprüfen Sie die Groß-/Kleinschreibung des Namens, Unterstriche, Team-ID und Typfilter; entfernen Sie zu strenge Filterbedingungen und versuchen Sie es erneut. |
| Nach Eingabe der Team-ID ist sie immer noch nicht in der Team-Dimension | Die Team-ID muss eine gültige positive Ganzzahl sein; nach Eingabe erneut auf „Abfragen“ klicken und oben auf der Seite die „Aktuelle Dimension“ überprüfen. |
| Nach dem Speichern ändert sich die Geschäftsseite nicht | Überprüfen Sie, ob der falsche Bereich ausgewählt ist, ob es vom Team überschrieben wird, ob ein Aktualisieren oder erneutes Anmelden erforderlich ist und ob das Konfigurationselement auf die aktuelle Funktion angewendet wird. |
| Löschsymbol ist nicht verfügbar | Der aktuelle Bereich darf keine benutzerdefinierten Überschreibungen haben und verwendet den Systemstandard oder einen von einer höheren Ebene vererbten Wert. |
| Speichern des Kontingents fehlgeschlagen | Überprüfen Sie den Wertebereich, die Einheit, die Beziehung zwischen Minimal- und Maximalwerten und bestätigen Sie, ob „Unbegrenzt“ erlaubt ist. |
| JSON Speichern der Konfiguration fehlgeschlagen | Verwenden Sie gültig JSON; überprüfen Sie Anführungszeichen, Kommas, Klammern und Feldtypen; wenn unsicher, stellen Sie den vollständigen Originalwert vor der Änderung wieder her. |

## Anhang A: Index der Editor-Konfigurationselemente

Der folgende Index listet nur die Editor-Konfigurationselemente auf, die auf der aktuellen Seite abgefragt und geändert werden können; der spezifisch sichtbare Bereich hängt von der derzeit bereitgestellten Version ab.

| **Konfigurationselementname** | **Kategorie / Funktionsbeschreibung** | **Typ** | **Standardwert / Optionaler Bereich** | **Konfigurationsmethode** |
| --- | --- | --- | --- | --- |
| exportieren_modoc_docx | Export | Funktionsschalter | Ein | Auf Seite konfigurierbar |
| exportieren_modoc_img | Export | Funktionsschalter | Ein | Auf Seite konfigurierbar |
| exportieren_modoc_pdf | Export | Funktionsschalter | Ein | Auf Seite konfigurierbar |
| exportieren_modoc_pdf_img | Export | Funktionsschalter | Ein | Auf Seite konfigurierbar |
| exportieren_modoc_wps | Export | Funktionsschalter | Ein | Auf Seite konfigurierbar |
| exportieren_mosheet_img | Export | Funktionsschalter | Ein | Auf Seite konfigurierbar |
| exportieren_mosheet_pdf_img | Export | Funktionsschalter | Ein | Auf Seite konfigurierbar |
| exportieren_mosheet_einzeln_Tabelle_csv | Export | Funktionsschalter | Ein | Auf Seite konfigurierbar |
| exportieren_mosheet_einzeln_Tabelle_pdf_img | Export | Funktionsschalter | Ein | Auf Seite konfigurierbar |
| exportieren_mosheet_einzeln_Tabelle_xlsx | Export | Funktionsschalter | Ein | Auf Seite konfigurierbar |
| exportieren_mosheet_xlsx | Export / Tabelle | Funktionsschalter | Ein | Auf Seite konfigurierbar |
| exportieren_mosheet_zip | Export | Funktionsschalter | Ein | Seite konfigurierbar |
| exportieren_Präsentation_img | Export | Funktionsschalter | Ein | Seite konfigurierbar |
| exportieren_Präsentation_pdf | Export | Funktionsschalter | Ein | Seite konfigurierbar |
| exportieren_Präsentation_pdf_img | Export | Funktionsschalter | Ein | Seite konfigurierbar |
| exportieren_Präsentation_pptx | Export | Funktionsschalter | Ein | Seite konfigurierbar |
| exportieren_rdoc_docx | Export | Funktionsschalter | Ein | Seite konfigurierbar |
| exportieren_rdoc_img | Export | Funktionsschalter | Ein | Seite konfigurierbar |
| exportieren_rdoc_md | Export | Funktionsschalter | Ein | Seite konfigurierbar |
| exportieren_rdoc_pdf | Export | Funktionsschalter | Ein | Seite konfigurierbar |
| exportieren_Tabelle_xlsx | Export / Anwendungstabelle | Funktionsschalter | Ein | Seite konfigurierbar |
| Formular_Benachrichtigung | Formularbearbeitung / Benachrichtigungswarnungen einstellen (Antwortwarnungen, Abonnement-Updates) | Funktionsschalter | Ein | Seite konfigurierbar |
| importieren_konvertieren_svg | Import / Hochladen / Erzwingung der Konvertierung des Anhangsformats | Funktionsschalter | Ein | Seite konfigurierbar |
| importieren_Mindmap_xmind | Import/Hochladen / Mindmap | Funktionsschalter | Ein | Seite konfigurierbar |
| importieren_modoc_doc | Import/Hochladen | Funktionsschalter | Ein | Seite konfigurierbar |
| importieren_modoc_docx | Import/Hochladen | Funktionsschalter | Ein | Seite konfigurierbar |
| importieren_modoc_wps | Import/Hochladen | Funktionsschalter | Ein | Seite konfigurierbar |
| importieren_modoc_wpt | Import/Hochladen | Funktionsschalter | Ein | Seite konfigurierbar |
| importieren_mosheet_csv | Import/Hochladen | Funktionsschalter | Ein | Seite konfigurierbar |
| importieren_mosheet_xls | Import/Hochladen | Funktionsschalter | Ein | Seite konfigurierbar |
| importieren_mosheet_xlsm | Import/Hochladen | Funktionsschalter | Ein | Seite konfigurierbar |
| importieren_mosheet_xlsx | Import/Hochladen | Funktionsschalter | Ein | Seite konfigurierbar |
| importieren_Präsentation_ppt | Import/Hochladen | Funktionsschalter | Ein | Seite konfigurierbar |
| importieren_Präsentation_pptx | Import/Hochladen | Funktionsschalter | Ein | Seite konfigurierbar |
| importieren_rdoc_doc | Import/Hochladen | Funktionsschalter | Ein | Seite konfigurierbar |
| importieren_rdoc_docx | Import/Hochladen | Funktionsschalter | Ein | Seite konfigurierbar |
| importieren_rdoc_md | Import/Hochladen | Funktionsschalter | Ein | Seite konfigurierbar |
| importieren_rdoc_txt | Import/Hochladen | Funktionsschalter | Ein | Seite konfigurierbar |
| importieren_Tabelle_csv | Import/Hochladen | Funktionsschalter | Ein | Seite konfigurierbar |
| importieren_Tabelle_xls | Import/Hochladen | Funktionsschalter | Ein | Seite konfigurierbar |
| importieren_Tabelle_xlsx | Import/Hochladen | Funktionsschalter | Ein | Seite konfigurierbar |
| importieren_nicht unterstützt_Anhang_svg | Import/Hochladen | Funktionsschalter | Ein | Seite konfigurierbar |
| importieren_nicht unterstützt_Anhang_xml | Import/Hochladen | Funktionsschalter | Ein | Seite konfigurierbar |
| mosheet_kombinieren_Blätter | Tabellenkalkulation Bearbeitung / Blätter Zusammenführen | Funktionsschalter | Versteckt/Aus | Seite konfigurierbar |
| mosheet_Datum_Erwähnen | Tabellenkalkulation Bearbeitung / Datums-Erinnerung | Funktionsschalter | Ein | Seite konfigurierbar |
| mosheet_Folgen_Modus | Tabellenkalkulation Bearbeitung / Folgen-Modus | Funktionsschalter | Ein | Seite konfigurierbar |
| mosheet_Folgen_Auswahl | Tabellenkalkulation Bearbeitung / Auswahl Folgen | Funktionsschalter | Verstecken/Schließen | Seite konfigurierbar |
| mosheet_Importieren_Bereich | Tabellenkalkulation Bearbeitung / Verweis über Blätter | Funktionsschalter | Verstecken/Schließen | Seite konfigurierbar |
| mosheet_Unabhängig_Ansichtsfenster | Tabellenkalkulation Bearbeitung / Unabhängige Ansicht | Funktionsschalter | Verstecken/Schließen | Seite konfigurierbar |
| Präsentation_Fern_Demo | Folie Bearbeitung / Fernpräsentation | Funktionsschalter | Verstecken/Schließen | Seite konfigurierbar |
| Vorschau_Nicht unterstützt_OFD | Vorschau | Funktionsschalter | Verstecken/Schließen | Seite konfigurierbar |
| Vorschau_Nicht unterstützt_pdf | Vorschau | Funktionsschalter | Verstecken/Schließen | Seite konfigurierbar |
| Vorschau_Nicht unterstützt_RTF | Vorschau / Text (Vorschau unterstützt nicht RTF) | Funktionsschalter | Verstecken/Schließen | Seite konfigurierbar |
| RDOC_Folgen_Modus | Dokument Bearbeitung / Folgen-Modus | Funktionsschalter | Ein | Seite konfigurierbar |
| RDOC_Benachrichtigung | Dokument Bearbeitung / Benachrichtigungen | Funktionsschalter | Ein | Seite konfigurierbar |
| RDOC_Breit_Papier | Dokument Bearbeitung / Breites Papier | Funktionsschalter | Ein | Seite konfigurierbar |
| SDK_Editor_Über_Marke_Sichtbar | Editor Marken-Eintrag | Funktionsschalter | Ein | Auf Seite konfigurierbar |
| SDK_Editor_Über_Eintrag_Sichtbar | Editor Marken-Eintrag | Funktionsschalter | Ein | Auf Seite konfigurierbar |
| SDK_Editor_Offiziell_Webseite_Eintrag_Sichtbar | Editor Marken-Eintrag | Funktionsschalter | Ein | Auf Seite konfigurierbar |
| Tabelle_Verknüpfung_Verweis_oder_Formel | App Tabellenbearbeitung / Feld - Verknüpfter Verweis & Verknüpfte Formel | Funktionsschalter | Versteckt/Aus | Auf Seite konfigurierbar |
| Tabelle_Benachrichtigung | App Tabellenbearbeitung / Datums-Erinnerung | Funktionsschalter | Ein | Auf Seite konfigurierbar |
| Tabelle_Beziehen_Daten | App Tabellenbearbeitung / Referenz-Datentabelle (Zusammengeführte Arbeitsblätter) | Funktionsschalter | Versteckt/Aus | Auf Seite konfigurierbar |
| Hochladen_Image_GIF | Bildformat Hochladen | Funktionsschalter | Ein | Auf Seite konfigurierbar |
| Hochladen_Image_JPEG | Bildformat Hochladen | Funktionsschalter | Ein | Auf Seite konfigurierbar |
| Hochladen_Image_PNG | Bildformat Hochladen | Funktionsschalter | Ein | Auf Seite konfigurierbar |
| Hochladen_Image_TIFF | Bildformat Hochladen | Funktionsschalter | Ein | Auf Seite konfigurierbar |
| Hochladen_Image_WebP | Bildformat Hochladen | Funktionsschalter | Ein | Auf Seite konfigurierbar |
| Anhängen_limit_Alle_IMG_Größe | Anhang Parameter / Maximale Größe für hochgeladene Bilder (MB) | Quote | Standard 512; 0–512 | Auf Seite konfigurierbar |
| Anhängen_limit_Alle_Größe | Anhang Parameter / Maximale Größe für hochgeladene Anhänge (GB) | Quote | Standard 2048; 0–2048 | Auf Seite konfigurierbar |
| Bearbeiten_limit_Form_Größe | Parameter bearbeiten / Maximale bearbeitbare Datenmenge (MB) | Quote | Standard 100; 0–100 | Auf Seite konfigurierbar |
| Bearbeiten_limit_Form_absenden | Parameter bearbeiten / Maximale Anzahl von Einsendungen pro Formular | Quote | Standard 50000; 0–50000 | Auf Seite konfigurierbar |
| Bearbeiten_limit_modoc_Größe | Parameter bearbeiten / Maximale bearbeitbare Datenmenge (MB) | Quote | Standard 100; 0–100 | Auf Seite konfigurierbar |
| Bearbeiten_limit_mosheet_berechnen_Zellen | Parameter bearbeiten / Formel - Tabellenübergreifender Bezug - Maximale Anzahl referenzierter Zellen | Quote | Standard 1500000; 0–1500000; Nicht überprüft | Auf Seite konfigurierbar |
| Bearbeiten_limit_mosheet_berechnen_Komplexität | Parameter bearbeiten / Formel - Tabellenübergreifender Bezug - Komplexität der referenzierten Formeln | Quote | Standard 6000000; 0–6000000; Nicht überprüft | Auf Seite konfigurierbar |
| Bearbeiten_limit_mosheet_Funktion_referenzieren | Parameter bearbeiten / Formel - Maximale Anzahl von eingehenden tabellenübergreifenden Bezug-Funktionen (Einheiten) | Quote | Standard 4000; 0–4000 | Auf der Seite konfigurierbar |
| Bearbeiten_limit_mosheet_Tabelle_Zelle | Parameter bearbeiten / Maximale Anzahl von Zellen in einem einzelnen Arbeitsblatt | Quote | Standard 0; 0–0; Nicht validiert | Auf der Seite konfigurierbar |
| Bearbeiten_limit_mosheet_Tabelle_fc | Parameter bearbeiten / Maximale Anzahl von Formeln, die in einem einzelnen Arbeitsblatt eingegeben werden können | Quote | Standard 0; 0–0; Nicht validiert | Auf der Seite konfigurierbar |
| Bearbeiten_limit_mosheet_Größe | Parameter bearbeiten / Maximale bearbeitbare Datenmenge (MB) | Quote | Standard 100; 0–100 | Auf der Seite konfigurierbar |
| Bearbeiten_limit_mosheet_ansehen | Parameter bearbeiten / Maximale Anzahl separater Ansichten, die ein Benutzer in einem einzelnen Arbeitsblatt erstellen kann (Einheiten) | Quote | Standard 100; 0–100 | Auf der Seite konfigurierbar |
| Bearbeiten_limit_Präsentation_Seite | Parameter bearbeiten / Anzahl der Folien | Quote | Standard 2000; 0–2000 | Auf der Seite konfigurierbar |
| Bearbeiten_limit_Präsentation_Größe | Parameter bearbeiten / Maximale bearbeitbare Datenmenge (MB) | Quote | Standard 100; 0–100 | Auf der Seite konfigurierbar |
| Bearbeiten_limit_rdoc_Größe | Parameter bearbeiten / Maximale bearbeitbare Datenmenge (MB) | Quote | Standard 100; 0–100 | Auf der Seite konfigurierbar |
| Bearbeiten_limit_Tabelle_Kalender_ansehen | Parameter bearbeiten / Maximale Anzahl von Kalenderansichten pro einzelner Datei | Quote | Standard 200; 0–200 | Auf der Seite konfigurierbar |
| Bearbeiten_limit_Tabelle_Anzahl | Parameter bearbeiten / Maximale Anzahl von Datentabellen | Quote | Standard 200; 0–200 | Auf der Seite konfigurierbar |
| Bearbeiten_limit_Tabelle_Gantt_ansehen | Parameter bearbeiten / Maximale Anzahl von Gantt-Ansichten pro einzelner Datei | Quote | Standard 200; 0–200 | Auf der Seite konfigurierbar |
| Bearbeiten_limit_Tabelle_Sperre_ansehen | Parameter bearbeiten / Maximale Anzahl von Sperransichten pro einzelne Datentabelle | Quote | Standard 50; 0–50 | Auf der Seite konfigurierbar |
| Bearbeiten_limit_Tabelle_manuell_Version | Parameter bearbeiten / Anzahl der manuell gespeicherten Versionen | Quote | Standard 10000; 0–10000 | Auf der Seite konfigurierbar |
| Bearbeiten_limit_Tabelle_zusammenführen_Tabelle_referenzieren | Parameter bearbeiten / Maximale Anzahl von Datentabellen, auf die ein einzelnes zusammengeführtes Arbeitsblatt verweisen kann | Quote | Standard 20; 0–20 | Auf der Seite konfigurierbar |
| Bearbeiten_limit_Tabelle_zusammenführen_Tabelle_Zusammenfassung | Parameter bearbeiten / Maximale Anzahl zusammengeführter Arbeitsblätter | Quote | Standard 20; 0–20 | Auf der Seite konfigurierbar |
| Bearbeiten_limit_Tabelle_Persönlich_ansehen | Parameter bearbeiten / Maximale Anzahl persönlicher Ansichten pro einzelner Datentabelle | Quote | Standard 50; 0–50 | Auf der Seite konfigurierbar |
| Bearbeiten_limit_Tabelle_einzeln_Spalte | Parameter bearbeiten / Gesamte Spalten einer einzelnen Datentabelle | Quote | Standard 50; 0–50 | Auf Seite konfigurierbar |
| Bearbeiten_limit_Tabelle_einzeln_Zeile | Parameter bearbeiten / Gesamte Zeilen einer einzelnen Datentabelle | Quote | Standard 20000; 0–20000 | Auf Seite konfigurierbar |
| Bearbeiten_limit_Tabelle_einzeln_ansehen | Parameter bearbeiten / Maximale Anzahl von Ansichten einer einzelnen Datentabelle | Quote | Standard 200; 0–200 | Auf Seite konfigurierbar |
| Bearbeiten_limit_Tabelle_Größe | Parameter bearbeiten | Quote | Standard 100; 0–100 | Auf Seite konfigurierbar |
| exportieren_limit_rdoc_Pixel_Höhe | Exportparameter / Maximale Höhe des exportierten Bildes (px) | Quote | Standard 66000; 0–66000 | Auf Seite konfigurierbar |
| exportieren_Größe_Limit | Exportparameter / Maximale Größe der exportierten Datei (GB) | Quote | Standard 3072; 0–3072 | Auf Seite konfigurierbar |
| Historie_limit_Alle_Zeit | Historienparameter / Aufbewahrungstage der Dateihistorie | Quote | Standard 10000000000000000; 0–10000000000000000; Nicht validiert | Auf Seite konfigurierbar |
| Historie_limit_mosheet_Zelle_Zeit | Historienparameter / Aufbewahrungstage der Tabellenzellenhistorie | Quote | Standard 10000000000000000; 0–10000000000000000; Nicht validiert | Auf Seite konfigurierbar |
| Historie_limit_Zurücksetzen_Nummer | Historienparameter / Anzahl der zuletzt wiederherstellbaren Historieneinträge für eine einzelne Datei | Quote | Standard 2000; 0–2000 | Auf der Seite konfigurierbar |
| Historie_limit_Tabelle_Zelle_Zeit | Historienparameter / Anzahl der Tage zur Aufbewahrung der Historie für Anwendungstabellenzellen | Quote | Standard 10000000000000000; 0–10000000000000000; nicht validiert | Auf der Seite konfigurierbar |
| Historie_limit_Tabelle_Zeile_Zeit | Historienparameter / Anzahl der Tage zur Aufbewahrung der dynamischen Historie für Anwendungstabellenzeilen | Quote | Standard 10000000000000000; 0–10000000000000000; nicht validiert | Auf der Seite konfigurierbar |
| Historie_limit_Version_Nummer | Historienparameter / Anzahl der Versionen (Snapshots), die für eine einzelne Datei gespeichert/wiederhergestellt werden können | Quote | Standard 100; 0–100 | Auf der Seite konfigurierbar |
| importieren_Export_Timeout | Importparameter / Maximaler Importzeitraum (Minuten) | Quote | Standard 10; 0–10 | Auf der Seite konfigurierbar |
| importieren_limit_modoc_Größe | Importparameter / Maximale Dateigröße (MB) | Quote | Standard 300; 0–300 | Auf der Seite konfigurierbar |
| importieren_limit_modoc_Wort | Importparameter / Maximale Anzahl von Zeichen (Zeichen) | Quote | Standard 2000000; 0–2000000 | Auf der Seite konfigurierbar |
| importieren_limit_mosheet_Alle_Tabelle_Zelle | Importparameter / Maximale Anzahl gültiger Zellen in einem Blatt | Quote | Standard 5.000.000; 0–5.000.000 | Auf Seite konfigurierbar |
| importieren_limit_mosheet_Alle_xml_Größe | Importparameter / Gesamtgröße aller XML Dateien im Blatt (MB) | Quote | Standard 300; 0–300 | Auf Seite konfigurierbar |
| importieren_limit_mosheet_konvertiert_Größe | Importparameter / ShimoDocs Datenvolumen (MB) | Quote | Standard 100; 0–100 | Auf Seite konfigurierbar |
| importieren_limit_mosheet_einzeln_Tabelle_Zelle | Importparameter / Maximale Anzahl gültiger Zellen in einem einzelnen Arbeitsblatt | Quote | Standard 2.000.000; 0–2.000.000 | Auf Seite konfigurierbar |
| importieren_limit_mosheet_einzeln_xml_Größe | Importparameter / Maximale Größe einer einzelnen XML Datei im Blatt (MB) | Quote | Standard 20; 0–20 | Auf Seite konfigurierbar |
| importieren_limit_mosheet_Größe | Importparameter / Maximale Dateigröße (MB) | Quote | Standard 300; 0–300 | Auf Seite konfigurierbar |
| importieren_limit_Präsentation_Seite | Importparameter / Maximale Anzahl von Folien (Seiten) | Quote | Standard 2000; 0–2000 | Auf Seite konfigurierbar |
| importieren_limit_Präsentation_Größe | Importparameter / Maximale Dateigröße (MB) | Quote | Standard 100; 0–100 | Auf der Seite konfigurierbar |
| importieren_limit_rdoc_Größe | Importparameter / Maximale Dateigröße (MB) | Quote | Standard 50; 0–50 | Auf der Seite konfigurierbar |
| importieren_limit_rdoc_Wort | Importparameter / Maximale Anzahl von Zeichen (Zeichen) | Quote | Standard 300000; 0–300000 | Auf der Seite konfigurierbar |
| importieren_limit_Tabelle_einzeln_Spalte | Importparameter / Maximale Anzahl effektiver Spalten pro Arbeitsblatt (Spalten) | Quote | Standard 50; 0–50 | Auf der Seite konfigurierbar |
| importieren_limit_Tabelle_einzeln_Zeile | Importparameter / Maximale Anzahl effektiver Zeilen pro Arbeitsblatt (Zeilen) | Quote | Standard 20000; 0–20000 | Auf der Seite konfigurierbar |
| einfügen_Limit | Einfügeparameter / Maximales Datenvolumen pro Einfügen (MB) | Quote | Standard 9; 0–9 | Auf der Seite konfigurierbar |
| einfügen_limit_modoc | Einfügeparameter / Maximale Anzahl von Zeichen pro Einfügen (Zeichen) | Quote | Standard 200000; 0–200000 | Auf der Seite konfigurierbar |
| einfügen_limit_mosheet | Einfügeparameter / Maximale Anzahl von Zellen pro Einfügen (Einheiten) | Quote | Standard 2000000; 0–2000000 | Auf der Seite konfigurierbar |
| einfügen_limit_Präsentation | Einfügeparameter / Maximale Anzahl von Folien, die auf einmal eingefügt werden können | Quote | Standard 200; 0–200 | Auf der Seite konfigurierbar |
| einfügen_limit_rdoc | Einfügeparameter / Maximale Anzahl von Zeichen, die auf einmal eingefügt werden können | Quote | Standard 200000; 0–200000 | Auf der Seite konfigurierbar |
| einfügen_limit_Tabelle | Einfügeparameter / Maximale Anzahl von Zeilen, die auf einmal eingefügt werden können | Quote | Standard 2000; 0–2000 | Auf der Seite konfigurierbar |
| Vorschau_Timeout | Vorschauparameter / Maximale Vorschauzeit (Minuten) | Quote | Standard 10; 0–10 | Auf der Seite konfigurierbar |

## Anhang B: Terminologie und Seitenfeldzuordnung

| **Begriff** | **Bedeutung** |
| --- | --- |
| Konfigurationselementname / Namens-Whitelist | Der eindeutige Name des Konfigurationselements, z. B. rdoc_Benachrichtigung, bearbeiten_limit_mosheet_Größe. |
| Team-ID | Team-Identifikator; geben Sie eine positive Zahl ein, um den Team-übergreifenden Konfigurationsbereich einzugeben. |
| Anwendungsstandard | Der Konfigurationsbereich, wenn die Team-ID leer gelassen wird; in diesem Handbuch als globale Konfiguration bezeichnet. |
| Team-Level-Konfiguration | Überschreibungen der Konfiguration, die nur für die angegebene Team-ID wirksam sind. |
| Systemstandard | Wenn auf diesem Level keine Überschreibung vorhanden ist, wird der integrierte Standardwert des Produkts verwendet. |
| Anwendungsabdeckung / Teamabdeckung | Benutzerdefinierte Konfigurationen existieren auf der aktuellen Ebene und haben Vorrang vor den Werten der oberen Ebene. |
| Funktionsschalter | Schalter- oder Statusparameter. |
| Einzelwertkontingent | Ein Maximalwert und ein optionaler Schalter zur Limitvalidierung. |
| Bereichskontingent | Ein Bereichsparameter, der sowohl Minimal- als auch Maximalwerte umfasst. |
| JSON Konfiguration | Strukturierter Parameter, der gültig bleiben muss JSON; einige Konfigurationselemente werden auf der aktuellen Seite nicht angezeigt. |
| Keine Validierung / Kein Limit | Führt keine Limitvalidierung basierend auf dem eingegebenen Maximum durch. |
