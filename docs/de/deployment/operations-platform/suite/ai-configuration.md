# KI-Konfiguration

[← ShimoDocs Suite Bereitstellungsdokumentation](../../README.md)

Die KI-Konfiguration wird verwendet, um eine Verbindung zu ShimoDocs Suite mit den Basismodellen, Bildmodellen, Onlinesuche und Embedding-Diensten. Nach Abschluss der Konfiguration können Funktionen in ShimoDocs Suite wie KI-Konversationen, Inhaltserstellung, Bildverarbeitung und Wissensabruf auf die entsprechenden Dienste zugreifen. 

## 1. Die vier Arten von Fähigkeiten vor der Konfiguration verstehen 

Die Zwecke der vier Konfigurationsarten sind unterschiedlich, und es ist nicht unbedingt erforderlich, alle zu konfigurieren. Bitte wählen Sie basierend auf den ShimoDocs Suite Funktionen, die Sie aktivieren möchten. 

| Konfigurationstyp | Zweck | Erforderlich zur Konfiguration | 
| --- | --- | --- |
| Basismodell | Bearbeitet Konversationen, Schreiben, Zusammenfassungen, Umschreiben, Frage-Antwort und andere Text- oder multimodale Aufgaben | Normalerweise erforderlich bei der Nutzung von KI-Funktionen |
| Bildmodell | Erstellt oder bearbeitet Bilder | Nur erforderlich bei der Nutzung von Bildgenerierungs- oder Bearbeitungsfunktionen |
| Onlinesuche | Ruft Informationen von externen Suchdiensten ab, um Modellreferenzen zu ergänzen | Nur erforderlich, wenn Online-Abruffunktionen genutzt werden |
| Einbettung | Konvertiert Text in Vektoren für Wissensdatenbank-Abruf, semantische Suche und ähnliche Funktionen | Nur erforderlich, wenn Wissensabruf- oder Vektorsuchfunktionen verwendet werden |

> [!TIP]
>
> Online-Suche ist in der Regel ein unabhängiger Suchdienst und nicht dasselbe wie die in das Basismodell integrierten Online-Funktionen. 

## 2. Zugriff auf AI-Konfiguration 

1. Melden Sie sich bei der **MDP Operations-Plattform**. 
2. Oben auswählen **ShimoDocs Suite**.
3. In der linken Seitenleiste auswählen **Mandantenverwaltung**.
4. Finden Sie die **KI-Konfiguration** Karte.
5. Klicken Sie auf die Karte, um zur Seite "AI-Modell- und Suchkonfiguration" zu gelangen.

## 3. Wählen Sie zuerst den Modellanbieter aus, mit dem Sie verbinden möchten

Bitte bestätigen Sie zuerst den Modellservice, den Sie nutzen möchten, und gehen Sie dann zum entsprechenden Abschnitt zur Konfiguration.

| Modelltyp | Dienstanbieter |
| --- | --- |
| Basismodell | Anbieter, die mit dem OpenAI Responses-Protokoll kompatibel sind |
| Bildmodell | Anbieter, die mit dem OpenAI Image-Protokoll kompatibel sind |
| Internet-Suchmaschinen-Modell | Unterstützt derzeit nur Volcengine |
| Embedding-Modell | Anbieter, die mit dem OpenAI-Embedding-Protokoll kompatibel sind |

## 4. KI-Modellkonfigurationen

Dieser Abschnitt dient zur Konfiguration von GPT-bezogenen Diensten. Bitte lassen Sie von der Technik bestätigen, ob die offiziellen OpenAI-Dienste, Azure OpenAI, Proxy-Dienste oder andere kompatible Schnittstellen verwendet werden sollen, da die Anforderungsadresse und die Modell-ID je nach Verbindungsmethode variieren können.

### 4.1 Basis-Modell

Das Basis-Modell wird für Dialoge, Inhaltserstellung, Zusammenfassungen, Umschreibungen und multimodale Verständnisfunktionen verwendet.

#### Anbieter-Konfiguration

| Konfigurationselement | Beispielwert | Beschreibung |
| --- | --- | --- |
| Anbieter | OpenAI auswählen (oder kompatibel mit dem OpenAI Responses-Protokoll) | OpenAI auswählen (oder kompatibel mit dem OpenAI Responses-Protokoll) |
| Anfrage URL / Basis URL | https://myai.com/v1 | Wählen Sie Ihre eigene mit dem OpenAI Responses-Protokoll kompatible AI-Gateway-Adresse |
| API Schlüssel | sk-I••••haTO | Die API Dem AI-Gateway zugewiesener Schlüssel |
| Standardmodell | gpt-5.5 | Modell, das mit dem OpenAI Responses-Protokoll kompatibel ist |

> [!TIP]
>
> Der hier konfigurierte Modellanbieter muss den Streaming-Modus unterstützen. ShimoDocs AI wird als Client immer `stream: true` senden, wenn beim Modellanbieter angefragt wird. Wenn der Modellanbieter den Streaming-Modus nicht unterstützt, schlägt die Anfrage fehl.

#### Modellkonfiguration

| Konfigurationselement | Beispielwert | Entwicklungshinweise |
| --- | --- | --- |
| Status | Aktiviert | Muss aktiviert werden |
| Modell-ID | gpt-5.5 | Gültige Modell-ID |
| Modellname | gpt-5.5 | Sollte mit der Modell-ID übereinstimmen |
| Kontextfenster | 1024000 | Entsprechend den tatsächlichen Bedingungen ausfüllen |
| Texteingabe | Aktiviert | Muss aktiviert werden |
| Bildeingabe | Aktiviert | Muss aktiviert werden |

### 4.2 Bildmodell

Bildmodelle werden für die Bilderzeugung oder Bildbearbeitung verwendet. Bitte füllen Sie die Modelle und Funktionen aus, die von der aktuellen Version tatsächlich unterstützt werden.

| Konfigurationselement | Beispielwert | Engineering-Notizen |
| --- | --- | --- |
| Status | Aktiviert | Muss aktiviert werden |
| Anbieter | OpenAI (oder kompatibel mit dem OpenAI-Bildprotokoll) | OpenAI (oder kompatibel mit dem OpenAI-Bildprotokoll) |
| Modellname | gpt-image-2 | Muss mit dem OpenAI-Bildprotokoll kompatibel sein |
| Anfrage URL / Basis URL | https://myai.com/v1 | Wählen Sie Ihre eigene AI-Gateway-Adresse, die mit dem OpenAI-Responses-Protokoll kompatibel ist |
| API Schlüssel | sk-I••••haTO | Die API Dem AI-Gateway zugewiesener Schlüssel |
| Funktionen | Bildgenerierung, Bildbearbeitung | Behalten Sie die Standardwerte Bildgenerierung, Bildbearbeitung bei |

> [!TIP]
>
> Unterstützt derzeit nur OpenAI Image API Protokoll

### 4.3 Internet-Suchmodell

Netzwerksuche unterstützt derzeit nur die Konfiguration von Volcengine.

| Konfigurationselement | Beispielwert | Engineering-Notizen |
| --- | --- | --- |
| Status | Aktiviert | Nach Bedarf aktivieren. Wenn aktiviert, müssen Sie die Werte aller anderen Einträge in der aktuellen Konfigurationsgruppe ausfüllen |
| Dienstanbieter | Volcengine | Unterstützt derzeit nur Volcengine |
| API Endpunkt | https://open.feedcoopapi.com/search_api/web_search | Standard-Netzwerksuchadresse von Volcengine |
| API Schlüssel | mCmh•••••••• | Vom Netzwerk-Suchdienstanbieter beziehen |
| Timeout-Einstellung | 120s | Wenn eine einzelne Netzwerksuchanfrage diese Zeit überschreitet, schlägt sie fehl. Es wird empfohlen, 120s beizubehalten |

### 4.4 Einbettungsmodell

Einbettungsmodelle werden für Wissensdatenbank-Abfragen und semantische Suche verwendet. Die Modell-ID und Dimension müssen mit dem tatsächlichen Vektorausgang übereinstimmen.

| Konfigurationselement | Beispielwert | Entwicklungshinweise |
| --- | --- | --- |
| Dienstanbieter | OpenAI (oder kompatibel mit OpenAI-Einbettungsmodell) | OpenAI (oder kompatibel mit OpenAI-Einbettungsmodell) |
| Basis URL | https://myai.com/v1 | Wählen Sie Ihre eigene AI-Gateway-Adresse, die mit dem OpenAI-Responses-Protokoll kompatibel ist |
| API Schlüssel | ak-•••••••• | Vom Anbieter des Einbettungsmodells beziehen |
| Embedding-Modell | qwen3-embedding:4b | Modell-ID |
| Dimension | Ganzzahlwert | Die Dimension hängt mit dem Einbettungsmodell zusammen; Sie können den Anbieter für Dimensionsparameter konsultieren |

| Entwicklungsbestätigungs-Elemente | Inhalt |
| --- | --- |
| Unterstützte Einbettungsmodelle | OpenAI (oder kompatibel mit OpenAI-Einbettungsmodell) |
| Empfohlene Vektordimension | Bezieht sich auf das Einbettungsmodell |
| Ist es notwendig, Vektordaten für verschiedene Dimensionen neu zu erstellen | Ja |

> [!TIP]
>
> Unterstützt derzeit nur OpenAI-Einbettung API Protokoll

### 4.5 GPT Überprüfung der Konfigurationsfertigstellung

| Überprüfungspunkt | Erwartetes Ergebnis | Tatsächliches Ergebnis |
| --- | --- | --- |
| Grundmodell-Konversation | Geben Sie eine einfache Frage in der KI-Sitzung ein | Modell gibt das entsprechende Ergebnis zurück |
| Verarbeitung langer Texte | Ausgabe langer Texte | Modell gibt das entsprechende Ergebnis basierend auf dem Inhalt des langen Textes zurück |
| Bildeingabe oder Bildverarbeitung | Geben Sie ein Bild zur Erkennung ein | Kann den erkannten Inhalt zurückgeben |
| Internetsuche | Fordern Sie es auf, Informationen zu Flug- oder Zugtickets abzurufen | Kann Ergebnisse zu Flug- oder Zugtickets zurückgeben |
| Einbettungsvektorisierung | Verwenden Sie Schlüsselwörter für KI-Suchen auf der gesamten Website | Kann den erwarteten passenden Inhalt zurückgeben |

## 5. Geschäftsbedeutung jedes Konfigurationselements

Dieser Abschnitt bietet eine einheitliche Erklärung des Zwecks jedes Konfigurationselements auf der Seite. Bei der Erstkonfiguration können Sie gemäß der zuvor erwähnten Vorlage des Anbieters ausfüllen und anschließend zu diesem Abschnitt zurückkehren, um zu bestätigen, ob jedes Feld den tatsächlichen Geschäftsanforderungen entspricht.

### 5.1 Konfigurationselemente des Basis-Modellanbieters

| Konfigurationselement | Geschäftsbedeutung | Übliche Auswirkungen falscher Eingaben | Erforderlich |
| --- | --- | --- | --- |
| Anbieter | Gibt dem System an, welche Modellanpassungsmethode verwendet werden soll. Selbst wenn zwei Dienste mit ähnlichen Schnittstellen kompatibel sind, kann die Anbieteroption das Anfrageformat, die Authentifizierungsmethode und die Art und Weise, wie Ergebnisse geparst werden, bestimmen. | Kann fehlschlagen zu speichern, Anfrageformat stimmt nicht überein oder Antwort kann nicht geparst werden. | Ja |
| Anfrage URL / Basis URL | Die Eintragsadresse ShimoDocs Suite wird beim Senden von Anfragen an den Modellservice aufgerufen. | Kann nicht mit dem Modell verbinden, wenn die Adresse falsch ist; die Schnittstelle kann als nicht existent erscheinen, wenn die Pfadebene falsch ist. | Ja |
| API Schlüssel | Anmeldeinformationen, die vom Modellservice verwendet werden, um den Aufrufer zu identifizieren und Berechtigungen zu überprüfen. | Weist normalerweise auf Authentifizierungsfehler hin, wenn sie falsch, abgelaufen oder Berechtigungen unzureichend sind. | Ja |
| Standardmodell | Das Modell, das das System vorrangig aufruft, wenn Geschäftsprozesse kein Modell explizit angeben. | Einige KI-Funktionen sind möglicherweise nicht verfügbar, wenn es nicht eingestellt ist oder auf ein nicht verfügbares Modell gesetzt wurde. | Ja |

### 5.2 Grundlegende Modellkonfigurationselemente

| Konfigurationselement | Geschäftsbedeutung | Übliche Auswirkungen falscher Eingaben | Erforderlich |
| --- | --- | --- | --- |
| Status | Steuert, ob das Modell aufgerufen werden darf von ShimoDocs Suite. Nach dem Schließen kann die Konfiguration beibehalten werden, aber das Geschäft kann das Modell normalerweise nicht weiter verwenden. | Selbst wenn die Modellkonfiguration korrekt ist, kann das Geschäft es immer noch als nicht verfügbar anzeigen, wenn der Status geschlossen ist. | Ja |
| Modell-ID | Der Modellname oder eindeutige Bezeichner, der von der Modellservice-Schnittstelle erkannt wird. | Weist normalerweise darauf hin, dass das Modell nicht existiert, wenn es mit dem Servernamen nicht übereinstimmt. | Ja |
| Modellname | Der Name, der Administratoren in der Betriebsplattform angezeigt wird, um verschiedene Modelle zu unterscheiden. | Wenn der Name doppelt oder unklar ist, ist es leicht, das falsche Modell auszuwählen; ob es an tatsächlichen Anfragen teilnimmt, wird von der Technik bestätigt. | Ja |
| Kontextfenster | Die Gesamtmenge an Informationen, die ein Modell in einer einzelnen Anfrage verarbeiten kann, wirkt sich in der Regel auf die Länge des Eingabetextes, die Verlaufshistorie von Unterhaltungen und den Ausgabebereich aus. | Wenn dies größer als die tatsächliche Kapazität des Modells eingestellt wird, kann dies zu Anfragefehlern führen; wenn es zu klein eingestellt wird, kann dies dazu führen, dass Inhalte abgeschnitten werden oder nicht übermittelt werden können. | Ja |
| Texteingabe | Gibt an, ob das Modell Textinhalte akzeptieren kann. | Wenn es fälschlicherweise auf Aus deaktiviert ist, können textbezogene Funktionen dieses Modell möglicherweise nicht auswählen oder aufrufen. | Ja |
| Bildeingabe | Gibt an, ob das Basismodell hochgeladene Bilder des Benutzers verstehen kann; dies ist eine multimodale Eingabefähigkeit und ist nicht dasselbe wie die Generierung von Bildern. | Wenn es für ein Modell aktiviert wird, das keine Bilder unterstützt, kann dies zu Anfragefehlern führen; ist es deaktiviert, steht die Bildverständnisfunktion nicht zur Verfügung. | Ja |

### 5.3 Bildmodell-Konfigurationsoptionen

| Konfigurationselement | Geschäftsbedeutung | Häufige Auswirkungen falscher Einstellungen | Erforderlich |
| --- | --- | --- | --- |
| Status | Steuert, ob das Bildmodell durch Bildgenerierungs- oder Bearbeitungsfunktionen aufgerufen werden kann. | Wenn der Status deaktiviert ist, können verwandte Bildfunktionen das Modell nicht verwenden. | Ja |
| Dienstanbieter | Bestimmt die Schnittstellenanpassungsmethode, die für Bildanfragen verwendet wird. | Eine falsche Auswahl kann zu nicht kompatiblen Anfrageparametern oder Rückgabeformaten führen. | Ja |
| Modellname / Modell-ID | Gibt das tatsächlich aufzurufende Bildmodell an. Ob dieses Feld der Anzeigename oder die Anfrage-ID ist, muss von der Technikabteilung geklärt werden. | Wenn der Name nicht mit dem Server übereinstimmt, kann dies darauf hinweisen, dass das Modell nicht existiert. | Ja |
| Basis URL | Die Dienstadresse, an die Bildgenerierungs- oder Bearbeitungsanfragen gesendet werden. | Wenn die Adresse oder der Pfad falsch ist, kann der Bilddienst nicht aufgerufen werden. | Ja |
| API Schlüssel | Das Authentifizierungsnachweis, der verwendet wird, um den Bilddienst aufzurufen. | Fehler, Ablauf oder fehlende Berechtigung führen zu Authentifizierungsfehlern. | Ja |
| Funktionen | Erklärt die vom Modell unterstützten Bildfunktionen, wie z. B. Bilderzeugung, Bildbearbeitung usw. | Wenn eine vom Modell nicht unterstützte Funktion konfiguriert wird, kann der Geschäftseintrag sichtbar sein, aber der Aufruf schlägt fehl. | Ja |

Hinweis: Derzeit wird nur das OpenAI-Bild API Protokoll unterstützt

### 5.4 Internet-Suchkonfiguration

| Konfigurationselement | Geschäftsbedeutung | Allgemeine Auswirkungen bei falscher Einstellung | Erforderlich |
| --- | --- | --- | --- |
| Status | Steuert, ob ShimoDocs Suite den aktuellen Suchdienst aufrufen kann. | Wenn der Status ausgeschaltet ist, kann das Modell weiterhin verfügbar sein, aber es kann keine Internetsuchergebnisse abrufen. | Nein |
| Dienstanbieter | Gibt den Typ des zu verwendenden Suchdienstes und dessen Schnittstellenanpassungsmethode an. | Bei falscher Wahl können Anfragen und Ergebnisparsing inkompatibel sein. | Nein |
| Schnittstellenadresse | Der Dienstendpunkt, der beim Start einer Suchanfrage aufgerufen wird. | Wenn die Adresse falsch ist, kann die Internetfunktion zeitüberschreiten oder die Verbindung fehlschlagen. | Nein |
| API Schlüssel | Authentifizierungsnachweis, der vom Suchdienst verwendet wird. | Wenn falsch oder unzureichende Berechtigungen, werden Suchanfragen abgelehnt. | Nein |
| Timeout-Einstellung | Maximale Wartezeit für eine einzelne Suche; wenn überschritten, stoppt das System das Warten und behandelt es als Fehler oder kein Ergebnis. | Zu kurze Einstellung führt zu häufigen Zeitüberschreitungen; zu lange Einstellung erhöht die Wartezeit des Benutzers. | Nein |

### 5.5 Einbettungskonfiguration

Einbettungsmodelle müssen nicht unbedingt aktiviert werden, aber wenn sie nicht aktiviert sind, kann der Dokumentinhalt nicht vektorisiert werden, und das System kann somit keine Fragen in Bezug auf die Wissensdatenbank des Benutzers bearbeiten.

| Konfigurationselemente | Geschäftsbedeutung | Häufige Folgen falscher Eingaben | Ist es obligatorisch |
| --- | --- | --- | --- |
| Basis URL | Die Serviceadresse, die an die Textvektorisierungsanfrage gesendet wird. Wenn die Adresse falsch ist, können Vektordaten nicht erstellt oder aktualisiert werden. Nein |
| API Schlüssel | Authentifizierungsdaten, die vom Embedding-Dienst verwendet werden. Die Vektorisierung schlägt aufgrund von Fehlern, abgelaufenen Daten oder fehlender Berechtigung fehl. Nein |
| Embedding-Modell | Die Modell-ID, die tatsächlich für die Umwandlung von Text in Vektoren verantwortlich ist. Vektoren können nicht generiert werden, wenn das Modell nicht existiert oder nicht übereinstimmt. Nein |
| Dimension | Die endgültige Vektorlänge, die von jeder Textzeile generiert wird, muss mit dem tatsächlichen Ausgabe- und Vektorspeicher des Modells übereinstimmen. | Wenn die Dimensionen inkonsistent sind, ist das Schreiben oder Abrufen normalerweise nicht möglich; nach einer Dimensionänderung müssen möglicherweise vorhandene Vektoren neu generiert werden. Nein |

Hinweis: Derzeit wird nur das OpenAI Embedding API Protokoll 

## 6. Empfohlene Konfigurationsreihenfolge 

Um Duplikationen zu reduzieren, wird empfohlen, gemäß der folgenden Reihenfolge zu konfigurieren: 

1. Bestätigen Sie zunächst, welche KI-Funktionen aktiviert werden müssen in ShimoDocs Suite. 
2. Wählen Sie ein Basismodell, das den Protokollanforderungen entspricht. 
3. Konfigurieren Sie den Anbieter und fügen Sie mindestens ein Basismodell hinzu. 
4. Legen Sie das validierte verfügbare Modell als Standardmodell fest. 
5. Konfigurieren Sie Bildmodelle nach geschäftlichen Anforderungen. 
6. Konfigurieren Sie die vernetzte Suche nach geschäftlichen Anforderungen. 
7. Wenn Wissensdatenbanken oder semantische Suche verwendet werden, konfigurieren Sie dann das Embedding. 
8. Nach dem Speichern überprüfen Sie jede Funktion separat; beurteilen Sie den Konfigurationserfolg nicht nur anhand der Anzeige „Aktivieren“ auf der Seite. 

## 7. Effektive Konfigurationsregeln 
| Probleme, die eine Bestätigung durch das Engineering erfordern | Inhalt |
| --- | --- |
| Tritt die Konfiguration sofort nach dem Speichern in Kraft | Sie tritt nicht sofort in Kraft; Sie müssen 1-2 Minuten warten |
| Müssen Sie den Dienst neu starten | Es ist nicht nötig, den Dienst neu zu starten |
| Tritt die neue Konfiguration auf einer geöffneten Seite in Kraft | Sie müssen die aktuelle Seite aktualisieren |
| Prioritätsauswahl zwischen mehreren Modellen | Nicht unterstützt |
| Wechselt es automatisch, wenn das Standardmodell nicht verfügbar ist? | Nicht unterstützt |

## 8. Häufige Problembehebung 

| Phänomen | Häufige Ursachen | Fehlerbehebungsmethode |
| --- | --- | --- |
| Verbindungsmodell-Dienstfehlfunktion | Anomalien bei Anforderungsadresse, Netzwerk, Zertifikat oder Portkonfiguration | Überprüfen Sie die Dienstadresse, DNSPort, Zertifikat und Firewall-Richtlinien | 
| Meldung Authentifizierungsfehler | API Schlüssel fehlerhaft, abgelaufen oder unzureichende Berechtigungen | Bestätigen Sie, dass der API Schlüssel korrekt ist und Zugriff auf das Zielmodell oder den Dienst hat | 
| Meldung Modell existiert nicht | Modell-ID stimmt nicht mit dem Server-End-Namen überein | Bestätigen Sie die vollständige Modell-ID und prüfen Sie Groß-/Kleinschreibung und Versionssuffix | 
| Text verfügbar, aber Bilder nicht verfügbar | Modell unterstützt keine Bildeingabe oder der Bildeingabe-Schalter ist nicht aktiviert | Überprüfen Sie die Modellfähigkeiten und den Eingabeschalter | 
| Bilder-Funktionseintrag vorhanden, aber Aufruf schlägt fehl | Funktionen stimmen nicht mit den tatsächlichen Fähigkeiten des Modells überein | Überprüfen Sie die vom Bildmodell unterstützten Generierungs- und Bearbeitungsfunktionen | 
| Häufige Timeouts bei Online-Suche | Suchdienst ist langsam, Netzwerk instabil oder Timeout-Einstellung zu kurz | Überprüfen Sie die Netzwerklatenz, Leistung des Dienstes und Timeout-Einstellungen | 
| Fehler beim Einbetten Schreibvorgang | Ausgabedimensionen stimmen nicht mit der Vektor-Speicherkonfiguration überein | Überprüfen Sie die tatsächlichen Ausgabedimensionen des Modells und die Speicher-Konfiguration | 

## Fragen & Antworten

1. Wie überprüft man, ob die Konfiguration wirksam ist?

Nachdem Sie die Konfiguration abgeschlossen haben, können Sie in der Seitenleiste des Editors eine AI-Sitzung öffnen, um zu prüfen, ob die Funktion funktioniert:

- Nachrichten sollten normal beantwortet werden
- Wenn ein Bildmodell konfiguriert ist, können Sie einen Befehl wie 'Erstelle ein Xxx-Bild' senden und überprüfen, ob der Befehl korrekt ausgeführt wird
- Wenn die Online-Suche konfiguriert ist, können Sie einen Befehl wie 'Suche online nach dem heutigen Wetter in Peking' senden und prüfen, ob das Ergebnis den Erwartungen entspricht

2. Unterstützt es die /chat/completions-Schnittstelle?

Derzeit nicht unterstützt. Momentan wird nur das OpenAI Responses API Protokoll unterstützt. Es ist bekannt, dass offizielle APIs wie Deepseek / Xiaomi-Mimo diesen Protokolltyp unterstützen. Lokale Bereitstellungslösungen wie vLLM und Ollama unterstützen ebenfalls das Responses-Protokoll.
