# Übersicht der Betriebsplattform

[← ShimoDocs Suite Bereitstellungsdokumentation](../README.md)

## Funktionsübersicht

- **ShimoDocs Suite**: Wird verwendet zur Verwaltung von Berechtigungen, Mandanten, Benutzern, Branding und KI-Konfigurationen im Zusammenhang mit ShimoDocs Suite.
- **Systemdienste**: Wird für allgemeine Betriebs- und Wartungsaufgaben wie globale Konfiguration, Clusterverwaltung, Log-Anzeige, Funktionsprüfung, Abfrage von Problemen, Dokumentenreparatur und **System-Upgrades verwendet**.

> **Hinweis**: Die angezeigte spezifische Funktionalität hängt von der aktuellen Bereitstellungsversion und den aktivierten Funktionen ab.

## Anmeldung an der Operations-Plattform

Greifen Sie in Ihrem Browser auf die folgende Adresse zu:
> **Browser-Anforderungen**: Bitte verwenden Sie Google Chrome Version 111 oder höher, um auf die Operations-Plattform zuzugreifen. Es wird empfohlen, zunächst auf die neueste stabile Version zu aktualisieren.

```text
http(s)://<OPERATIONS_PLATFORM IP OR_DOMAIN_NAME>/mdp/user/login
```

Geben Sie das Administratorkonto ein und PASSWORD, klicken Sie dann auf „Anmelden“.

## Die Operations-Plattform-Startseite kennenlernen

Nach der Anmeldung können Sie über das Menü auf der linken Seite der Seite auf die entsprechenden Verwaltungsfunktionen zugreifen. Das angezeigte Menü hängt von den Produkten und Versionen ab, die in der aktuellen Umgebung bereitgestellt und autorisiert sind.

## Zurücksetzen des Administrators PASSWORD Bei Vergessen

Wenn der Administrator der Operations-Plattform PASSWORD vergessen wurde, können Sie sich beim Bereitstellungsknoten anmelden und den folgenden Befehl ausführen, um ihn zurückzusetzen.﻿

```bash
kubectl exec -it $(kubectl get pods -l app=mdp -o jsonpath='{.items[0].metadata.name}') -- reset-admin-password Aa1234567.
```

Das obige Beispiel setzt den PASSWORD zu `Aa1234567.`zurück. Im tatsächlichen Betrieb ersetzen Sie bitte das Beispiel PASSWORD am Ende des Befehls durch einen neuen PASSWORD , der den Sicherheitsanforderungen entspricht.

Nach Abschluss des Zurücksetzens kehren Sie zur Anmeldeseite zurück, melden sich mit dem neuen PASSWORDan und bestätigen, dass auf das Menü normal zugegriffen werden kann.
