# Benachrichtigungskanäle

[← ShimoDocs Suite Bereitstellungsdokumentation](../../../README.md)

## Funktionsübersicht

Benachrichtigungskanäle werden verwendet, um zentral zu verwalten, wie Systemwarnmeldungen empfangen werden, und ermöglichen es, dass Middleware-Inspektionen und andere Funktionen Fehler- und Wiederherstellungsbenachrichtigungen senden.

Derzeit unterstützte Kanäle umfassen WeCom, DingTalk, Feishu und benutzerdefinierte Webhooks.

## Zugriff auf die Seite

Nach dem Einloggen in die Administrationskonsole wählen Sie **Benachrichtigungskanäle** im linken Navigationsbereich, um die Seite aufzurufen.

Benachrichtigungskanäle sind nur für Administratoren verfügbar. Wenn Sie dieses Menü nicht sehen, wenden Sie sich bitte an den Systemadministrator, um Ihre Kontoberechtigungen zu bestätigen.

## Erstellen eines neuen Benachrichtigungskanals

Klicken Sie **Kanal erstellen**, geben Sie den Kanalnamen ein und wählen Sie den Kanaltyp:

- **WeCom**: Geben Sie den Robot-Webhook-Schlüssel ein.
- **DingTalk**: Geben Sie den vollständigen Webhook ein URLund geben Sie optional das Signatur-Geheimnis gemäß der Roboterkonfiguration ein.
- **Feishu**: Geben Sie den vollständigen Webhook ein URLund geben Sie optional das Signatur-Geheimnis gemäß der Roboterkonfiguration ein.
- **Benutzerdefinierter Webhook**: Geben Sie die Anfrage URL, HTTP Methode und die Body-Vorlage ein.

Bestimmen Sie, ob der Kanal aktiviert werden soll, und klicken Sie dann auf **Speichern**.

## Benutzerdefinierter Webhook

Die Body-Vorlage eines benutzerdefinierten Webhooks unterstützt die folgenden Variablen: 

```text
{{title}}
{{body}}
{{level}}
```

Beispiel der Standardvorlage: 

```json
{"title":"{{title}}","body":"{{body}}","level":"{{level}}"}
```

Wenn das System eine Benachrichtigung sendet, werden die Variablen durch den tatsächlichen Titel, Inhalt und Alarmstufe ersetzt. 

## Testkanal 

Nach dem Speichern klicken Sie **Test** auf der rechten Seite des Kanals. Das System sendet eine Testnachricht, um zu überprüfen, ob die Webhook-Adresse, Signatur und Netzwerkverbindung korrekt sind. 

Es wird empfohlen, sofort nach dem Erstellen oder Ändern eines Kanals einen Test durchzuführen, bevor er an Middleware-Inspektionen oder andere Geschäftsprozesse gebunden wird. 

## Aktivieren, Bearbeiten und Löschen 

- **Aktivieren/Deaktivieren**: Passen Sie den Aktivierungsstatus beim Bearbeiten des Kanals an. Wenn deaktiviert, erhält der Kanal keine Geschäftsbenachrichtigungen. 
- **Bearbeiten**: Sie können den Kanalnamen, den Typ und die Webhook-Konfiguration ändern. 
- **Löschen**: Löschen Sie nicht mehr verwendete Kanäle. Kanäle, die von Middleware-Inspektionen referenziert werden, müssen zuerst entbunden werden, bevor sie gelöscht werden können. 

## Häufige Situationen

- **Testversand fehlgeschlagen**: Bitte überprüfen Sie die Webhook-Adresse, den Key, das Secret, HTTP die Methode und die Netzwerkzugriffsberechtigungen.
- **Speichern fehlgeschlagen**: Bitte stellen Sie sicher, dass alle erforderlichen Felder ausgefüllt sind und das Webhook- URL Format korrekt ist.
- **Geschäftsbenachrichtigungen nicht empfangen**: Bitte bestätigen Sie, dass der Kanal aktiviert ist und auf der entsprechenden Geschäftsseite ausgewählt wurde.
- **Kanal kann nicht gelöscht werden**: Dieser Kanal wird möglicherweise noch von Middleware-Inspektionen verwendet. Bitte entfernen Sie die Zuordnung und speichern Sie zuerst die Inspektionskonfiguration.
- **Benutzerdefiniertes Webhook-Empfangsformat falsch**: Bitte überprüfen Sie, ob die Body-Vorlage den Anforderungen des Zielsystems entspricht.

> Webhook-Adresse und Signatur-Geheimnis sind sensible Informationen. Bitte beschränken Sie den Zugriff und vermeiden Sie öffentliches Teilen über Screenshots, Protokolle oder Chat-Tools.

## Beispiel für die Bedienoberfläche

Die Abbildung unten zeigt die Kanaltypen und das Konfigurationsformular beim Erstellen eines neuen Benachrichtigungskanals.

