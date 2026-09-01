# Plattformbenutzerverwaltung

[← ShimoDocs Suite Bereitstellungsdokumentation](../../../README.md)

## Funktionsübersicht

Die Systembenutzerverwaltung wird verwendet, um Benutzerkonten im MDP Verwaltungs-Backend zu pflegen, einschließlich der Erstellung von Benutzern, der Bearbeitung von Basisinformationen, dem Zurücksetzen von PASSWORD, der Verwaltung der Zwei-Faktor-Authentifizierung und dem Löschen von Benutzern.

## Zugriff auf die Seite

Nach der Anmeldung mit einem Systemadministrator-Konto wählen Sie **Systembenutzerverwaltung** im linken Navigationsbereich, um die Seite aufzurufen.

Dieses Menü ist nur für bestimmte Systemadministrator-Konten zugänglich. Wenn Sie dieses Menü nicht sehen, wenden Sie sich bitte an Ihren Systemadministrator.

## Benutzer anzeigen

Die Seite zeigt Benutzernamen, BENUTZERNAMEs, Rollen, E-Mail-Adressen, Kontaktinformationen, letzte Anmeldezeiten und Registrierungszeiten an und bietet Übersichtsinfos wie Gesamtanzahl der Benutzer, kürzlich aktive Benutzer und Administrator-Konten.

Sie können alle Benutzer über die Listenseitenansicht anzeigen.

## Neuen Benutzer erstellen

Klicken Sie **Neuen Benutzer erstellen** und füllen Sie die folgenden Informationen aus:

- **Spitzname**: Pflichtfeld, wird für die Anzeige auf der Seite verwendet.
- **USERNAME**: Pflichtfeld, wird zur Anmeldung im System verwendet.
- **E-Mail**: Pflichtfeld, muss eine gültige E-Mail-Adresse angeben.
- **Kontaktinformationen**: Optional.
- **Rolle**: Wählen Sie normalen Benutzer oder Administrator.

Nach der Erstellung generiert das System ein anfängliches PASSWORD. Bitte kopieren Sie es sofort und geben Sie es dem Benutzer auf einem sicheren Weg weiter, da PASSWORD es möglicherweise nach Schließen dieses Fensters nicht erneut angezeigt werden kann.

### Benutzerrollenbeschreibung

- Administrator
  - Kann alle Seiten basierend auf globalen Berechtigungen verwenden
    - ShimoDocs Suite
    - Dokumentenzentrum
    - Systemdienste
- Regulärer Benutzer
  - Seiten-Nutzungsbereich basierend auf globalen Berechtigungen
    - ShimoDocs Suite
    - Dokumentenzentrum
    - Systemdienste (versteckt)

## Benutzerinformationen bearbeiten

Klicken Sie auf die Schaltfläche Bearbeiten rechts neben dem Benutzer, um dessen Spitznamen, E-Mail und Kontaktinformationen zu ändern. USERNAME kann auf dieser Seite nach der Erstellung nicht geändert werden.

## Zurücksetzen PASSWORD

Nach dem Klicken auf die Schaltfläche Zurücksetzen PASSWORD und dem Bestätigen der Aktion wird das System ein neues PASSWORDgenerieren. Das ursprüngliche PASSWORD wird sofort ungültig.

Bitte kopieren und speichern Sie das neue PASSWORDordentlich, liefern Sie es dem entsprechenden Benutzer über einen vertrauenswürdigen Kanal und erinnern Sie den Benutzer daran, sich anzumelden und das PASSWORD so schnell wie möglich zu ändern.

## Zwei-Faktor-Authentifizierung verwalten

- **2FA aktivieren oder deaktivieren**: Verwenden Sie den Schalter in der Zeile des Benutzers und fahren Sie im Bestätigungsfenster fort.
- **2FA zurücksetzen**: Das System generiert einen neuen QR-Code und ein Secret, und die ursprünglichen Verifizierungsinformationen werden ungültig.

Nach dem Zurücksetzen sollten Benutzer Authenticator-Programme wie Authenticator zur erneuten Abtastung und Bindung verwenden. QR-Codes und Secrets sind sensible Anmeldeinformationen und sollten nicht über öffentliche Kanäle übertragen werden. 

2FA binden

Hinzufügen durch Scannen mit Authenticator und Verwendung der dynamischen 6-stelligen 2FA für nachfolgende Anmeldungen

## Benutzer löschen

Nach dem Klicken auf die Löschen-Schaltfläche und dem Bestätigen wird das Benutzerkonto entfernt. Die Löschaktion kann nicht rückgängig gemacht werden, stellen Sie daher sicher, dass das Konto nicht mehr genutzt wird und schließen Sie die notwendigen Daten- und Berechtigungsübergaben ab, bevor Sie fortfahren.

## Häufige Situationen

- **Benutzer kann nicht erstellt werden**: Bitte prüfen Sie, ob das USERNAME doppelt ist, das E-Mail-Format korrekt ist und alle erforderlichen Felder ausgefüllt sind.
- **Benutzer kann sich nicht anmelden**: Überprüfen Sie, ob USERNAME und PASSWORD korrekt sind; setzen Sie ggf. das PASSWORD.
- **zurück, wenn der Benutzer die 2FA-Verifizierung nicht abschließen kann**: Stellen Sie sicher, dass die Systemzeit korrekt ist, oder setzen Sie die 2FA für den Benutzer zurück und binden Sie sie erneut.
- **Benutzermenü nicht sichtbar**: Das aktuelle Konto darf nicht das festgelegte Systemadministrator-Konto sein.
- **Unbeabsichtigtes Löschen eines Benutzers**: Die Löschaktion kann nicht direkt rückgängig gemacht werden; das Konto muss neu erstellt und die entsprechenden Berechtigungen erneut konfiguriert werden.

> Anmeldeinformationen, die beim Erstellen, Zurücksetzen PASSWORDund Zurücksetzen der 2FA generiert werden, sollten umgehend gespeichert und nur dem Kontoinhaber bereitgestellt werden.
