# Markenanpassung

[← ShimoDocs Suite Bereitstellungsdokumentation](../../README.md)

> [!TIP]
>
> Brand-Anpassung wird verwendet, um die Markenidentität und den Schnittstellenstil von ShimoDocs Suitezu vereinheitlichen. Hier können Sie Ihr Firmenlogo, Browser-Symbole, Tab-Marken-Erweiterungen, Themenfarben, abgerundete Schaltflächen, Seitenzugänge und System-Wasserzeichen einstellen. 
>
> Bei der Verwendung dieser Funktion wird empfohlen, zunächst den wirksamen Anwendungsbereich der Konfiguration zu bestätigen und dann die Konfiguration in der Reihenfolge Markenidentität, Schnittstellenstil, Zugang und Wasserzeichen abzuschließen. 

> Wenn keine Mieter ausgewählt sind, gilt die Konfiguration global für ShimoDocs Suite. Die Priorität für denselben Konfigurationseintrag ist: ** Mieterkonfiguration > Globale Konfiguration**. 

## 1. Brand-Anpassung eingeben 

1. Melden Sie sich bei **MDP Operations-Plattform**. 
2. Oben auswählen **ShimoDocs Suite**. 
3. In der linken Navigationsleiste wählen Sie **Markenanpassung**. 

## 2. Wählen Sie den wirksamen Anwendungsbereich der Konfiguration aus 

Brandanpassung unterstützt globalen, Mandanten- oder Benutzerbereich. Bitte wählen Sie vor der Konfiguration den entsprechenden Bereich basierend auf Ihrem tatsächlichen Bedarf. 

| Konfigurationsbereich | Wie wählen | Effekt |
| --- | --- | --- |
| Globale Konfiguration | Keine Mandanten oder Benutzer auswählen. | Wirksam global für ShimoDocs Suite. |
| Mandantenkonfiguration | Den angegebenen Mandanten auswählen. | Nur für ausgewählte Mandanten wirksam. |
| Benutzerkonfiguration | Den angegebenen Benutzer auswählen. | Nur für ausgewählte Benutzer wirksam. |

Zum Beispiel: Wenn die globale Themenfarbe auf Blau eingestellt ist und die Themenfarbe eines bestimmten Mandanten auf Grün eingestellt ist, verwendet dieser Mandant Grün, während andere Mandanten, die nicht individuell konfiguriert wurden, weiterhin Blau verwenden.

## 3. Markenidentität konfigurieren

### 1. Firmenlogo

Durch die Konfiguration von "Hauptseiten-Firmenlogo ändern" können Sie steuern, ob die Option zum Ändern des Firmenlogos angezeigt wird auf der **Enterprise Management > Unternehmensgrundinformationen** Seite in ShimoDocs Suite.

Nach der Aktivierung können Administratoren das Unternehmenslogo auf der Seite mit den Basisinformationen des Unternehmens ändern.

### 2. Browser-Symbol

Über die Einstellung "Browser-Symbol der Seite ändern" können Sie das Symbol (Icon) ersetzen, das von ShimoDocs Suite auf dem Browser-Tab angezeigt wird.

Nach der Konfiguration können Sie die tatsächliche Anzeige auf dem Browser-Tab sehen.

### 3. Browser-Tab-Marken-Suffix

Durch die Konfiguration "Browser-Tab-Marken-Suffix" können Sie das angezeigte Marken-Suffix einstellen ShimoDocs Suite im Browser-Tab.

Nach der Konfiguration können Sie die Wirkung im Titel des Browser-Tabs sehen.

## 4. Stil der Konfigurationsoberfläche

### 1. Farbthema

Über die Konfiguration "Farbthema" können Sie die Farbe der Hauptschaltflächen, der ausgewählten Zustände und hervorgehobenen Inhalte einheitlich anpassen. ShimoDocs Suite.

Nach Änderung der Farbe können Sie die tatsächliche Anwendung des Farbthemas auf der Seite in der Vorschau sehen.

### 2. Schaltflächenradius

Passen Sie den Radius-Effekt der Schaltflächen über die ShimoDocs Suite "Eckradius-Konfiguration" an.

Nach Anpassung der Werte ändert sich die Form der Schaltflächenecken entsprechend.

## 5. Konfiguration des Seiteneintritts und der Markeninformationen

### 1. Zugriff auf die offizielle Website

Durch die Konfiguration "Hauptseiten-Zugang zur offiziellen Website aktivieren" können Sie steuern, ob der ShimoDocs Zugang zur offiziellen Website auf persönlichen Visitenkarten angezeigt wird.

Nach der Aktivierung können Benutzer den Zugang zur offiziellen Website in ihrem persönlichen Profil sehen.

### 2. „Über“-Eintrag

Über die Einstellung "Hauptseiten-Zugang ‚Über‘ aktivieren" können Sie steuern, ob der ‚Über‘-Eintrag auf persönlichen Profilen angezeigt wird.

Nach der Aktivierung können Benutzer den ‚Über‘-Eintrag in ihrem persönlichen Profil sehen.

### 3. Markeninformationen

Über die Einstellung „Markeninformationen anzeigen“ können Sie steuern, ob Markeninformationen den Benutzern auf relevanten Seiten angezeigt werden.

**Anzeigeeffekt:**

**Effekt bei Verbergen:**

## 6. System-Wasserzeichen konfigurieren

### 1. Mitarbeiter-Wasserzeichen

Durch die Konfiguration von „Systemintegriertes Mitarbeiter-Wasserzeichen aktivieren“ können Sie den Wasserzeicheninhalt steuern, der angezeigt wird, wenn Benutzer Dateien bearbeiten oder in der Vorschau anzeigen.

Der Wasserzeicheninhalt variiert je nachdem, ob der Besucher anonym ist und die Auswahl „Benutzerinformationen anzeigen“ getroffen wurde. 

#### Nicht-anonymer Zugriff 

| Konfigurationsoption | Angezeigter Wasserzeicheninhalt |
| --- | --- |
| Anzeigen/Verbergen | Zeigt das systeminterne Wasserzeichen an, einschließlich grundlegender Autorisierungsinformationen und Benutzerinformationen. |
| Benutzerdefiniert | Anzeige entsprechend dem im Unternehmen eingestellten Mitarbeiter-Wasserzeicheninhalt ShimoDocs Suite Unternehmen. |

#### Anonymer Zugriff

| Konfigurationsoption | Angezeigter Wasserzeicheninhalt |
| --- | --- |
| Anzeigen/Verbergen | Zeigt das systeminterne Wasserzeichen an, einschließlich grundlegender Autorisierungsinformationen und Benutzerinformationen. |
| Benutzerdefiniert | Zeigt für anonyme Benutzer nur den konfigurierten benutzerdefinierten Text an. |

Nach Aktivierung des systemintegrierten Mitarbeiter-Wasserzeichens wird das entsprechende Wasserzeichen dauerhaft angezeigt, wenn Benutzer die Datei bearbeiten oder in der Vorschau anzeigen.

### 2. Wasserzeichen der unteren Editor-Leiste

Über die Konfiguration „Systemintegriertes Wasserzeichen der unteren Editor-Leiste ändern“ können Sie das im Editor unten angezeigte systeminterne Wasserzeichen anpassen.

Nach der Konfiguration können Sie die tatsächliche Anzeigewirkung am unteren Rand des Editors anzeigen.

## 7. Konfigurationsergebnisse überprüfen

Nach Abschluss der Konfiguration wird empfohlen, in der folgenden Reihenfolge zu prüfen:

1. Bestätigen Sie, ob der derzeit ausgewählte Konfigurationsbereich global oder mandantenbezogen ist.
2. Öffnen Sie die ShimoDocs Suite Seite innerhalb des entsprechenden Bereichs.
3. Aktualisieren Sie die Seite und überprüfen Sie das Logo, das Browser-Symbol, den Tab-Namen, die Farbgestaltung des Themas und den Anzeigenstatus des Eintrags.
4. Öffnen Sie die Datei sowohl mit nicht-anonymen als auch anonymen Methoden und bestätigen Sie, dass der Wasserzeicheninhalt wie erwartet angezeigt wird.
5. Wenn der tatsächliche Effekt nicht den Erwartungen entspricht, überprüfen Sie bitte zuerst, ob eine Mandantenkonfiguration mit höherer Priorität vorhanden ist.
