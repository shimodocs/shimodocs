# Clusterverwaltung

[← ShimoDocs Suite Bereitstellungsdokumentation](../../../README.md)

## 1. Funktionsübersicht

Das Cluster-Management-Modul ist eine Konsole in der MDP Operations-Plattform, die mit dem Kunden interagiert Kubernetes Cluster, die auf drei Szenarien abzielen: tägliche Inspektionen, Notfall-Fehlerbehebung und Ressourcenänderungen. Das Ziel dieses Moduls ist es, es dem im Dienst befindlichen Betriebspersonal zu ermöglichen, gängige Fehlerbehebungs- und Betriebsvorgänge durchzuführen, ohne häufig zur nativen Umgebung zu wechseln. `kubectl`.

Hauptfunktionen:

- Cluster-Übersicht: Knotenstatus, Anwendungsstatus
- Workload-Management: Anzeigen, Neustarten, Ändern der Replikazahlen, Ändern der Container-Ressourcen und Anzeigen YAML für Deployment, StatefulSet, DaemonSet, Pod, Job und CronJob
- Konfigurationsverwaltung: Anzeige von ConfigMap, HorizontalPodAutoscaler (HPA)
- Netzwerkressourcen: Anzeige von Service, Ingress
- Pod-Ebene Diagnosen: Echtzeitprotokolle, Absturzprotokolle, K8s Ereignisse, Web-Terminal, YAML Anzeige

### 1.1 Anwendbare Benutzer

| Rolle        | Häufige Vorgänge                                      |
| ----------- | ---------------------------------------------------- |
| Betriebsdienst | Anzeige von Knoten- und Pod-Anomalien, Abfrage von Protokollen, Anzeige von Ereignissen |
| Vor-Ort-Support | Anzeigen des Status von Deployment-Replikas, Image-Version, Ressourcenanforderung/-begrenzung         |
| Fehlernotfall | Deployment oder DaemonSet neu starten, Replikazahl anpassen, anpassen CPU/Speicher |
| Kapazitätsplanung | Anzeigen HPA aktuellen Replikazahl und obere/untere Grenzen                             |

### 1.2 Nicht empfohlene Operationen in diesem Modul

Löschen NAMESPACE, Erzwingendes Vereinigen von Pods, Ändern von Secret oder RBAC Ressourcen und andere sensible Operationen sind in diesem Modul nicht verfügbar und müssen über native `kubectl` oder relevante Änderungstools ausgeführt werden. Clusterübergreifende Batch-Operationen sind nicht verfügbar; jede Operation betrifft nur den aktuell ausgewählten Cluster und NAMESPACE. Zum Herunterladen großer Protokolldateien auf einmal wird empfohlen, das Web-Terminal anstelle eines Streaming-Protokoll-Popups zu verwenden.

---

## 2. Anmeldung und Navigation

Linkes Menü: **Betriebsverwaltung → Clusterverwaltung**.

Nach dem Betreten wird das **Deployments** Menü auf der linken Seite standardmäßig ausgewählt. Das NAMESPACE Standardmäßig wird das erste im aktuellen Cluster ausgewählt, eine benutzerdefinierte Auswahl des Clusters und NAMESPACE wird unterstützt.

---

## 3. Workloads

### 3.1 Deployments
**Schritte**: Ziel-Deployment finden → Auf das Stiftsymbol oben rechts klicken → Bearbeitungspopup erscheint → Neue Werte eingeben → Änderungen bestätigen.

Felder, die im Popup zur Änderung unterstützt werden: 

- Anzahl der Replikate, Mindestwert 0, muss eine ganze Zahl sein 
- CPU Anfrage / Limit pro Container, Einheit ist "Core", kann eingegeben werden `1` oder `1000m` 
- Speicheranfrage / Limit pro Container, Einheit ist Mi, kann eingegeben werden `512` 

Nach der Einreichung wird ein Rolling-Rebuild ausgelöst. Nicht aufgeführte Felder (Image, Env, Probes usw.) werden nicht geändert. 

#### 3.1.1 Deployment-Neustart 
Schritte: Ziel-Deployment finden → Auf das kreisförmige Pfeilsymbol oben rechts klicken → Bestätigen, dass das Popup erscheint → Cluster / NAMESPACE / Lastname prüfen → Neustart bestätigen. 

Das Bestätigungs-Popup besagt deutlich: "Ein Neustart führt zum Wiederaufbau der Pods und Services können kurzzeitig unterbrochen werden." Ein Neustart wird die Pods auf allen Knoten gleichzeitig wiederaufbauen. 

### 3.2 Pods
**Operationsschritte**: Gehe zu Pods im linken Menü → Der untere Bereich listet alle Pods im aktuellen NAMESPACE, unterstützt die Suche nach Namespace, POD_NAME, Pod-IP und NODE_IP. 

Dies YAML ist nur zur Ansicht.

### 3.3 Jobs und CronJobs

#### Jobs
**Schritte**: Gehen Sie im linken Menü zu Jobs → Die Tabelle listet alle Jobs im aktuellen Bereich auf. NAMESPACE.

Kann nach Namespaces und Name durchsucht werden.

#### CronJobs
**Schritte**: Gehen Sie im linken Menü zu CronJobs → Die Tabelle listet alle CronJobs unter dem aktuellen Bereich auf. NAMESPACE.

Kann nach Namespaces und Name durchsucht werden. 
Klicken Sie **** **** um die Untertabelle der Pods anzuzeigen, die allen von diesem CronJob ausgelösten Jobs entsprechen. 

### 3.4 DaemonSets 
**Operationsschritte**: Gehen Sie im linken Menü zu DaemonSets. 

Kann nach Namespaces und Workload-Name durchsucht werden.
Unterstützte Operationen:

- Ändern: CPU /Speicher kann geändert werden, die Anzahl der Replikate kann nicht geändert werden.
- Neustart: Pods auf allen Knoten gleichzeitig neu erstellen.
- YAML: Nur Ansicht.

### 3.5 StatefulSets
**Operationsschritte**: Gehen Sie im linken Menü zu StatefulSets → Tabellenansicht.

Die Anzahl der Replikate, CPU/Speicher, Neustarts oder Pod-Listen von StatefulSets können nicht geändert werden. Notwendige Änderungen sollten mit dem nativen Tool vorgenommen werden `kubectl` (siehe Anhang B).

---

## 4. Konfiguration

### 4.1 ConfigMaps
**Schritte**: Gehen Sie im linken Menü zu ConfigMaps → Die Tabelle listet alle ConfigMaps im aktuellen Bereich auf. NAMESPACE.
[Cluster Management] unterstützt keine Bearbeitung von Schlüssel-Wert-Paaren. Für Änderungen gehen Sie bitte ins Konfigurationscenter.

### 4.2 HPA
**Operationsschritte**: Gehen Sie im linken Menü zu HPA → Die Tabelle listet alle HPAs unter dem aktuellen Bereich auf. NAMESPACE.

Nur Ansicht. Um die HPA min- und max-Werte zu ändern, verwenden Sie bitte das native Tool. `kubectl`.

---

## 5. Netzwerk

### 5.1 Dienste
**Schritte**: Verwenden Sie das linke Menü, um zu Dienste zu gehen → Eine Tabelle listet alle Dienste im aktuellen Bereich auf. NAMESPACE.

Nur Ansicht. Um Änderungen vorzunehmen, bearbeiten Sie diese bitte über die globale mdp-Konfiguration. 

### 5.2 Ingresses
**Schritte**: Gehe im linken Menü zu Ingresses → Die Tabelle listet alle Ingresses unter der aktuellen NAMESPACE. 

Ansicht auf. Um Änderungen vorzunehmen, bitte über die globale MDP-Konfiguration ändern.

---

## 6. Allgemeine Operationen

### 6.1 Fehlerbehebung bei Pod-Problemen

1. Verwende das obere Dropdown-Menü, um zum entsprechenden Cluster zu wechseln und NAMESPACE
2. Gehe im linken Menü zu Pods
3. Filtern nach POD_NAME oder IP
4. Achte auf das Feld Phase oben auf der Karte, Priorität `Failed` und `Pending`
5. Die Bedingung, die dem ausgegrauten Gesundheitsindikator entspricht, ist der Problempunkt
6. Klicke auf das Symbol "Events" am Ende der Zeile, um die Ursache zu finden
7. Verwende "Logs", um die Echtzeitausgabe zu sehen / "Crash Logs", um die letzte Container-Ausgabe zu sehen

### 6.2 Deployment neu starten

1. Gehe im linken Menü zu Deployments
2. Finde das Ziel-Deployment
3. Klicken Sie auf das kreisförmige Pfeilsymbol oben rechts
4. Bestätigen Sie das Popup, indem Sie das Cluster / NAMESPACE / Workload-Namen überprüfen → Neustart bestätigen
5. Beobachten Sie die Fortschrittsleiste des Pod-Replikastatus unten auf der Karte, um den Aufbaufortschritt zu beurteilen

### 6.3 Deployment-Replikas zur Überprüfung reduzieren

1. Betreten Sie das entsprechende Deployment
2. Klicken Sie auf das Stiftsymbol „Bearbeiten"
3. Geben Sie den neuen Wert für die Anzahl der Replikas ein (kann zum Debuggen auf 0 gesetzt werden) 
4. Anpassen CPU / Speicher nach Bedarf (optional) 
5. Änderungen bestätigen und auf Rolling Update warten 

Bevor die Anzahl der Replikas reduziert wird, wird empfohlen, mit SRE Kollegen abzuklären, ob der Zielwert den Online-Verkehr beeinflusst. 

### 6.4 ConfigMap ändern 

Die Plattform unterstützt das Bearbeiten von ConfigMap-Schlüsselwert-Paaren in Cluster Management - Konfiguration - ConfigMap nicht. Bitte gehen Sie zum Konfigurationszentrum. 

--- 

## 9. Häufig gestellte Fragen 

**F1: Die obige Übersicht zeigt, dass die Anwendungsbetriebsrate nicht 100% beträgt.** 

Das bedeutet, dass sich Pods unter dem aktuellen befinden NAMESPACE die sich nicht im Bereit-Zustand befinden (einschließlich Ausstehend, CrashLoopBackOff, Fehler usw.). Bitte gehen Sie zum Menü Pods auf der linken Seite, filtern Sie nach POD_NAME oder IP und überprüfen Sie die Ereignisse und Protokolle jedes nicht bereit-Pods. 

**F2: Das Popup ist leer, nachdem Sie auf 'Deployment ändern' geklickt haben.** 

Es gibt drei häufige Gründe: Netzwerk-Jitter, zu viele `managedFields` im Ressourcenobjekt oder Server API Ausnahmen. Bitte deaktivieren Sie zuerst den Wiederholungsversuch; wenn es immer noch leer ist, kontaktieren Sie SRE und geben Sie den Cluster-/Namespace-/Workload-Namen zur Fehlerbehebung an. 

**F3: Das YAML Popup-Inhalt ist sehr groß.** 

Normales Phänomen. K8s Ressourcenobjekte tragen standardmäßig viele Metadaten und Bedingungen, wobei sich der Hauptinhalt im `spec` Abschnitt konzentriert. 

**F4: Keine Ausgabe im Log-Popup.** 

Der Container sendet möglicherweise keine Logs an stdout/stderr, bitte überprüfen Sie die Richtlinie zur Logausgabe der Anwendung. Wenn der Container abgestürzt ist, verwenden Sie das Symbol „Crash-Log“, um die Ausgabe der vorherigen Instanz abzurufen. 

**F5: Änderung der Replikanzahl oder Ressourcen hat keine Wirkung.** 

Die Plattform führt einen Strategischen Merge-Patch aus und K8s tritt innerhalb weniger Sekunden in den Reconcile-Prozess ein. Wenn sich innerhalb von 30 Sekunden nichts ändert, gehen Sie bitte zurück zur nativen `kubectl describe deployment` , um die Ereignisse zu überprüfen. 

**F6: Kann StatefulSets, ConfigMaps, HPA, Services, Ingresses nicht bearbeiten.** 

Die Plattform erlaubt nur die Ansicht dieser Ressourcen. Änderungen sollten über die globale MDP-Konfiguration vorgenommen werden, und nur Services und Ingresses werden unterstützt. 

--- 

--- 

## Anhang A: Schlüssel kubectl Befehle, die auf dieser Plattform verwendet werden 

Die folgenden Befehle werden verwendet, um direkt auf dem Host oder Wartungsterminal auszuführen, als alternativer Weg, wenn Funktionen dieses Moduls nicht abgedeckt sind. 

```bash
# View
kubectl get  statefulset <name> -n <ns>
kubectl get deployment <name> -n <ns>

# Restart STS / deployment
kubectl rollout restart statefulset/<name> -n <ns>
kubectl rollout restart deployment/<name> -n <ns>

# View the complete Ingress rule chain
kubectl describe ingress <name> -n <ns>
```

`kubectl describe deployment <name> -n <ns>` können verwendet werden, um den Fortschritt der von der Plattform nach einer Änderung ausgegebenen Abstimmung zu prüfen.

Vorsichtsmaßnahmen:
Das Ändern von Ressourcen, die verwaltet werden von MDP wie Deployment, ConfigMap, Ingress, StatefulSet usw. über kubectl sollte vermieden werden. Der korrekte Weg, um zu operieren, ist die Verwendung der MDP Backend-Konfiguration.

## Anhang B: Glossar der Begriffe

| Begriff               | Erklärung                                           |
| --------------- | ---------------------------------------------------- |
| Cluster         | Ziel K8s Cluster, konfiguriert und ausgegeben, wenn MDP startet                              |
| Namespace       | K8s NAMESPACE, verwendet zur geschäftlichen oder Umgebungs-Isolation                                   |
| Workload        | Workload, bezieht sich allgemein auf Deployment, StatefulSet, DaemonSet, Job, CronJob |
| Pod             | Die kleinste Planungseinheit in K8s, die 1 bis N Container enthält                              |
| HPA             | HorizontalPodAutoscaler, metrikbasierte horizontale Skalierung                  |
| Request / Limit | Container-Ressourcenvorbelegung / Limit, die Plattform unterstützt die Änderung beider |
| Patch           | Teilaktualisierung, diese Plattform verwendet Strategic Merge Patch                     |
| STS             | Abkürzung für StatefulSet                                       |
| DS              | Abkürzung für DaemonSet                                         |
