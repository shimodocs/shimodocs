# Transcodierung Ereignissuche

[← ShimoDocs Suite Bereitstellungsdokumentation](../../../README.md)

## Funktionsübersicht 
Die Abfragefunktion für Transkodierungsereignisse wird verwendet, um kürzlich erfolgte Transkodierungsereignisse schnell zu überprüfen in der MDP Backend, das dabei hilft, Probleme während des Transkodierungsprozesses zu lokalisieren und zu beheben. 

Standardmäßig zeigt die Liste die neuesten Transkodierungsereignisse an. 

## Aufgabe_ID-Akquise
Eine Aufgabe_ID wird während Import- und Exportaufgaben generiert

Öffnen Sie den Entwicklermodus des Browsers. Beim Export können Sie die Aufgaben-ID erhalten, indem Sie diese Schnittstelle wie in der Abbildung unten gezeigt überprüfen_

## Nach Aufgabe suchen_id
Geben Sie die Aufgabe ein_Geben Sie die ID in das TaskID-Eingabefeld ein, um die Transcoding-Ereignisse, die mit dieser Aufgabe zusammenhängen, schnell zu filtern.

## Link anzeigen
Wie in der Abbildung unten gezeigt, klicken Sie auf das Symbol "Link anzeigen" in der Zeilenaufzeichnung, um alle Ereignisse im Zusammenhang mit der Aufgabe anzuzeigen_ID, die die Analyse des gesamten Prozesses dieser Transcoding-Aufgabe von Anfang bis Ende erleichtert.

## Fehlerlokalisierung

### gRPC Erfolgreich, Callback nicht empfangen
Wenn gRPC Wird erfolgreich gesendet und eine Antwort wird erfolgreich empfangen, zeigt dies an, dass die Transcoding-Aufgabe an den Transcoding-Dienst übermittelt wurde. In diesem Fall, wenn der Callback aufgrund eines Timeouts des Transcoding-Dienstes nicht rechtzeitig empfangen wird, muss der Transcoding-Dienst untersucht werden.

### Callback empfangen
Wenn Sie sehen können, dass es einen Callback für die Aufgabe gibt_ID, dann wird dies im Allgemeinen als Transcoding-Fehler betrachtet, wie inkompatible Formate oder andere Ausnahmen.
