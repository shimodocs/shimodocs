# Mandantenverwaltung

[← ShimoDocs Suite Bereitstellungsdokumentation](../../README.md)

> [!TIP]
>
> Tenant Management wird für die zentrale Verwaltung von Mandanten verwendet in ShimoDocs Suite.
> Administratoren können hier die Anzahl der Mandanten und die Sitzungsnutzung einsehen, Drittanbieter-Integrationsberechtigungen erhalten, KI-Konfigurationen verwalten sowie Mandanten erstellen, bearbeiten, aktivieren oder deaktivieren.
>

## 1. Zugriff auf das Tenant Management

1. Melden Sie sich bei **MDP Operations-Plattform**.
2. Auswählen **ShimoDocs Suite** oben.
3. Auswählen **Mandantenverwaltung** in der linken Navigationsleiste.

## 2. Die Tenant Management Seite verstehen

Die Gesamtinformationen des aktuellen Systems werden oben auf der Seite angezeigt:

| Region | Beschreibung |
| --- | --- |
| API-KEY | Anzeigen und kopieren Sie die `AppID` und `AppSecret` für die Drittanbieter-Integration erforderlichen Informationen. |
| KI-Einstellungen | Überprüfen Sie, ob KI-Funktionen aktiviert sind, und greifen Sie auf KI-Modell- und Suchkonfigurationen zu. |
| Gesamtanzahl der Mandanten | Die Anzahl der im aktuellen System erstellten Mandanten. |
| Aktivierte Mandanten | Die Anzahl der derzeit aktivierten Mandanten. |
| Sitzungsnutzung | Anzahl der verwendeten Sitzungen, Gesamtanzahl der Sitzungen im System und Nutzungsquote. |

Die untenstehende Mandantenliste zeigt den Mandantennamen, die Aktivierungszeit, den Mandantenadministrator, die Sitzungsnutzung und den aktuellen Status an. In der Spalte „Aktionen“ können Sie den entsprechenden Mandanten bearbeiten oder deaktivieren.

## 3. Ansicht API Schlüssel

`AppID` und `AppSecret` werden zur Authentifizierung verwendet, wenn ShimoDocs Suite integriert sich mit Drittanbietersystemen.

### Operationsschritte

1. Finden Sie die **API-KEY** Karte auf der Seite zur Mieterverwaltung.
2. Klicken Sie auf das Kopiersymbol.
3. Das System kopiert die aktuelle Umgebung. `AppID` und `AppSecret`.
4. Geben Sie die Anmeldeinformationen in die entsprechende Drittanbieter-Integrationskonfiguration ein.

> `AppSecret` ist eine sensible Anmeldeinformation. Bitte bewahren Sie sie sicher auf und schreiben Sie sie nicht in öffentliche Dokumente, Chat-Protokolle oder öffentlich zugängliche Code-Repositories.

## 4. Verwaltung der KI-Konfiguration

Die KI-Konfigurationskarte zeigt den aktuellen Aktivierungsstatus der KI-Funktionalität an.

Nach dem Klicken auf die KI-Konfigurationskarte können Sie auf der Seite "KI-Modell- und Suchkonfiguration" die folgenden Inhalte ansehen oder bearbeiten: 

### 1. Grundlegende Modellkonfiguration

Wird zur Konfiguration allgemeiner großer Sprachmodelle (LLMs) und ihrer verfügbaren Modelle verwendet. Auf dieser Seite können Sie Informationen wie Anbieter, Anforderungsschlüssel, URL, API Standardmodell, Modell-ID, Kontextrahmen und Eingabefähigkeiten einsehen.

### 2. Bildmodellkonfiguration

Wird zur Konfiguration von Bildgenerierungs- oder Bildbearbeitungsmodellen verwendet. Auf der Seite können Sie den Anbieter, Modellnamen, Basisschlüssel URL, API und die unterstützten Bildfunktionen einsehen.

### 3. Konfiguration der Netzwerksuchmaschine

Wird zur Konfiguration des KI-Netzwerksuchdienstes verwendet. Auf der Seite können Sie den Dienstanbieter, die Schnittstellenadresse, API Schlüssel und Zeitüberschreitung einsehen.

### 4. Konfiguration des Embedding-Anbieters

Wird zur Konfiguration des Text-Vektorisierungsdienstes verwendet. Auf der Seite können Sie den Basisschlüssel, URL, API Embedding-Modell und Vektordimensionen einsehen.

> Bevor Sie die KI-Konfiguration ändern, bestätigen Sie bitte zuerst die Dienstadresse, API Schlüssel, Modell-ID und Fähigkeitsparameter sind alle korrekt. Nach der Änderung wird empfohlen, eine kleine Menge Testinhalt zu verwenden, um zu überprüfen, ob der Modellaufruf ordnungsgemäß funktioniert.

### 5. Verwendung von KI in ShimoDocs Suite
Nachdem die Konfiguration abgeschlossen ist, können Sie die KI-Funktionen innerhalb von ShimoDocs Suite.

## 5. Verwaltung bestehender Mandanten

In der Mieterübersicht können Sie die Grundinformationen und die Sitzplatznutzung jedes Mieters einsehen.

### Mieter bearbeiten

1. Finden Sie den Mieter, der angepasst werden muss.
2. Klicken Sie in der Spalte 'Aktionen' auf 'Bearbeiten'.
3. Ändern Sie die Mieterinformationen oder die Anzahl der Sitzplätze gemäß den Anweisungen auf der Seite.
4. Speichern Sie die Änderungen und kehren Sie zur Liste zurück, um zu bestätigen, dass die Informationen aktualisiert wurden.

### Mieter deaktivieren oder wiederherstellen

- Für derzeit aktivierte Mieter können Sie in der Spalte ‚Aktionen‘ auf ‚Deaktivieren‘ klicken.
- Um einen deaktivierten Mieter wiederherzustellen, aktivieren Sie ihn in den Aktionspunkten des entsprechenden Mieters erneut.

> Das Deaktivieren eines Mieters wirkt sich auf den normalen Zugriff auf diesen Mieter aus. Bitte bestätigen Sie, dass der Ziel-Mieter korrekt ist, bevor Sie fortfahren, und planen Sie den Vorgang entsprechend der tatsächlichen Nutzung.

## 6. Aktivierung eines neuen Mieters

Überprüfen Sie vor der Aktivierung des Mieters zunächst die Sitzplatzauslastung oben auf der Seite, um zu bestätigen, dass noch Sitze zur Zuweisung verfügbar sind.

### Betriebsschritte

1. Klicken Sie oben rechts auf der Seite auf „Neuen Mieter aktivieren“.
2. Geben Sie den Namen ein, der zur Identifizierung dieses Mieters in ‚Mietername‘ verwendet wird.
3. Bestätigen Sie die vom System generierte E-Mail des Tenant-Administrators. Nachdem der Tenant erfolgreich erstellt wurde, speichern Sie bitte dieses Administratorkonto und das anfängliche PASSWORD umgehend.
4. Überprüfen Sie "Zuweisbare Plätze", um die maximale Anzahl der derzeit dem neuen Tenant zugewiesenen Plätze zu verstehen.
5. Geben Sie die diesem Tenant zugewiesene Platzanzahl in "Dem Tenant zugewiesene Plätze" ein.
6. Nachdem Sie bestätigt haben, dass die Informationen korrekt sind, klicken Sie auf "Speichern".

### Beschreibung des Platzfelds

| Feld | Beschreibung |
| --- | --- |
| Zuweisbare Plätze | Die maximale Anzahl an Plätzen, die derzeit vom System dem Tenant zugewiesen werden kann. |
| Dem Tenant zugewiesene Plätze | Die Gesamtzahl der diesem Tenant zugewiesenen Plätze. Diese Zahl darf nicht kleiner sein als die bereits vom Tenant genutzten Plätze. |
| Vom Tenant genutzte Plätze | Die Anzahl der aktiven Unternehmenskunden-Mitglieder in diesem Tenant. Jedes aktive Mitglied belegt einen Platz. |

> Eine bestimmte Anzahl von Plätzen muss bei der Erstellung eines Mandanten zugewiesen werden. Die Sitzanzahl kann später entsprechend der tatsächlichen Nutzung angepasst werden.

## 7. Erste Anmeldung und Änderung der Initialen PASSWORD

Nachdem der Mandant erfolgreich erstellt wurde, melden Sie sich bitte an ShimoDocs Suite mit dem vom System generierten Standardkonto oder dem Administratorkonto an und ändern Sie sofort die Initialen PASSWORD.

### 1. Öffnen Sie ShimoDocs Suite

Zugriff im Browser:

```text
http://<ACCESS_DOMAIN>/
```

Wenn HTTPS ist bereits konfiguriert, bitte besuchen Sie: 

```text
https://<ACCESS_DOMAIN>/
```

### 2. Melden Sie sich bei ShimoDocs Suite

Geben Sie das Administratorkonto und die initialen PASSWORD ein, die bei der Einrichtung des Mandanten erstellt wurden, um die Anmeldung abzuschließen.

### 3. Ändern Sie die Initialen PASSWORD

Nach der ersten Anmeldung folgen Sie bitte den Seitenanweisungen oder den Kontosicherheitseinstellungen, um die Initialen zu ändern PASSWORD. Sobald die neuen PASSWORD ist eingestellt, bitte bewahren Sie es ordnungsgemäß auf.

